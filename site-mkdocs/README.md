# MkDocs Material Site

This directory contains the MkDocs Material implementation for the AirSDLC documentation.

## Features

- 🎨 Beautiful Material Design theme
- 🔍 Built-in search functionality
- 📱 Fully responsive
- 🌓 Dark/Light mode support
- 🎯 Navigation tabs and sections
- 📝 Mermaid diagram support

## Prerequisites

```bash
pip install mkdocs-material
```

## Development

```bash
# From this directory (site-mkdocs/)
mkdocs serve

# Or from project root
cd site-mkdocs && mkdocs serve
```

Visit: http://localhost:8000

## Build

```bash
# Build static site
mkdocs build

# Output will be in: site/
```

## Deploy to GitHub Pages

```bash
# Deploy from this directory
mkdocs gh-deploy

# Or use GitHub Actions (recommended)
```

## Directory Structure

```
site-mkdocs/
├── mkdocs.yml          # Configuration (points to ../docs/)
├── overrides/          # Theme customizations
└── site/              # Build output (gitignored)
```

## Configuration Highlights

- **docs_dir**: `../docs` - References original documentation
- **Theme**: Material Design with AirsStack branding
- **Colors**: Deep orange (Rust) primary, Teal accent
- **Features**: Search, navigation, code copy, dark mode

## Notes

- The `docs_dir` is set to `../docs`, so all content comes from the main docs/ directory
- No duplication of content - single source of truth maintained
- Custom styling can be added in `overrides/` directory
