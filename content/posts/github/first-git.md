---
author: Kyle McLester
categories:
- Github
date: 2022-02-15
format: hugo
summary: Create repository and make first commit
title: Creating a GitHub Repo
toc-title: Table of contents
---

GitHub is a version control system that allows users to manage and host
their projects. It provides built in functionality for tracking code
changes, conduct code review, create dependency graphs and much more.

## Install Git

``` bash
# ubuntu
sudo apt install git

# mac os w/brew package manager
brew install git 

# windows w/chocolatey package manager
choco install git

# windows w/anaconda
conda install -c anaconda git
```

## Create a New Repository

Assuming you have already signed up for a GitHub account, you should see
something similar to the image shown below. I have covered a few repo's
and accounts that are private but you should still be able to get a
general idea of what the main landing page looks like on GitHub.

![Git Landing Page](/git-landing-page.png)

To create a new repo, select the **New** button in the upper left hand
portion of the screen. This will take you to a new page where you can
fill out the information about your new repo.

![Create new repo](/git-create-new.png)

On this page, you start by entering a repo name. This repo name will be
used to identify your repository and is what you will use when it is
time to download or "clone" your repository. Next, you can select if you
want the repository to be public or private. Private repositories will
not be visible when people visit your public profile.

You can also choose to initialize a README, gitignore, or license at the
same time you create your repository. The README is a markdown file that
is rendered on the main page of your Git Repo. This file will typically
contain instructions, citations, versioning information, etc. -
basically anything you want other people to know, include it in your
README.

You can also select to include a gitignore file - this file lists which
files or folders to not track in your project directory. For example, if
I had a virtual environment setup in my project called `venv`, I could
list the `venv` folder in a gitignore which would prevent it from being
uploaded to GitHub. Lastly, you can select the appropriate license to go
along with your project. We are not going to go into licensing in this
tutorial so I suggest checking out [this
page](https://choosealicense.com) for which license to use. Reminder -
these options are ... optional.

## Cloning Your Repository

Once you have created your Git Repo, you should see something similar to
what is shown in the image below. As you can see, this page contains a
lot of information but we will break it down to the core essentials to
get up and running with GitHub.

![Main repo page](/repo-main-page.png)

To begin adding files, we first need to save this repository to our
local machine. Note - you can upload files by using the `Add file`
button, however I think it is important to get comfortable using the
command line.

Open your respective terminal app and change to the directory where you
want your repo to be saved.

``` bash
# Change directory to my "Documents" folder
cd ~/Documents
```

Now, clone your GitHub repo to this directory using the following
command.

``` bash
# replace url with your repo's url
git clone https://github.com/kmcleste/git-tutorial.git
```

If you are unsure of how to find this url, select the `Code` button on
your GitHub repo page and you will be shown the link to download your
project (see image below).

![GitHub clone url](/git-link.png)

Once this is complete, you should see your repo folder appear in your
directory. Now cd into the repo folder.

![GitHub clone directory](/git-clone.png)

## Adding Files to Repository

To begin adding files, you will first need files to add 😄. So lets
begin by creating a test file to upload. Here I am going to create a new
file called `test.py`. GitHub is pretty language agnostic so it does not
matter what type of file you want to upload - I'm just using python
because that is what I'm familiar with.

``` bash
# create new test file
touch test.py

# edit test file
nano test.py
```

![test.py contents](/nano-test.png)

For this example, I use the built-in nano editor but you can use any IDE
or text editor you like to create this file. Contrary to what many
opinionated people will say online, just use whatever you are
comfortable with.

Now that we have a new file created, we need to prepare it to be sent to
GitHub. For this, we must first **add** our changes/files. To do this,
simply run:

``` bash
# you can either use the period (add all files)
# or type a specific file path
git add .
```

We can check to make sure our changes have been added by returning the
status of our git repo.

![git status](/git-status.png)

Notice how the output shows that we have staged changes for our new file
called **test.py**. At this point, if we changed our mind and no longer
want to include these changes, we could revert the changes by restoring
the staged file(s).

Let's assume for this demo that we are happy with the file we have
created and want to keep the changes made.

You can now **commit** your changes to the repository by running:

``` bash
git commit -m "created test.py"
```

The `-m "created test.py"` is called a commit message and is required
for committing changes to a repo. Always try to include an informative
message that indicates what you changed or why you changed it.

To update these changes on the GitHub website, push your committed
changes:

``` bash
git push origin main
```

Note - if git requests a username and password, please read the next
section "Configuring Authentication"

In the above command, **origin** refers the directory we are going to
push our changes. By default, git sets your origin to be the url you
provided when you ran git clone. Next, **main** refers to the branch you
would like to push your changes. **Main** is automatically set as the
default branch for a new repository - if you see older tutorials, they
may list **master** as the primary branch, just know these function as
the same thing. Branches are useful for creating different versions or
iterations of a project without having to over-write old editions.

You should now be able to see your new file as shown below.

![GitHub file uploaded](/git-new-file.png)

## Configuring Authentication

When you push/pull to GitHub, you may be prompted for a username and
passsword. GitHub uses **personal access tokens** to authenticate
users - similar to how apple or google handle authentication with "app
specific passwords". To create a personal access token:

1.  Go to GitHub --\> **Settings**
2.  Select **Developer Settings**
3.  Under **Personal access tokens**, select `Generate new token`
4.  Give the token a short description
5.  Set the **Expiration** time
6.  Select the scope for the given token
    -   I typically choose `repo` for personal projects
7.  Click `Generate token` --\> do not close this page
8.  Back in your terminal, run the following:

``` bash
# depending on your system, you will need to run either of these commands
# this will save your access token the next time you are prompted for it
git config --global credential.helper manager-core

git config --global credential.help store
```

9.  Run your git push command again
    -   When prompted, enter your username
    -   For password, copy / paste the personal access token we created
        in step 7
        -   Since it's a password, you will not see any text appear in
            your terminal but this is normal

If you would like to use SSH to connect to repositories, see my other
post: [Git SSH Integration](https://kmclester.com/post/github/git-ssh/)

## TL;DR

1.  Create new git repo
2.  git clone
3.  git add
4.  git commit
5.  git push
    -   setup access token

## Final Comments

There are many more features included with git and GitHub that are not
shown in this tutorial. Being efficient with git is an art of its own.
For now, you should be able to create and add files to a new repository.
Start playing around with making new projects and hosting them on
GitHub; you will eventually find that you may need more functionality
but this should at least give you a good starting point.
