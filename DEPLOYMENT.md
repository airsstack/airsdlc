# GitHub Pages Deployment Guide

This document provides instructions for deploying the AirSDLC documentation (MkDocs Material) to GitHub Pages.

## Overview

The AirSDLC documentation uses **MkDocs Material** theme with the content source in the `docs/` directory.

## Quick Start - Automated Deployment

The easiest way to deploy is via **GitHub Actions** (already configured).

### How It Works

1. Push changes to the `main` branch
2. GitHub Actions automatically:
   - Detects changes in `docs/` or `site-mkdocs/`
   - Builds the MkDocs site
   - Deploys to `gh-pages` branch
3. GitHub Pages serves from `gh-pages` branch

### Setup Steps

#### 1. Enable GitHub Actions (One-time setup)

**Option A: If You Have Admin Access**

In your GitHub repository:
1. Go to **Settings** → **Actions** → **General**
2. Under "Workflow permissions", select:
   - ✅ **Read and write permissions**
3. Click **Save**

**Option B: If You Don't Have Admin Access**

You have several alternatives:

**B1: Ask Repository Admin**
- Ask the repository owner/admin to enable "Read and write permissions" for workflows
- They can do this in Settings → Actions → General

**B2: Use Personal Access Token (PAT)**
1. Create a Personal Access Token:
   - Go to GitHub **Settings** (your profile, not repo) → **Developer settings** → **Personal access tokens** → **Tokens (classic)**
   - Click **Generate new token (classic)**
   - Name: "MkDocs Deploy"
   - Expiration: Choose duration (90 days, 1 year, or no expiration)
   - Select scopes:
     - ✅ `repo` (full control of private repositories)
   - Click **Generate token**
   - **Copy the token immediately** (you won't see it again!)

2. Add token to repository secrets:
   - Go to repository **Settings** → **Secrets and variables** → **Actions**
   - Click **New repository secret**
   - Name: `GH_DEPLOY_TOKEN`
   - Value: Paste your token
   - Click **Add secret**

3. The workflow will automatically use this token (already configured)

**B3: Use Deploy Key (Advanced)**
- Generate SSH deploy key
- Add to repository Deploy Keys with write access
- Update workflow to use the key

**B4: Manual Deployment (No Admin Needed)**
- Deploy from your local machine using `mkdocs gh-deploy`
- No GitHub Actions setup required
- See "Manual Deployment" section below

#### 2. Configure GitHub Pages (One-time setup)

1. Go to **Settings** → **Pages**
2. Under "Source":
   - Source: **Deploy from a branch**
   - Branch: **gh-pages**
   - Folder: **/ (root)**
3. Click **Save**

#### 3. Deploy

Just push to `main`:
```bash
git add .
git commit -m "Update documentation"
git push origin main
```

The GitHub Action will automatically build and deploy!

### Manual Trigger

You can also trigger deployment manually:
1. Go to **Actions** tab in GitHub
2. Select "Deploy MkDocs to GitHub Pages"
3. Click **Run workflow** → **Run workflow**

---

## Manual Deployment (Alternative)

If you prefer to deploy manually from your local machine:

### Prerequisites
```bash
# Using pip
pip install -r site-mkdocs/requirements.txt

# Or using uv (recommended)
uv pip install -r site-mkdocs/requirements.txt
```

### Local Preview
```bash
cd site-mkdocs
mkdocs serve
# Visit http://localhost:8000
```

### Deploy to GitHub Pages
```bash
cd site-mkdocs
mkdocs gh-deploy --force --clean
```

This will:
1. Build the site
2. Push to `gh-pages` branch
3. GitHub Pages will automatically serve it

---

## Site URL

After deployment, your site will be available at:

```
https://airsstack.github.io/airsdlc/
```

---

## Project Structure

```
airsdlc/
├── docs/                    # Documentation content (Markdown files)
│   ├── index.md
│   ├── overview.md
│   ├── philosophy.md
│   ├── lifecycle.md
│   ├── artifacts.md
│   ├── workflow.md
│   ├── operations.md
│   ├── extensibility.md
│   ├── core.md
│   ├── AI-DLC-ATTRIBUTION.md
│   └── examples/
│       ├── README.md
│       ├── example-prd.md
│       ├── example-daa.md
│       ├── example-tip.md
│       ├── example-rfc.md
│       ├── example-adr.md
│       └── example-playbook-entry.md
│
├── site-mkdocs/             # MkDocs configuration
│   ├── mkdocs.yml          # Main configuration file
│   ├── requirements.txt    # Python dependencies
│   ├── overrides/          # Custom theme overrides
│   │   ├── assets/
│   │   │   └── extra.css  # Custom styling
│   │   └── main.html       # Template overrides
│   └── site/               # Build output (gitignored)
│
└── .github/
    └── workflows/
        └── deploy-mkdocs.yml  # Automated deployment workflow
```

---

## Configuration Highlights

**File**: `site-mkdocs/mkdocs.yml`

Key settings:
- **docs_dir**: `../docs` - Points to documentation source
- **site_dir**: `site` - Build output directory
- **site_url**: `https://airsstack.github.io/airsdlc/`
- **theme**: Material Design with custom colors (rust orange, teal)
- **features**: Navigation tabs, search, code copy, Mermaid diagrams

---

## Troubleshooting

### Problem: Deployment fails in GitHub Actions

**Check**:
1. Workflow permissions enabled? (Settings → Actions → General)
2. Check the Actions log for specific errors

**Solution**:
```bash
# Run locally to debug:
cd site-mkdocs
mkdocs build --verbose
```

### Problem: `mkdocs: command not found`

**Solution**:
```bash
pip install -r site-mkdocs/requirements.txt
```

### Problem: Links broken after deployment

**Check**: Ensure `site_url` in `mkdocs.yml` matches your GitHub Pages URL:
```yaml
site_url: https://airsstack.github.io/airsdlc/
```

### Problem: Mermaid diagrams not rendering

**Check**: `pymdownx.superfences` is configured in `mkdocs.yml`:
```yaml
markdown_extensions:
  - pymdownx.superfences:
      custom_fences:
        - name: mermaid
          class: mermaid
```

### Problem: 404 on GitHub Pages

**Check**:
1. GitHub Pages source is set to `gh-pages` branch
2. `gh-pages` branch exists and has content
3. Wait a few minutes - GitHub Pages can take time to update

---

## Custom Domain (Optional)

To use a custom domain like `airsdlc.dev`:

### 1. Buy Domain
From registrar (Namecheap, Cloudflare, etc.)

### 2. Add DNS Records
```
Type: CNAME
Name: www
Value: airsstack.github.io
```

### 3. Configure in GitHub
1. Go to **Settings** → **Pages**
2. Under "Custom domain", enter your domain
3. Check "Enforce HTTPS"

### 4. Add CNAME file
Create `docs/CNAME` with your domain:
```
airsdlc.dev
```

---

## GitHub Actions Workflow Explained

**File**: `.github/workflows/deploy-mkdocs.yml`

```yaml
name: Deploy MkDocs to GitHub Pages

on:
  push:
    branches: [ main ]           # Trigger on push to main
    paths:
      - 'docs/**'                # Only when docs change
      - 'site-mkdocs/**'         # Or site config changes
  workflow_dispatch:             # Allow manual trigger

permissions:
  contents: write                # Write access to push to gh-pages

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.x'
      
      - name: Install dependencies
        run: pip install -r site-mkdocs/requirements.txt
      
      - name: Build and deploy
        run: |
          cd site-mkdocs
          mkdocs gh-deploy --force --clean
```

---

## Updating Documentation

### Workflow

1. **Edit docs** in `docs/` directory
2. **Preview locally**:
   ```bash
   cd site-mkdocs && mkdocs serve
   ```
3. **Commit changes**:
   ```bash
   git add docs/
   git commit -m "Update documentation"
   ```
4. **Push to GitHub**:
   ```bash
   git push origin main
   ```
5. **GitHub Actions deploys automatically**

View deployment status in the **Actions** tab on GitHub.

---

## Why MkDocs?

We chose MkDocs Material over VitePress because:

✅ **Simpler** - Python-based, fewer moving parts  
✅ **Reliable** - Mature, stable, well-documented  
✅ **Built-in Mermaid** - Works out of the box  
✅ **Material Design** - Beautiful, accessible theme  
✅ **Great Search** - Instant, powerful search  
✅ **Easy Deployment** - One command to deploy  
✅ **GitHub Actions** - Simple CI/CD setup  

---

## Resources

- **MkDocs Documentation**: https://www.mkdocs.org
- **Material for MkDocs**: https://squidfunk.github.io/mkdocs-material/
- **GitHub Pages**: https://docs.github.com/en/pages
- **GitHub Actions**: https://docs.github.com/en/actions

---

## Questions?

Open an issue at: https://github.com/airsstack/airsdlc/issues

