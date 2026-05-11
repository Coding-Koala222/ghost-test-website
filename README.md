# Complete Tutorial: Ghost + Eleventy + GitHub Pages (Local CMS + Static Deploy)

This guide shows how to build a workflow where:

-   [Ghost](https://docs.ghost.org/install/local) runs **locally only**
-   Eleventy builds your site using the [Eleventy Starter Project](https://github.com/TryGhost/eleventy-starter-ghost)
-   GitHub stores the generated site
-   GitHub Pages hosts it
-   search engines can be optionally blocked

# 0\. Final architecture

```
Ghost (local only)
    ↓
Content APIEleventy build (local)
    ↓
generates static site
    ↓
Commit + push to your repo
    ↓
GitHub Pages deploys site
```

No backend server is online.

# 1\. Prerequisites

### Note: Unless otherwise stated, all commands run in terminal or shell should be run with administrative permissions. The easiest way to do this is to open the terminal or shell application with administrative permissions.

You need:

- A GitHub account [Sign Up Here](https://github.com/signup)

-   [Node.js](https://nodejs.org/en/download) (LTS)
-   [Git](https://git-scm.com/install/)
-   [Yarn](https://www.geeksforgeeks.org/node-js/how-to-install-yarn-with-npm/)

-   [Ghost CLI](https://docs.ghost.org/ghost-cli)
-   [GitHub CLI](https://github.com/cli/cli#installation)

-   [Microsoft Visual Studio Code](https://code.visualstudio.com/Download)
    - [To be signed into your GitHub account in Visual Studio Code](https://code.visualstudio.com/docs/sourcecontrol/github)
 
-   The latest version of [Python](https://www.python.org/downloads/)
    -   [Pip (Python Package Installer)](https://pypi.org/project/pip/) (Instructions below)
    -   [SetupTools](https://pypi.org/project/setuptools/) (Instructions below)

---

Run the following code in terminal or shell to install pip:

```
python -m ensurepip --upgrade
```

---

Run the following code in terminal or shell to install setup tools:

```
python -m pip install setuptools
```

# 2\. Create local Ghost instance

Create a folder where you would like to store your Ghost instance and copy its path.

Open terminal or shell and type:

```
cd [your_directory_path]
```

```
ghost install local
```

By default, the live preview of your Ghost website will run at:

```
http://localhost:2368
```

To access the Admin panel, go to:

```
http://localhost:2368/ghost
```

You can now:

- run `ghost start` to start Ghost
- run `ghost stop` to stop Ghost

# 3\. Retrieve Ghost integration key

In Ghost admin, go to:

```
Settings → Integrations → Add custom integration
```

Copy and save:

-   Content API Key
-   API URL

# 4\. Set Up Eleventy Builder

Create a new folder where you want the Eleventy builder to live and copy its path.

Run the following commands in order:

```
cd [your_folder_path]
```

In the following, replace [Your fork name] with the name you would like for your GitHub repository.

```
gh repo fork TryGhost/eleventy-starter-ghost --clone --fork-name [Your fork name]
```

```
yarn
```

# 5\. Configure Eleventy Builder environment variables

Open Visual Studio Code

Inside Visual Studio, open the directory/folder `[Your fork name]`

In the file `.env`, replace the placeholder info with your saved information:

- SITE_URL can be set to your apex domain if you are are using a custom domain.

```
GHOST_API_URL=http://localhost:2368G
HOST_CONTENT_API_KEY=your_key_here
SITE_URL=https://[Your-user-name].github.io/repo-name/
```

# 6\. Configure Eleventy Builder output folder for GitHub Pages

Edit `.eleventy.js` so that it looks like this:

```
module.exports = function (eleventyConfig) {
  return {
    pathPrefix: "/[Your fork name]/",
    dir: {
      input: "src",
      output: "docs"
    }
  };
};
```

The `output` property is now set to `docs`

`pathPrefix` has been added and uses your repo name

**If you are using a custom domain, remove the line `pathPrefix: "/[Your fork name]/",`**

# 7\. Fix URL handling in templates \*\*IMPORTANT\*\* (Only if using automatic GitHub Pages URL)

Any hardcoded links like:

```
<a href="/about/">
```

must become:

```
<a href="{{ '/about/' | url }}">
```

Same for assets:

```
<link rel="stylesheet" href="{{ '/assets/style.css' | url }}">
```

# 8\. Build the site

### Start Ghost:

```
cd [Your Ghost Directory]
```

```
ghost start
```

### Then build with Eleventy Builder:

```
cd [Your Eleventy Builder fork directory]
```

```
yarn build
```

### The output appears in:

```
docs/
```

# 9\. Test locally (optional)

If you are using a custom domain, run:

```
npx @11ty/eleventy --serve
```

If you are using the default GitHub Pages URL, rn:

```
npx @11ty/eleventy --serve --pathprefix=/repo-name/
```

# 10\. Commit your changes and sync to the GitHub repository



# 11\. Push to GitHub

Create a repo on GitHub, then:

```
git remote add origin https://github.com/username/repo-name.gitgit push -u origin main
```

# 12\. Enable GitHub Pages

In the GitHub web interface, go to:

```
Repo → Settings → Pages
```

Set:

-   Source: `main`
-   Folder: `/docs`

After a minute, your site will be live.

By default it will be hosted at:

```
https://username.github.io/repo-name/
```

# 13\. Create a publish script for future ease of use (recommended)

## For Mac and Linux

[Make a MacOS Shortcut](https://iboysoft.com/tips/create-shortcut-for-terminal-command-mac.html)

[Make a Linux Shortcut](https://richhewlett.com/2021/03/27/creating-linux-desktop-shortcuts/)

Here is the bash script for reference:

```
#!/bin/bash

cd [your ghost instance directory path]

ghost start

cd [your eleventy builder fork directory path]

yarn build

git add .

git commit -m "Publish update $(date)"

git push

ghost stop
```

### Example use:

Save the script text as:

```
[script name].sh
```

Allow execution of the script:

```
chmod +x [script path].sh
```

Run the script:

```
./publish.sh
```

# 14\. Prevent search engine indexing (optional)

```
[Your Eleventy fork name]/
└── src/
    └── robots.njk
```

Set `robots.njk` to:

```
---
permalink: 'robots.txt'
---
User-agent: *
Disallow: /
{# To enable search indexing, comment out the above line and uncomment the below line. #}
{# Allow: / #}
```

This generates `robots.txt` with the correct setting when you build the website.

---

# Key features that WORK

## Content

-   Posts
-   Pages
-   Tags
-   Authors
-   Markdown / rich editor

## Site generation

-   Static HTML
-   Fast loading
-   SEO metadata (if configured)

## Deployment

-   GitHub Pages hosting
-   Git-based publishing
-   No backend required

## Development

-   Local Ghost editing
-   Offline writing
-   Full control over builds

# Features that DO NOT work (offline Ghost)

Because Ghost is not online:

## Ghost-native dynamic features

-   Member accounts
-   Subscriptions
-   Ghost Portal
-   Newsletters
-   Stripe payments
-   Login system

## Live backend features

-   Admin dashboard online access
-   Webhooks from production Ghost
-   API-driven live updates

# 17\. Common pitfalls

## ❌ Assets not loading

Fix:

-   use `{{ url }}` filter
-   ensure `pathPrefix` is correct

## ❌ Wrong GitHub Pages paths

Fix:

-   ensure `/repo-name/` prefix is set everywhere

## ❌ Build works locally but breaks online

Cause:

-   hardcoded `/` paths instead of `url` filter

## ❌ Confusion about whether Ghost is needed at runtime

Reminder:

-   Ghost is ONLY for building content
-   Ghost does not deploy the site

# Analogy

You can think of the pieces like this:

> Ghost = Word processor  
> Eleventy = compiler  
> GitHub Pages = hosting server

There is no interaction with a dynamic server after after the website is built.

# Optional upgrades

If you want to improve later, you can:

-   Add local search (Pagefind)
-   Add user comment feature (Giscus)
-   [Use custom a custom domain that you own](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages)
-   Move images to S3 / R2

# You're Done!

You now have a fully static publishing system where:

-   Ghost is local-only
-   GitHub is your deploy trigger
-   GitHub Pages hosts everything
-   builds are reproducible and fast
