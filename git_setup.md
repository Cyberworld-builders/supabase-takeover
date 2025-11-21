# Git Workflow & Setup Guide

[← Back to Overview](./README.md) | [← Takeover Strategy](./sb_takeover.md)

---

## Git and GitHub CLI Installation

Here's a straightforward, up-to-date (November 2025) guide to install **Git** and the **GitHub CLI (gh)** on your system and authenticate with GitHub — everything from the terminal.

### 1. Install Git

#### macOS (best ways)
```bash
# Option 1: Homebrew (recommended if you already have brew)
brew install git

# Option 2: Xcode Command Line Tools (no brew needed)
xcode-select --install   # then Git is included
```

#### Windows (best ways)
```powershell
# Option 1: Winget (built into Windows 10/11)
winget install --id Git.Git -e --source winget

# Option 2: Chocolatey (if you use it)
choco install git

# Option 3: Download installer from https://git-scm.com/download/win (choose "Git Bash" included)
```

#### Linux (Ubuntu/Debian-based)
```bash
sudo apt update && sudo apt install git -y
```

#### Linux (Fedora/RHEL-based)
```bash
sudo dnf install git   # or sudo yum install git on older
```

Verify installation (works on all platforms):
```bash
git --version
# Should show something like git version 2.46.x or higher
```

### 2. Install GitHub CLI (gh)

#### macOS
```bash
brew install gh
```

#### Windows
```powershell
winget install --id GitHub.cli
# or with Chocolatey
choco install gh
```

#### Linux (Ubuntu/Debian)
```bash
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```

#### Or the super-short way (works on most Linux/macOS):
```bash
# Homebrew or apt/dnf will already have it, or use the official one-liner:
curl -fsSL https://github.com/cli/cli/releases/download/v2.60.1/gh_2.60.1_linux_amd64.deb -o gh.deb && sudo dpkg -i gh.deb   # adjust version if needed
```

Verify:
```bash
gh --version
# Should show gh version 2.60.x or newer
```

### 3. Authenticate with GitHub

You only need to do this once per machine.

#### Option A: Authenticate gh CLI (recommended — also sets up Git credential helper automatically)
```bash
gh auth login
```
It will ask you a few questions:
- GitHub.com (or GitHub Enterprise if you use that)
- HTTPS or SSH → choose **HTTPS** first (easier)
- Login with browser or token → choose **Login with a web browser**
- It opens your browser, you click Authorize GitHub CLI
- Done! You’re logged in.

Bonus: gh also configures Git to use itself as the credential helper, so normal `git push/pull` will just work without passwords.

#### Option B: Manual SSH setup (if you prefer SSH URLs)
```bash
# Generate a new SSH key (or skip if you already have one)
ssh-keygen -t ed25519 -C "your-email@example.com"

# Start the ssh-agent and add your key
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy the public key to clipboard (macOS)
pbcopy < ~/.ssh/id_ed25519.pub
# Windows (PowerShell)
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard
# Linux
cat ~/.ssh/id_ed25519.pub   # then copy manually

# Add the SSH key in GitHub: Settings → SSH and GPG keys → New SSH key → paste it
```

Then test:
```bash
ssh -T git@github.com
# You should see: Hi username! You've successfully authenticated...
```

#### Option C: If you only want HTTPS with personal access token (classic way)
```bash
gh auth login   # choose "Paste an authentication token"
# Or create one manually at https://github.com/settings/tokens → generate new token (classic) with repo/workflow scopes
```

### Quick test that everything works
```bash
# Clone something
gh repo clone cli/cli

# Or create a test repo and push
mkdir test-repo && cd test-repo
git init
echo "# test" > README.md
git add .
git commit -m "first commit"
gh repo create test-repo --public --source=.
git push -u origin main
```

You're all set! From now on `git` and `gh` will just work with your GitHub account without asking for credentials every time.

Let me know which OS you're on if you hit any snag — happy to give the exact commands for your setup.

---

## Navigation

[← Back to Overview](./README.md) | [← Takeover Strategy](./sb_takeover.md)