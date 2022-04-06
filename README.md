# Personal Blog

This repository holds the code for our Weekend Data Science blog, feel free to check it out here: https://weekenddatascientist.netlify.app/

This website is created using Markdown, Hugo, and Quarto. It is then hosted on Netlify.

# To collaborate

To clone this repo, it is recommended that you first [install quarto](https://quarto.org/docs/get-started/).

Next, you'll need to decide on what IDE you want to use. We recommend [Visual Studio](https://quarto.org/docs/get-started/hello/vscode.html). One reason is that VS plays better across multiple languages like R, Python, Julia, and JavaScript. Visual Studio has an [extension for quarto](https://marketplace.visualstudio.com/items?itemName=quarto.quarto) as well.

Third, you'll need to clone the repo locally. If you're using Visual Studio, we recommend creating a new project [via a GitHub repo option](https://docs.microsoft.com/en-us/visualstudio/get-started/tutorial-open-project-from-repo?view=vs-2022).

Alternatively, you can run:

```bash
git clone https://github.com/dikaia-ai/weekender-blog.git
```

After you have cloned, you'll need to install the blog's theme (PaperMod). [Here are instructions](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation). We recommend in the root of your directory running these commands:

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
git submodule update --remote --merge
```


