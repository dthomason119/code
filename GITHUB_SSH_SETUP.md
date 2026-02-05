# GitHub SSH Key Setup Procedure

## Overview
This document provides step-by-step instructions for adding your SSH public key to GitHub.com to enable secure authentication for Git operations.

---

## Prerequisites
- GitHub account (create one at https://github.com/signup if needed)
- SSH key pair already generated (see files: `github_rsa` and `github_rsa.pub`)
- Access to your GitHub account

---

## Part 1: Locate Your SSH Public Key

### Step 1: View Your Public Key
Your public key has been generated and saved to:
```
/Users/demontethomason/code/github_rsa.pub
```

### Step 2: Copy the Public Key Content
Run this command in your terminal to display and copy the public key:

```bash
cat /Users/demontethomason/code/github_rsa.pub
```

Or copy it directly to clipboard (macOS):
```bash
cat /Users/demontethomason/code/github_rsa.pub | pbcopy
```

**IMPORTANT**: Only copy the **PUBLIC** key (`github_rsa.pub`), NEVER the private key (`github_rsa`).

---

## Part 2: Add SSH Key to GitHub

### Step 1: Log into GitHub
1. Open your web browser
2. Navigate to https://github.com
3. Click **Sign In** (top right)
4. Enter your username/email and password
5. Complete 2FA if enabled

### Step 2: Navigate to SSH Settings
1. Click your **profile picture** (top right corner)
2. Select **Settings** from dropdown menu
3. In the left sidebar, click **SSH and GPG keys**
4. Click the green **"New SSH key"** button

### Step 3: Add Your Public Key
1. **Title**: Enter a descriptive name (e.g., "MacBook Air - 2026" or "Personal Laptop")
2. **Key Type**: Select **"Authentication Key"** (default)
3. **Key**: Paste your entire public key content
   - Should start with `ssh-rsa AAAAB3NzaC1yc2E...`
   - Should end with `...== demontethomason@github`
4. Click **"Add SSH key"**
5. Confirm with your GitHub password if prompted

### Step 4: Verify the Key Was Added
- You should see your new SSH key listed with:
  - The title you provided
  - Key fingerprint
  - Date added
  - "Never used" status (until first use)

---

## Part 3: Configure Local Git to Use SSH Key

### Step 1: Add SSH Key to SSH Agent
Start the SSH agent:
```bash
eval "$(ssh-agent -s)"
```

### Step 2: Add Private Key to SSH Agent
Add your private key to the agent:
```bash
ssh-add /Users/demontethomason/code/github_rsa
```

### Step 3: Configure SSH Config File (Optional but Recommended)
Create or edit `~/.ssh/config`:

```bash
nano ~/.ssh/config
```

Add this configuration:
```
Host github.com
  HostName github.com
  User git
  IdentityFile /Users/demontethomason/code/github_rsa
  AddKeysToAgent yes
```

Save and exit (Ctrl+X, then Y, then Enter)

### Step 4: Test SSH Connection to GitHub
Test your authentication:
```bash
ssh -T git@github.com
```

**Expected output:**
```
Hi [your-username]! You've successfully authenticated, but GitHub does not provide shell access.
```

If you see this message, your SSH key is working correctly! ✅

---

## Part 4: Create GitHub Repository and Push Code

### Step 1: Create New Repository on GitHub
1. Go to https://github.com/new
2. **Repository name**: `nexus-9k-evpn-config` (or your choice)
3. **Description**: "Cisco Nexus 9K BGP EVPN VXLAN Configuration"
4. **Visibility**: Choose Public or Private
5. **Do NOT initialize** with README, .gitignore, or license (we already have files)
6. Click **"Create repository"**

### Step 2: Link Local Repository to GitHub
Copy the SSH URL from GitHub (should look like: `git@github.com:username/nexus-9k-evpn-config.git`)

In your terminal, run:
```bash
cd /Users/demontethomason/code
git remote add origin git@github.com:[your-username]/nexus-9k-evpn-config.git
```

### Step 3: Push Code to GitHub
Push your master branch:
```bash
git push -u origin master
```

**Expected output:**
```
Enumerating objects: X, done.
Counting objects: 100% (X/X), done.
...
To github.com:[your-username]/nexus-9k-evpn-config.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

### Step 4: Verify Upload
1. Refresh your GitHub repository page
2. You should see all your files:
   - spine-01.txt
   - spine-02.txt
   - leaf-01.txt
   - leaf-02.txt
   - leaf-03.txt
   - leaf-04.txt
   - README.md
   - github_rsa.pub
   - .gitignore

---

## Part 5: Future Git Operations

### Pulling Updates from GitHub
```bash
git pull origin master
```

### Pushing New Changes
```bash
git add .
git commit -m "Description of changes"
git push origin master
```

### Viewing Repository Status
```bash
git status
```

### Viewing Commit History
```bash
git log --oneline --graph
```

---

## Security Best Practices

### ✅ DO:
- Keep your private key (`github_rsa`) secure and private
- Use strong passphrases for SSH keys (if regenerating)
- Add private keys to `.gitignore`
- Regularly rotate SSH keys (every 6-12 months)
- Delete SSH keys from GitHub when no longer needed
- Use different SSH keys for different machines

### ❌ DON'T:
- **NEVER** commit private keys to Git
- **NEVER** share your private key with anyone
- **NEVER** post private keys online or in public repositories
- Don't reuse SSH keys across multiple services
- Don't leave private keys in unsecured locations

---

## Troubleshooting

### Issue: "Permission denied (publickey)"
**Solution:**
1. Verify key was added to GitHub (check Settings > SSH keys)
2. Test connection: `ssh -T git@github.com`
3. Verify SSH agent has the key: `ssh-add -l`
4. Re-add key: `ssh-add /Users/demontethomason/code/github_rsa`

### Issue: "Could not resolve hostname github.com"
**Solution:**
1. Check internet connection
2. Verify DNS: `ping github.com`
3. Try using HTTPS instead of SSH temporarily

### Issue: "Repository not found"
**Solution:**
1. Verify repository exists on GitHub
2. Check you have access permissions
3. Verify remote URL: `git remote -v`
4. Update remote if needed: `git remote set-url origin git@github.com:[user]/[repo].git`

### Issue: SSH key "never used" status persists
**Solution:**
- This is normal until you perform your first Git operation
- Status will update after first `git push`, `git pull`, or `git clone`

---

## Additional Resources

- **GitHub SSH Documentation**: https://docs.github.com/en/authentication/connecting-to-github-with-ssh
- **Git Documentation**: https://git-scm.com/doc
- **SSH Key Generation**: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

---

## Quick Reference Commands

```bash
# View public key
cat /Users/demontethomason/code/github_rsa.pub

# Copy public key to clipboard (macOS)
cat /Users/demontethomason/code/github_rsa.pub | pbcopy

# Test GitHub SSH connection
ssh -T git@github.com

# Add SSH key to agent
ssh-add /Users/demontethomason/code/github_rsa

# Check current remotes
git remote -v

# Add GitHub remote
git remote add origin git@github.com:[username]/[repo].git

# Push to GitHub
git push -u origin master
```

---

## Notes
- **Key Location**: `/Users/demontethomason/code/`
- **Private Key**: `github_rsa` (protected by .gitignore)
- **Public Key**: `github_rsa.pub` (safe to share)
- **Key Type**: RSA 4096-bit
- **Key Comment**: demontethomason@github

---

## Revision History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-02-05 | Initial procedure document |

---

**⚠️ SECURITY WARNING**: The private key file `github_rsa` contains sensitive authentication credentials. Protect it like a password. It has been excluded from Git via `.gitignore` for your security.
