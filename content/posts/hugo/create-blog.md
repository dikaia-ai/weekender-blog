---
author: Kyle McLester
date: 2022-02-21
format: hugo
title: Creating a Personal Blog Site
categories:
    - Web Development
tags:
    - hugo
    - blog
    - personal website
    - markdown
    - netlify
summary: 🚧  UNDER CONSTRUCTION
---



[Hugo](https://gohugo.io/) is the foundation we will be using to create
our blog website - it is also the framework that this website is built
upon. If you are unfamiliar with Hugo, it is a static site generator. A
static site is a website that does not connect to a backend server - ie,
the website does not dynamically change and all of the elements are
fixed in place at the time of render/compilation.

The core of Hugo is built around markdown files. We will see more detail
on this later but for now just understand that Hugo uses these markdown
files to render each page or post on our website.

## Creating a Hugo Project

Note: The Hugo
[documentation](https://gohugo.io/getting-started/quick-start/) goes
into much more detail than I will be able to here. I am using a macOS
system so I am unable to definitively explain the install process on
other operating systems. Once you have the installation completed, all
of the following steps will be identical, regardless of operating
system. If you have questions, feel free to reach out using the [contact
page](https://kmclester.com/contact/) and I will do my best to respond.

### Step 1: Version Control

It is highly recommended to use a version control system for keeping up
with changes to your website and it is required if you would like to
host your site on a service like [Netlify](https://www.netlify.com/). If
you do not already know how to use GitHub, please read through my post
on setting up your [first
repository](https://kmclester.com/posts/github/first-git/).

### Step 2: Installation

Run the installation command below. Remember - this is for macOS, so
your command may differ.

``` bash
brew install hugo 
```

To verify your installation, run the following:

``` bash
hugo version
```

output:

``` bash
➜  hugo git:(main) ✗ hugo version
hugo v0.92.1+extended darwin/amd64 BuildDate=unknown
```

### Step 3: Create a New Site

``` bash
hugo new site my-blog -f yml

# note: -f allows you to choose the file extension of your config file
# the default is toml but I prefer yaml
```

![Hugo New Site](/posts/hugo/test.png)

This command will create a new site folder called `my-blog` in the
directory from which you ran the script. For example, if we created a
new website in the `www` folder, the file structure would be:

``` bash
📂 www
    📂 my-blog
        📂 archetypes
        📂 content
        📂 layouts
        📂 themes
        📂 data
        📂 static
        📄 config.yml
```

### Step 4: Picking a Theme

Something to note is that Hugo does **not** come with a theme out of the
box. So, before you get started on creating the content of your pages,
you will need to head to https://themes.gohugo.io/ and choose a theme
that fits your site.

I am using the [PaperMod
theme](https://github.com/adityatelange/hugo-PaperMod), created by
[Aditya Telange](https://github.com/adityatelange). For the sake of this
demo, this is the theme that we will be using as it has a variety of
features (search, categories, tags, etc) and it is pretty easy to get
working.

1.  Start by cloning the theme's GitHub repository to your `themes`
    folder. This can be done a variety of ways but I prefer using
    GitHub's submodule feature - think of it like nested repositories.

    ``` bash
    git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod --depth=1
    git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
    ```

    Once you have added the submodule, you should now see a new entry
    has been appended to your `.gitmodules` file and your `themes`
    folder now looks something like this:

    ``` bash
    📂 themes
        📂 PaperMod
            📂 .github
            📂 assets
            📂 i18n
            📂 images
            📂 layouts
            📄 go.mod
            📄 LICENSE
            📄 README.md
    ```

    If you ever need to update the theme, run:

    ``` bash
    git submodule update --remote --merge
    ```

2.  Next, we must alter the sites `config.yml` to use this new theme.
    The config file allows us to take advantage of the features that
    come bundled with the theme - and by setting certain variables, we
    can activate or modify these features. Below is an example of how I
    have my config setup but this is not the only way to do it, as you
    may need more or less features than I do. I strongly suggest
    checking if your theme has an example config file on the GitHub page
    that you downloaded the theme from.

This is going to be a lot of text - [skip to next step
⬇️](https://kmclester/posts/hugo/create-blog/#serve-hugo-site)

``` yaml
baseURL: "https://kmclester.com/"
title: Kyle's Blog
paginate: 5
theme: PaperMod

enableInlineShortcodes: true
enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false
enableEmoji: true
googleAnalytics: G-06XBWZ9STS

minify:
    disableXML: true
    minifyOutput: true

languages:
    en:
        languageName: "English"
        weight: 1
        menu:
            main:
                - name: Archive
                  url: archives
                  weight: 5
                - name: Search
                  url: search/
                  weight: 10
                - name: Tags
                  url: tags/
                  weight: 10
                - name: Categories
                  url: categories/
                  weight: 10
                - name: Contact
                  url: contact/
                  weight: 2

outputs:
    home:
        - HTML
        - RSS
        - JSON

params:
    env: production
    description: "Personal blog"
    author: Kyle McLester

    defaultTheme: auto
    ShowShareButtons: true
    ShowReadingTime: true
    displayFullLangName: true
    ShowPostNavLinks: true
    ShowBreadCrumbs: true
    ShowCodeCopyButtons: true
    ShowToc: true

    homeInfoParams:
        Title: "Welcome!"
        Content: >
            This website is going to act as a dumping ground for things I'm learning or working on.
            Feel free to take a peek

    socialIcons:
        - name: github
          url: "https://github.com/kmcleste"
        - name: RsS
          url: "index.xml"

    editPost:
        URL: "https://github.com/kmcleste/personal-blog/tree/main/content"
        Text: "Suggest Changes"
        appendFilePath: true

    analytics:
        google:
            SiteVerificationTag: "G-06XBWZ9STS"

taxonomies:
    category: categories
    tag: tags
    series: series

markup:
    goldmark:
        renderer:
            unsafe: true

privacy:
    vimeo:
        disabled: false
        simple: true

    twitter:
        disabled: false
        enableDNT: true
        simple: true

    instagram:
        disabled: false
        simple: true

    youtube:
        disabled: false
        privacyEnhanced: true

services:
    instagram:
        disableInlineCSS: true
    twitter:
        disableInlineCSS: true
```

## Serve Hugo Site
