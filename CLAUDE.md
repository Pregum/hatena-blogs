# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Hatena Blog management repository that synchronizes blog posts between GitHub and Hatena Blog using GitHub Actions workflows. It enables version control, collaborative editing, and automated publishing of blog posts.

## Common Commands and Workflows

### Creating a New Draft Post
To create a new draft, manually trigger the GitHub Action:
1. Go to Actions tab → "Create draft from hatena blog"
2. Click "Run workflow"
3. Enter the title for your new post
4. The workflow will create a new branch with your draft

### Publishing Posts
Posts are automatically published when:
1. A PR containing changes to files in `entries/` is merged to main
2. The `push.yaml` workflow runs and syncs changes to Hatena Blog

### Syncing from Hatena Blog
To pull latest changes from Hatena Blog:
- The `pull.yaml` workflow runs automatically on schedule
- Or manually trigger it from the Actions tab

## Architecture and Key Components

### Directory Structure
- **`entries/`**: Published blog posts organized by date under `pregum-fox.hatenablog.jp/entry/`
- **`draft_entries/`**: Draft posts pending publication
- **`.github/workflows/`**: GitHub Actions automation workflows

### Workflow System
All workflows use reusable workflows from `hatena/hatenablog-workflows@v1`:
- **initialize.yaml**: Initial sync of all existing blog posts
- **push.yaml**: Publishes posts when PRs are merged to main
- **pull.yaml**: Syncs updates from Hatena Blog (scheduled or manual)
- **create-draft.yaml**: Creates new draft posts with PR
- **pull-draft.yaml**: Pulls specific draft from Hatena Blog
- **push-draft.yaml**: Pushes draft changes back to Hatena Blog

### Configuration
- **blogsync.yaml**: Configures blog domain (`pregum-fox.hatenablog.jp`) and username
- **draft.template**: Template for new draft posts with Title and Draft status
- **GitHub Secrets**: Requires `OWNER_API_KEY` for Hatena Blog API access
- **GitHub Variables**: Requires `BLOG_DOMAIN` set to your blog domain

## Post Format

Blog posts use Markdown with YAML frontmatter:
```markdown
---
Title: Post Title
Date: 2025-01-13T21:30:00+09:00
URL: https://pregum-fox.hatenablog.jp/entry/YYYY/MM/DD/HHMMSS
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/ID
Draft: false
---

Post content in Markdown...
```

For drafts, only Title and Draft status are required in frontmatter.