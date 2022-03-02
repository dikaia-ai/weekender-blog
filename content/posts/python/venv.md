---
author: Kyle McLester
date: 2022-02-28
format: hugo
title: Python Virtual Environments
categories:
    - Virtual Environments
tags:
    - venv
    - virtualenv
    - environments
summary: Create virtual environments for code reproducibility and easy sandboxing of python packages
---

Have you ever gone to follow a python tutorial and realized that nothing seems to work - or that you get seemingly endless package errors? This is where **virtual environments** can come in handy.

Virtual environments (venv or virtualenv) allow us to create a sandbox for our python environment. We can install, update, upgrade packages in these environments without impacting the rest of the packages already installed on our system.

This also aids the function of **reproducibility** -- say you have a project that uses a package which is dependent on python2.7 but your system is running python3.8, what do you do? Instead of creating aliases or weird path variables, you can simply use a virtual environment and load whatever version of python you need. This goes for packages as well.

## Installing Virtualenv

There are several virtual environment tools but I will be showing you **virtualenv**. To install this on Linux or MacOS, run the following:

```bash
# Linux
sudo apt-get install virtualenv

#MacOS
brew install virtualenv
```

You can confirm this was installed by entering:

```bash
which virtualenv
```

## Creating a Virtual Environment

Change to your project directory and run the following:

```bash
cd my-project/
virtualenv venv
```

The above code will create a virtualenv called `venv`. This will usually appear as a new folder in your project directory with the same name.

If you would like your virtual environment to inherit globally installed packages, you can run:

```bash
virtualenv venv --system-site-packages
```

## Activate Your Environment

You may have noticed this new `venv` folder appear but you are not able to access your packages - this is because you have to **activate** your virtual environment. This can be done by running the following:

```bash
source venv/bin/activate
```

You will know `venv` is active when your terminal shows the environment name:

```bash
(venv) kyle@ubuntu:~$
```

The above will run the activation script from within the `venv` folder. Keep in mind that if you are not running this from your root project directory, that you will need to replace the word `venv` with your entire directory path.

Now you are free to `pip install` packages and they will be saved in the `venv` environment.

{{<collapse summary="Reproducibility Tip" >}}
Once you have finished installing all of your packages, to create a full list of all the package and version information, run the following:

```bash
pip freeze > requirements.txt
```

This function will create a file with the structure `package_name`==`version`:

```bash
scipy==1.8.0
sentence-transformers==2.2.0
sentencepiece==0.1.96
seqeval==1.2.2
smmap==5.0.0
sniffio==1.2.0
spacy==3.2.2
spacy-legacy==3.0.8
spacy-loggers==1.0.1
SPARQLWrapper==1.8.5
SQLAlchemy==1.4.31
SQLAlchemy-Utils==0.38.2
sqlparse==0.4.2
```

You can then reinstall these packages using the requirements.txt file - or send it to a friend that wants to recreate your project. This can be done by running:

```bash
python3 -m pip install -r requirements.txt
```

{{</collapse>}}

## Deactivate your Environment

When you are finished using the environment, you can **deactivate** it by running:

```bash
deactivate
```

## TL;DR

- To install -> `python3 -m pip install virtualenv`
- To create -> `virtualenv venv`
- To activate -> `source venv/bin/activate`
- To deactivate -> `deactivate`

For further reading, I would suggest looking at [this post from Peter Baumgartner](https://www.peterbaumgartner.com/blog/python-virtual-environment-package-workflow/) on creating python packages.

## Closing

Thanks for reading. Please reach out through the [contact page](https://kmclester.com/contact/) if you have any questions or concerns - I really appreciate feedback. Also, share this with anyone who might find it useful!
