+++
draft = false
date = 2025-10-19T15:16:04+02:00
title = "LAB Hugo Setup"
description = ""
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++


## Introduction

This guide presents the setup of a static blog with HUGO and its automatic deployment via GitHub Actions on GitHub Pages. This solution offers a modern, free, and fully automated workflow.

## Process Overview

The principle is simple: every time you push content to GitHub, an automatic action will compile your HUGO site and deploy it on GitHub Pages. You just write and push, the rest happens automatically!

## Step 1: Create Your HUGO Site

### Installation and Initialization

Install HUGO on your machine (via Homebrew, apt-get, or your OS's package manager), then create your site with the command `hugo new site my-blog`. Then initialize a Git repository in this folder.

### Choose a Theme

HUGO works with themes. Visit [themes.gohugo.io](https://themes.gohugo.io/) to choose one. Most are installed as a Git submodule in the `themes/` folder. Personally, I use PaperMod for its simplicity and speed.

### Basic Configuration

The `hugo.toml` (or `config.toml`) file contains the global configuration: your base URL, site title, theme used, menus, etc. **Important point**: the base URL must match your future GitHub Pages site (`https://username.github.io/repo-name/`).

### Create Content

Use `hugo new posts/my-article.md` to create an article. HUGO generates a markdown file with front matter (YAML metadata). Remove `draft: true` when the article is ready to be published.

## Step 2: Host on GitHub

### Create the Repository

Create a new public repository on GitHub. The repository must be public to benefit from GitHub Pages for free. Then connect your local project to this remote repository and push your code.

### Project Structure

Your project will have this structure:
- `content/`: your articles and pages
- `themes/`: the chosen theme
- `static/`: images and static files
- `hugo.toml`: configuration
- `.github/workflows/`: the automation file (which we'll create)

## Step 3: Automate with GitHub Actions

### The Workflow File

Create the file `.github/workflows/deploy.yml`. This file contains the instructions for GitHub Actions. It defines:

**When to trigger**: On every push to the `main` branch

**What it should do**:
1. Install HUGO on GitHub's server
2. Fetch your code (including submodules if you have any)
3. Compile the HUGO site in production mode
4. Deploy the result to GitHub Pages

The workflow typically includes two jobs: one for the build, one for deployment. Official GitHub actions (`actions/checkout`, `actions/configure-pages`, etc.) greatly facilitate the configuration.

### Required Permissions

The workflow needs specific permissions to write to GitHub Pages. These permissions are defined in the YAML file with the `contents: read` and `pages: write` parameters.

## Step 4: Enable GitHub Pages

In your GitHub repository settings:
1. Go to **Settings** > **Pages**
2. Select **GitHub Actions** as the source (not "Deploy from a branch")
3. Save

From the first push, the workflow starts and your site is deployed automatically!

## Daily Workflow

Once everything is configured, publishing is super simple:

1. Write a new article with `hugo new posts/title.md`
2. Test locally with `hugo server -D`
3. Commit and push to GitHub
4. GitHub Actions does everything else automatically
5. Your article is online in 1-2 minutes!

## Advanced Features

### Custom Domain

You can use your own domain by creating a `static/CNAME` file with your domain, then configuring the DNS with your registrar. GitHub Pages automatically manages the SSL certificate via Let's Encrypt.

### Optimizations

The workflow can be optimized with caching to speed up recurring builds. You can also configure HUGO to minify HTML/CSS/JS in production.

### Multiple Environments

You can create different workflows for a dev branch (preview) and the main branch (production), allowing you to test before publishing.

## Points of Attention

### Base URL

The most common error is an incorrect `baseURL` configuration in `hugo.toml`. It must exactly match the final URL of your site, with the trailing slash.

### Themes as Submodules

If your theme is a Git submodule, make sure the GitHub Actions workflow fetches them properly with `submodules: recursive` in the `checkout` action.

### Draft Files

Articles with `draft: true` are not compiled in production. Remember to remove this line before pushing.

## Why This Solution?

This stack offers several major advantages:

**Performance**: Ultra-fast static sites, no database or server to manage

**Free**: GitHub Pages is free for public repositories, unlimited bandwidth

**Version Control**: Your entire blog history is in Git, impossible to lose content

**Simplicity**: No complex CMS, just Markdown and Git

**Modern CI/CD**: Professional workflow with automatic deployment

**Security**: No attack surface, no plugins to update

## Resources

- [HUGO Documentation](https://gohugo.io/documentation/)
- [HUGO Themes](https://themes.gohugo.io/)
- [GitHub Actions](https://docs.github.com/en/actions)
- [GitHub Pages](https://docs.github.com/en/pages)

## Conclusion

With HUGO and GitHub Actions, you get a professional blog that deploys automatically. No need to worry about hosting, updates, or security. Focus on what matters: writing quality content.

The initial setup takes 30 minutes, but then everything is automated. It's the ideal solution for a technical blog, portfolio, or project documentation.
