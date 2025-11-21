# Supabase Takeover Strategy

> **Export and migrate your Supabase project to a new account**

This repository contains the comprehensive strategy and step-by-step guide for exporting an existing Supabase project and deploying it to a different Supabase account. Perfect for transferring projects between organizations, clients, or personal accounts.

## 🎯 Purpose

The **Supabase Takeover** strategy enables you to:

- ✅ **Export complete projects** from one Supabase account
- ✅ **Deploy to a new account** with all schema, data, and configuration
- ✅ **Version control** your entire database schema, functions, and settings
- ✅ **Transfer between organizations** (agency to client, personal to team, etc.)
- ✅ **Maintain full functionality** including RLS policies, functions, and auth
- ✅ **CLI-driven workflow** for repeatable, reliable migrations

## 📚 Documentation

### Core Documents

- **[Supabase Takeover Strategy](./sb_takeover.md)** - Complete technical implementation guide
  - Exporting from existing Supabase account
  - Step-by-step migration process
  - Deploying to new Supabase account
  - Schema and data transfer
  - Testing and validation

- **[Git Workflow & Setup](./git_setup.md)** - Version control best practices
  - Repository structure
  - Git workflow for database changes
  - CLI-driven development
  - Team collaboration patterns
  - Deployment strategies

## 🚀 Quick Start

### Prerequisites

- Supabase CLI (`brew install supabase/tap/supabase`)
- Access to your source Supabase account/project
- Access to your destination Supabase account
- Git repository for version control

### Migration Overview

1. **Extract** - Pull schema, functions, and config from source project
2. **Version Control** - Commit everything to git for safety
3. **Create New Project** - Set up fresh project in destination account
4. **Deploy** - Push schema and functions to new project
5. **Migrate Data** - Transfer data using pg_dump/restore
6. **Switch** - Update your applications to point to new project

See [sb_takeover.md](./sb_takeover.md) for detailed steps.

## 🏗️ What Gets Migrated

### Database & Schema

- **Database Schema** - All tables, columns, types, extensions
- **Row Level Security (RLS)** - All policies and rules
- **Functions** - PostgreSQL functions and triggers
- **Indexes** - All database indexes for performance
- **Extensions** - PostgreSQL extensions (uuid, pgcrypto, etc.)

### Application Layer

- **Edge Functions** - Deno serverless functions
- **Storage Configuration** - Buckets and access policies
- **Auth Settings** - User tables and authentication config
- **Environment Variables** - Secrets and API keys

### Migration Flow

```
┌─────────────────────┐
│  Source Account     │
│  (Old Project)      │
└──────────┬──────────┘
           │
           │ supabase db pull
           │ supabase functions download
           ↓
┌─────────────────────┐
│   Git Repository    │
│  (Version Control)  │
└──────────┬──────────┘
           │
           │ supabase db push
           │ supabase functions deploy
           ↓
┌─────────────────────┐
│ Destination Account │
│   (New Project)     │
└─────────────────────┘
```

## 💡 Why This Matters

### Common Use Cases

**Agency to Client Handoff**
- Develop project in your agency account
- Transfer complete project to client's account
- Clean ownership transfer with all data and config

**Organizational Changes**
- Moving from personal to team account
- Merging projects across accounts
- Separating projects after company splits

**Account Consolidation**
- Multiple projects across different accounts
- Consolidate into single organization account
- Better project management and billing

### Benefits

**Complete Migration**
- All schema, policies, and functions transferred
- No data loss or manual recreation
- Maintain all existing functionality

**Version Controlled**
- Everything saved in git before migration
- Easy rollback if needed
- Audit trail of all changes

**Repeatable Process**
- CLI-driven workflow
- Document every step
- Repeat for multiple projects

## 🛠️ Tools Used

- **[Supabase CLI](https://supabase.com/docs/guides/cli)** - Project export and deployment
- **[Git](https://git-scm.com/)** - Version control for all migrations
- **[GitHub CLI](https://cli.github.com/)** - Repository management
- **[pg_dump/pg_restore](https://www.postgresql.org/docs/current/app-pgdump.html)** - Data migration utilities

## 📖 Additional Resources

### Official Supabase Docs

- [Supabase CLI Reference](https://supabase.com/docs/reference/cli)
- [Database Migrations](https://supabase.com/docs/guides/cli/local-development)
- [Managing Projects](https://supabase.com/docs/guides/cli/managing-projects)

### Community Resources

- [Supabase GitHub](https://github.com/supabase/supabase)
- [Supabase Discord](https://discord.supabase.com/)
- [CLI Documentation](https://github.com/supabase/cli)

## 🎯 Use Cases

This strategy is ideal for:

- **Agency Project Handoffs** - Transfer completed projects to client accounts
- **Account Consolidation** - Merge projects from multiple accounts
- **Organizational Changes** - Move projects between personal/team accounts
- **Client Onboarding** - Set up identical environments for new clients
- **Backup & Recovery** - Create versioned backups of entire projects
- **Multi-tenant Setups** - Deploy same schema to multiple accounts

## ⚠️ Considerations

**Before migrating, consider:**

- **Data Migration** - Large databases may take time to export/import
- **Downtime** - Plan for brief downtime during the switch
- **API Keys** - All applications need new project URLs and keys
- **Auth Users** - User passwords cannot be migrated (users must reset)
- **Storage Files** - Large file storage may need separate migration
- **Testing** - Thoroughly test new project before switching production traffic

## 🚦 Status

- ✅ Strategy documented
- ✅ Git workflow defined
- 🔄 Implementation in progress
- ⏳ Production deployment pending

## 📝 Contributing

This is a living document. As we learn more through implementation, we'll update the strategy and add:

- Migration scripts
- Deployment automation
- Monitoring setup
- Backup strategies
- Performance optimization guides

## 📞 Support

Questions or need help? Open an issue or reach out to the team.

---

**Last Updated**: November 2024  
**Status**: Active Development  
**License**: Internal Use

