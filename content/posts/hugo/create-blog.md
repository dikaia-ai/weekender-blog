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
summary: Create Your First Hugo Site with Netlify
---

[Hugo](https://gohugo.io/) is the foundation we will be using to create our blog website -
it is also the framework that this website is built upon. If you are
unfamiliar with Hugo, it is a static site generator. A static site
is a website that does not connect to a backend server - ie, the website
does not dynamically change and all of the elements are fixed in place at
the time of render/compilation.

The core of Hugo is built around markdown files. We will see more detail on this later
but for now just understand that Hugo uses these markdown files to render each page or
post on our website.

## Creating a Hugo Project

Note: The Hugo [documentation](https://gohugo.io/getting-started/quick-start/) goes into much more detail than I will be able to here. I am
using a macOS system so I am unable to definitively explain the install process on other
operating systems. Once you have the installation completed, all of the following steps
will be identical, regardless of operating system. If you have questions, feel free to
reach out using the [contact page](https://weekenddatascientist.netlify.app/contact/) and I will do my best
to respond.

### Step 1: Version Control

It is highly recommended to use a version control system for keeping up with changes to
your website and it is required if you would like to host your site on a service like
[Netlify](https://www.netlify.com/). If you do not already know how to use GitHub, please
read through my post on setting up your [first repository](https://weekenddatascientist.netlify.app/posts/github/first-git/).

### Step 2: Installation

Run the installation command below. Remember - this is for macOS, so your command may differ.

```bash
brew install hugo 
```

To verify your installation, run the following:

```bash
hugo version
```

output:

```bash
➜  hugo git:(main) ✗ hugo version
hugo v0.92.1+extended darwin/amd64 BuildDate=unknown
```

### Step 3: Create a New Site

```bash
hugo new site my-blog -f yml

# note: -f allows you to choose the file extension of your config file
# the default is toml but I prefer yaml
```

![Hugo New Site](/posts/hugo/hugo-new-site.png)

This command will create a new site folder called `my-blog` in the directory from which
you ran the script. For example, if we created a new website in the `www` folder, the
file structure would be:

```bash
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

Something to note is that Hugo does **not** come with a theme out of the box. So, before you get started on creating the content of your pages, you will need to head to https://themes.gohugo.io/ and choose a theme that fits your site.

I am using the [PaperMod theme](https://github.com/adityatelange/hugo-PaperMod), created by [Aditya Telange](https://github.com/adityatelange). For the sake of this demo, this is the theme that we will be using as it has a variety of features (search, categories, tags, etc) and it is pretty easy to get working.

1. Start by cloning the theme's GitHub repository to your `themes` folder. This can be done a variety of ways but I prefer using GitHub's submodule feature - think of it like nested repositories.

    ```bash
    git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod --depth=1
    git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
    ```

    Once you have added the submodule, you should now see a new entry has been appended to your `.gitmodules` file and your `themes` folder now looks something like this:

    ```bash
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

    ```bash
    git submodule update --remote --merge
    ```

2. Next, we must alter the sites `config.yml` to use this new theme. The config file allows us to take advantage of the features that come bundled with the theme - and by setting certain variables, we can activate or modify these features. Below is an example of how I have my config setup but this is not the only way to do it, as you may need more or less features than I do. I strongly suggest checking if your theme has an example config file on the GitHub page that you downloaded the theme from.

    - This is going to be a lot of text - [skip to next step ⬇️](https://weekenddatascientist.netlify.app/posts/hugo/create-blog/#serve-hugo-site-locally)

```yaml
baseURL: "https://kmclester.com/" # NOTE: include only if you have a custom domain
title: My Blog
paginate: 5
theme: PaperMod

enableInlineShortcodes: true
enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false
enableEmoji: true
googleAnalytics: G-06XXXXXXS

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
            SiteVerificationTag: "G-06XXXXXXS"

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

## Serve Hugo Site Locally

To view the site on your local machine, simply run:

```bash
hugo serve
```

Open `http://localhost:1313` in your browser, and you should be able to see the site live! Most themes come with default posts that can provide you with more information on how to use Hugo, the theme you selected, etc. - I would strongly recommend at least reading through some of these pages before creating your own content.

## Creating Content

This is great but how do we actually make any webpages? The main folder that you will be working in is the `content` folder - this is where all of the pages/posts on your website will be kept.

Open the project directory in your favorite text editor or IDE - I'm using VS Code for this project. Now, you can open any file in the `/content/posts` directory to begin editing files. Every time you save, the website will automatically update with the new content.

With this in mind, you can begin creating your own webpages.

Feel free to delete the included posts and begin making your own. Just remember, we are using markdown so you will need to create a new file with the `.md` file extension. Once you've created your file, you will need to set the pages [front matter](https://gohugo.io/content-management/front-matter/). The front matter is what allows you to attach meta data to your posts, as well as alter the configuration of a page. This is going to vary from theme to theme but I tend to use this for my pages:

```yaml
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
summary: Creating Your First Hugo Site
---
```

Note - the three dashes above and below the front matter are formatted for yaml code. Which symbol to use depends on the format of your config file. If you are using toml, you would use `+++` and json uses `{ }`.

The front matter should be followed by a single new line and then your content. Checkout [my github of this page](https://github.com/dikaia-ai/weekender-blog/blob/main/content/posts/hugo/create-blog.md) to see further example of how to format your markdown file(s).

## Hosting with Netlify

Incase you haven't done so already, create a git repo for this project. As a quick refresher, feel free to follow the steps below:

1. Go to GitHub and create a new repo
2. In your root project directory, run `git init`
3. Now add all of your files/changes by running `git add .`
4. Commit your changes: `git commit -m "Initial commit"`
5. Set branch to **main**: `git branch -M main`
6. Add remote origin: `git remote add origin <GIT_REPO_URL>`
7. Push changes to remote repo: `git push origin main`

Now head on over to [Netlify](https://netlify.com) and create an accout/login.

Once you have logged in, you should be greeted with a dashboard with the option `Add new site`. Select this and follow the instructions:

1. Add new site -> Import an existing project
   ![Netlify add new site](/posts/hugo/netlify-add-new.png)
2. You will be presented with three options: GitHub, GitLab, or BitBucket. Select `GitHub`
   ![Import existing project](/posts/hugo/import-existing.png)
3. Next, you must authorize Netlify to grant access to your Git repositories. You can either grant access for all repositories or select a specific repo (ie your blog repo)
4. Netlify will automatically recognize your project as a Hugo repo. I suggest keeping the options set to their defaults for now (see below)
    ![Netlify build options](/posts/hugo/netlify-build.png)
5. Select deploy and wait for Netlify to build your website
6. Once the build and deploy is completed, you should be able to access the link shown in your main site overview page
   ![Access newly deployed website](/posts/hugo/deployed-site.png)
7. Now every time you commit new changes to your Git repo, Netlify will automatically build and redeploy with the latest updates!

## Closing

Congrats on making it this far. If you have any questions or concerns, please feel free to reach out using the [contact page](https://weekenddatascientist.netlify.app/contact/). Please share with anyone that might find this useful!!
