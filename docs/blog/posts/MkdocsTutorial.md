---
date:
  created: 2024-12-21
---

# Setting up your own site using mkdocs material !

This tutorial gives you a perfect guide to build your own static site with mkdocs material.

MkDocs is a fast, simple static site generator for you to build project documentations. Also, you can use this project to build your personal website, too. MkDocs material is a framework based on MkDocs to serve a beautiful theme for your site.

In this tutorial, I will show you how to build your site simply and it is totally free, then publish it to your GitHub pages. The process is totally free of charge.

<!-- more -->

View [MkDocs material official documentations](https://squidfunk.GitHub.io/mkdocs-material/) here.

## Install MkDocs and build a running site.

### Install MkDocs-material using python

This tutorial recommended you to install MkDocs with Python **pip** command. Be sure to have a running python environment in your system.

First, in your terminal, use the following command to install MkDocs.

```bash
pip install mkdocs-material
```

After the installation finishes, you should now be able to generate a project in your folder.

### Create a site folder in your system

Create a folder where you want to build your site. Open the folder in the terminal.

```bash
mkdocs new .
```

Be careful that there is a dot after the "new" (When I was building the site I stucked here for 10 minutes. )

This command generates a Mkdocs project in your current folder.

```
.
├─ docs/
│  └─ index.md
└─ mkdocs.yml
```

### Basic configuration

Add the following lines to `mkdocs.yml`. You should already have the `site_name` in your file, then just add the `site_url` and theme to enable the material theme.

```
site_name: My site
site_url: https://mydomain.org/mysite
theme:
name: material
```

For the URL, we just fill it in with an example. Later this is not related to our publishment to GitHub.

### Test your site in your local server

After these configuration, finally we can see our product.

Now you should be able to create and modify your markdown files. To test it and make it running, use the following command in your folder terminal.

```bash
mkdocs serve
```

The default setting of material runs your site at local server port 8000. If you see the output like the followings, you are success to build a running site using MkDocs.

```
INFO    -  Building documentation...
INFO    -  Cleaning site directory
INFO    -  Documentation built in 0.14 seconds
INFO    -  [20:06:34] Watching paths for changes: 'docs', 'mkdocs.yml'
INFO    -  [20:06:34] Serving on http://127.0.0.1:8000/mysite/

```

Now you can access the site in your browser using the following path.

```
127.0.0.1:8000
```

## Publish your site to GitHub pages

Now you should already have a running site on your local server.

We are going to publish this site to your GitHub pages then everyone having the link can see your personal website.

### Using VScode and git to help you update your site and synchronize your local folder with your GitHub repository.

I strongly recommend you to use VScode to help you update your website.

Install VScode from the official website of [VScode](https://code.visualstudio.com/).

Open your website folder in VScode and go to the "source control" panal and you can choose to create a public or a private repository in your GitHub account ( You may need to login first. )

Give a name to your repository, then VScode will generate a repository for you.

Once you made a change of your website, you can synchronize it in your VScode.

### Set up actions in GitHub and publish your site

Finally, we arrive to the real publishment of your site.

Go to your repository in your browser.

create a new GitHub Actions workflow copy the following contents:

```Action
name: ci
on:
  push:
    branches:
      - master
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name github-actions[bot]
          git config user.email 41898282+github-actions[bot]@users.noreply.github.com
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: .cache
          restore-keys: |
            mkdocs-material-
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

Now, when a new commit is pushed to either the master or main branches, the static site is automatically built and deployed.

Finally, go to your repository settings -> pages -> Build and Deployment -> branch, change your branch to `gh-pages` and save it.

## Finish !

Great job, now you already have a running personal website on the internet, to view your website, go to `<username>.github.io/<repository>`

For more settings and customization, you can reference to the official [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) website, I hope you can have a satisfactory personal website !
