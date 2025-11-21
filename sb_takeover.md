# Supabase Takeover Strategy - Technical Implementation

[← Back to Overview](./README.md) | [Git Workflow →](./git_setup.md)

---

## Complete Migration Guide

This document provides the step-by-step technical implementation for migrating from Supabase Cloud to a self-hosted Supabase instance.

### Prerequisites (run once)
- Install the latest Supabase CLI: `npm install -g supabase` (or use brew on macOS: `brew install supabase/tap/supabase`)
- Log in to both Supabase accounts (old and new) in your terminal:
  ```
  supabase login   # with old account access token first
  # Then switch accounts later with another `supabase login` when needed
  ```
- Clone your existing Next.js repo (the one that already has the frontend + possibly partial Supabase config):
  ```
  git clone <your-repo-url> my-project
  cd my-project
  ```

### Step 1: Pull all backend resources from the old project into your repo
This pulls schema (tables, RLS, functions, triggers), Edge Functions, and makes everything version-controlled.

```bash
# 1. Initialize Supabase folder structure if it doesn't exist yet
supabase init

# 2. Link to the OLD project (you need the project ref from dashboard URL: https://app.supabase.com/project/<PROJECT_REF>)
supabase link --project-ref <OLD_PROJECT_REF>

# 3. Pull the full remote schema (including auth/storage changes if any)
supabase db pull
# This creates a migration file like supabase/migrations/<timestamp>_remote_schema.sql with everything

# 4. Pull Edge Functions (if you have any in supabase/functions/)
supabase functions download --project-ref <OLD_PROJECT_REF>

# 5. Pull secrets / env vars (e.g. third-party API keys, SMTP, etc.)
supabase secrets list --project-ref <OLD_PROJECT_REF> > old_secrets.txt
# Manually copy the ones you need and later set them on the new project
```

Commit everything:
```bash
git add supabase/
git commit -m "chore: pull full Supabase schema and functions from old project"
git push
```

### Step 2: Export all database data (including auth users)
```bash
# Get the old project's DB connection string (from Dashboard > Settings > Database > Connection string (click "URI"))
# Or use the password version

# Dump schema + data (excludes Supabase internal stuff)
supabase db dump --linked -f full_dump_with_data.sql --data-only --use-copy

# If you want schema + data in one file:
supabase db dump --linked -f full_dump.sql
```

This file can be huge — keep it out of git (add to `.gitignore`).

### Step 3: Export Storage files (media / uploaded objects)
Supabase does **not** have a CLI command for this yet, so use the official JS script (run from your repo root):

```bash
npm install @supabase/supabase-js
```

Create `migrate-storage.js`:
```js
// migrate-storage.js
const { createClient } = require('@supabase/supabase-js');

const OLD_URL = 'https://<OLD_PROJECT_REF>.supabase.co';
const OLD_SERVICE_KEY = '<old-service-role-key>';  // Dashboard > Settings > API > service_role
const NEW_URL = 'https://<NEW_PROJECT_REF>.supabase.co';  // You can create new project first
const NEW_SERVICE_KEY = '<new-service-role-key>';

const oldClient = createClient(OLD_URL, OLD_SERVICE_KEY);
const newClient = createClient(NEW_URL, NEW_SERVICE_KEY);

async function migrateStorage() {
  const { data: buckets } = await oldClient.storage.listBuckets();
  for (const bucket of buckets) {
    await newClient.storage.createBucket(bucket.name, { public: bucket.public });
    const { data: objects } = await oldClient.storage.from(bucket.name).list('', { limit: 1000, offset: 0 });
    async function downloadAndUpload(path = '') {
      const { data } = await oldClient.storage.from(bucket.name).list(path, { limit: 1000 });
      for (const item of data) {
        if (item.name === '.') continue;
        const fullPath = path ? `${path}/${item.name}` : item.name;
        if (item.id === null) { // it's a folder
          await downloadAndUpload(fullPath);
        } else {
          const { data: file } = await oldClient.storage.from(bucket.name).download(fullPath);
          await newClient.storage.from(bucket.name).upload(fullPath, file, { upsert: true });
          console.log(`Copied ${fullPath}`);
        }
      }
    }
    await downloadAndUpload();
  }
  console.log('Storage migration complete!');
}

migrateStorage().catch(console.error);
```

Run it **after** you create the new project:
```bash
node migrate-storage.js
```

### Step 4: Create & set up the new Supabase project (on your account)
```bash
# Log in with your new/personal account if needed
supabase login   # use your access token

# Create the new project via dashboard (CLI cannot create projects yet)
# Go to https://app.supabase.com → New project → note the new PROJECT_REF
```

Then link your repo to it:
```bash
supabase link --project-ref <NEW_PROJECT_REF>
```

### Step 5: Deploy the backend to the new project
```bash
# Push schema + functions
supabase db push          # applies all migrations (including the big remote_schema one)
supabase functions deploy --project-ref <NEW_PROJECT_REF>  # if you have functions

# Re-set secrets on the new project
supabase secrets set KEY1=value1 KEY2=value2 --project-ref <NEW_PROJECT_REF>
# Or from the old_secrets.txt you exported earlier

# Restore data
supabase db reset --linked   # wipes new DB first (safe on fresh project)
psql $(supabase db url --project-ref <NEW_PROJECT_REF>) < full_dump_with_data.sql
# Or if you dumped everything in one file:
psql $(supabase db url --project-ref <NEW_PROJECT_REF>) < full_dump.sql
```

### Step 6: Deploy the Next.js frontend to a new Vercel project
```bash
# Install Vercel CLI if you don't have it
npm i -g vercel

# Log in to Vercel (your account)
vercel login

# Create & deploy a fresh Vercel project (not importing the old one)
vercel   # it will ask questions → choose "Create a new project"

# Update env vars on Vercel (Dashboard > Project Settings > Environment Variables)
# Add:
NEXT_PUBLIC_SUPABASE_URL=https://<NEW_PROJECT_REF>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<new-anon-public-key>
# Plus any other vars you had
```

Deploy:
```bash
git push origin main   # if you made changes
vercel --prod          # force fresh deploy
```

### Final checklist
- Update any hard-coded old project refs in code (search/replace)
- Test auth signup/login (auth users were migrated via data dump)
- Test storage uploads (buckets recreated by script, objects copied)
- Test Edge Functions
- Point your domain/custom domain to the new Vercel project

You now have a completely independent copy under your control, fully CLI-driven, and everything (schema, functions, secrets) is version-controlled in git. Let me know which step you want to dive deeper into!

---

## Navigation

[← Back to Overview](./README.md) | [Git Workflow →](./git_setup.md)