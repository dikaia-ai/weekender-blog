---
title: Getting Started with Rasa
date: 2022-03-01
author: Kyle McLester
summary: 🚧  UNDER CONSTRUCTION
---

[Rasa](https://rasa.com/) is an open source platform that allows you to create enterprise-level chat assistants. You may have experience with simple rule-based dialogue systems, however; Rasa takes it one step further by using NLP and NLU to understand the context in which user utterances are provided, and execute some function based on this given input.

The theory behind why you would want to create an automated chatbot vs speaking to a live agent is an entirely different discussion...there are definitely many opinions on this.

In this short post, I will take you through creating your first Rasa project and give a brief explanation of the different parts that make Rasa function.

For more detailed information, I suggest checking out Rasa's [YouTube channel](https://www.youtube.com/c/RasaHQ/featured) and [documentation](https://rasa.com/docs/rasa/).

## Preparation

1. Create a new project folder and open the directory

   ```bash
   mkdir $PROJECT_FOLDER
   cd $PROJECT_FOLDER
   ```

2. Create and activate a new virtual environment
  
   ```bash
   virtualenv $ENV_NAME
   source $ENV_NAME/bin/activate
   ```

   For more info, see related post: [Python Virtual Environments](https://kmclester.com/posts/python/venv/)

3. Create git repository in project folder and add your virtual environment to .gitignore (optional)

## Installing Rasa Dependencies

1. Install Rasa Open Source

   ```bash
   python3 -m pip install rasa
   ```

2. Confirm Installation

    ```bash
    rasa --version
    ```

{{<collapse summary="If you have any trouble with install, check here for possible solutions">}}

When I first tried using Rasa, I ran into several errors pertaining to ssl, xml, developer versions, and requests. I believe these are all fixed in the latest version of Rasa (3.0.8), but just in case, these are some of the things that worked for me:

- Installing/updating packages with errors

    ```bash
    # Linux
    sudo apt-get install pkg-config libssl-dev libffi-dev python3-dev libxml2-dev libxmlsec1-dev libxmlsec1-openssl

    # MacOS
    brew install openssl
    brew install libffi
    python3 -m pip install lxml
    brew install libxmlsec1
    ```

- This should only apply to those using Rasa X but as of the time of writing this you need `requests` version 2.25.0

   ```bash
   python3 -m pip install requests==2.25.0
   ```

Let me know if you are unable to get it working and I will try to assist.

{{</collapse>}}

## Creating First Rasa Project

1. Within your root project directory, run:

    ```bash
    rasa init
    ```

    - Enter the path where the project will be created; the default is the current directory
      - If the directory is not empty, the system will ask if you would like to continue. If you type 'y' or 'n', it will automatically move to the next step without asking for confirmation.
    - Do you want to train an initial model? I suggest selecting 'no' for the time being. We will train a model in later steps.

    This function will create a basic Rasa project using the **Mood Bot** example -- think of this like a **Hello World** project.

2. Below is an example of the folder structure and contents you should see after you have initialized your project. Expalanations of each file will be listed in later steps.

    ```bash
    (venv) ➜ kyle@ubuntu~$ tree -I venv
    .
    ├── actions
    │   ├── __init__.py
    │   └── actions.py
    ├── config.yml
    ├── credentials.yml
    ├── data
    │   ├── nlu.yml
    │   ├── rules.yml
    │   └── stories.yml
    ├── domain.yml
    ├── endpoints.yml
    └── tests
        └── test_stories.yml
    ```

## Training a Model

Rasa makes it very easy to begin training a model, simply run:

```bash
rasa train
```

## Interacting With Your Chatbot

There are several ways to speak to your now-trained chatbot. The most basic of these is through `rasa shell`:

```bash
rasa shell -m models
```

The `-m models` in the above code is explicitly telling Rasa to check our models directory for a trained language model -- 'models' is the default folder so this is not required but it can be useful to state in some cases.

You should see something similar to the image below show up in your terminal. Now you can begin talking to your chatbot!

![rasa shell example](/posts/rasa/rasa-shell.png)