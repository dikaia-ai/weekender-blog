---
author: Kyle McLester
date: 2022-02-08
format: hugo
summary: Integrate Quarto to your Hugo website
title: How to Quarto
---

Head to the **[Get Started](https://quarto.org/docs/get-started/)**
page on Quarto's website and download the latest version of Quarto for
your operating system. You can verify your installation is working by
running this command:

``` bash
quarto check install
```

![check-install](/posts/quarto/quarto-check-install.png)

## Hugo Integration

Once you have verified your installation, you can head over to your
project folder. Start by editing your site configuration file
(config.toml) by adding the following parameters:

```toml
ignoreFiles = [ "\.qmd$", "\.ipynb$" ]

[markup.goldmark.renderer]
    unsafe = true
```

## Creating Quarto documents

1.  Create a directiry within **content** that will hold your Quarto
    article

2.  Add your article as a .qmd file. So you might name it something like
    "tutorial.qmd". This will create "tutorial.md" when rendered which
    is then displayed by Hugo.

3.  Add your Hugo [front
    matter](https://gohugo.io/content-management/front-matter/) and then
    specify **format**: **hugo** and any other Quarto options. Here's an
    example front matter:

``` yaml
---
title: How to Quarto
date: "2022-02-08"
format: hugo
author: Kyle McLester
description: Adding Quarto to Existing Hugo Project
---
```

4.  To render Quarto markdown to regular markdown, use the following:

``` bash
quarto render $PATH_TO_FILE/$FILE.qmd
```
