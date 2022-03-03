---
title: 'Getting Started with Rasa - Part 1'
date: 2022-03-01
author: Kyle McLester
summary: Creating your first conversational ai assistant using Rasa Open Source
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

You can also run Rasa in a server configuration using the following command:

```bash
rasa run -m models --enable-api --cors "*"
```

This will allow you to make API calls to your Rasa server, which returns a formatted json object as a response. This is most likely the option you will use if you decide to connect your chatbot to any sort of front end components like a chat widget. There are several other methods for deploying your chatbot but I will talk about those in **Part 2**.

{{<collapse summary="Some useful runtime options">}}

1. `-p $PORT, --port $PORT`
   - Default rasa server port = 5005
2. `--cors “*”`
    - Whitelist all origins using *
    - Default = None
3. `--enable-api`
    - Default = False
    - Starts a web server API in addition to the input channel
4. `--ssl-certificate $SSL_CERT_FILE_PATH`
    - Default = None
    - Creates a TLS secured server
5. `--ssl-keyfile $SSL_KEY_FILE_PATH`
    - Default = None
    - Creates a TLS secured server
6. `--ssl-password $SSL_PASSWORD`
    - Default = None
    - If your ssl-keyfile is protected by a password
{{</collapse>}}

## Configuration

Rasa uses `yaml` files for configuring settings, pipelines, etc. If no configuration is provided, the default configuration is loaded:

```yaml
# The config recipe.
# https://rasa.com/docs/rasa/model-configuration/
recipe: default.v1

# Configuration for Rasa NLU.
# https://rasa.com/docs/rasa/nlu/components/
language: en

pipeline:
# # No configuration for the NLU pipeline was provided. The following default pipeline was used to train your model.
# # If you'd like to customize it, uncomment and adjust the pipeline.
# # See https://rasa.com/docs/rasa/tuning-your-model for more information.
  - name: WhitespaceTokenizer
  - name: RegexFeaturizer
  - name: LexicalSyntacticFeaturizer
  - name: CountVectorsFeaturizer
  - name: CountVectorsFeaturizer
    analyzer: char_wb
    min_ngram: 1
    max_ngram: 4
  - name: DIETClassifier
    epochs: 100
    constrain_similarities: true
  - name: EntitySynonymMapper
  - name: ResponseSelector
    epochs: 100
    constrain_similarities: true
  - name: FallbackClassifier
    threshold: 0.3
    ambiguity_threshold: 0.1

# Configuration for Rasa Core.
# https://rasa.com/docs/rasa/core/policies/
policies:
# # No configuration for policies was provided. The following default policies were used to train your model.
# # If you'd like to customize them, uncomment and adjust the policies.
# # See https://rasa.com/docs/rasa/policies for more information.
  - name: MemoizationPolicy
  - name: RulePolicy
  - name: UnexpecTEDIntentPolicy
    max_history: 5
    epochs: 100
  - name: TEDPolicy
    max_history: 5
    epochs: 100
    constrain_similarities: true
```

When you are first starting out, the default configuration is usually sufficient for getting decent results. Of course these settings can be tinkered with and fine-tuned but its not absolutely necessary out of the box - which is nice. For more information on setting up a custom configuration, I suggest checking out [this page](https://rasa.com/docs/rasa/components) from Rasa.

## Domain

The `domain.yml` file is used to define the "universe in which your assistant operates." It specifies intents, entities, slots, responses, forms, and actions your bot should know about.

Here's the example `domain.yml` file that comes with the default Mood Bot:

```yaml
version: "3.0"

intents:
  - greet
  - goodbye
  - affirm
  - deny
  - mood_great
  - mood_unhappy
  - bot_challenge

responses:
  utter_greet:
  - text: "Hey! How are you?"

  utter_cheer_up:
  - text: "Here is something to cheer you up:"
    image: "https://i.imgur.com/nGF1K8f.jpg"

  utter_did_that_help:
  - text: "Did that help you?"

  utter_happy:
  - text: "Great, carry on!"

  utter_goodbye:
  - text: "Bye"

  utter_iamabot:
  - text: "I am a bot, powered by Rasa."

session_config:
  session_expiration_time: 60
  carry_over_slots_to_new_session: true
```

For more information, check out Rasa's [domain documentation](https://rasa.com/docs/rasa/domain).

## Training Data

There are a series of different training data that Rasa uses to make a well-rounded chatbot. This data is usually stored in the ... you guessed it, `data` folder. It consists of NLU data, Stories, and Rules.

The NLU data provides examples of user utterances for a given intent (see code below for example). This data is what helps the model associate certain words and phrases with different actions or responses.

Rules are used to train the chatbots dialogue management model. These Rules describe short pieces of conversations that should **always** follow the same path (see code below).

Stories are similar to rules but are not set in stone, so to speak. Stories allow the assistant to generalize unseen conversation paths (again, code ⬇️).

```yaml
version: "3.0"

nlu:
- intent: greet
  examples: |
    - Hey
    - Hi
    - hey there [Sara](name)

- intent: faq/language
  examples: |
    - What language do you speak?
    - Do you only handle english?

stories:
- story: greet and faq
  steps:
  - intent: greet
  - action: utter_greet
  - intent: faq
  - action: utter_faq

rules:
- rule: Greet user
  steps:
  - intent: greet
  - action: utter_greet
```

## Intents & Entities

Intents and Entities are something you will work with very often in the Rasa ecosystem. Along with slots (these will be discussed in another post), intents and entities are largely what control conversation with your chat assistant.

[Intents](https://rasa.com/docs/rasa/domain/#intents) are nearly as they sound - they are what the user **intends** to do or convey. For example, if someone says 'hello' then we could classify the intent as 'greeting'; or if the assistant received 'are you a bot' then the intent would be bot_challenge. You can see these intents listed in your NLU, Rules, Stories, and Domain files.

[Entities](https://rasa.com/docs/rasa/domain/#entities) are structured pieces of information inside a user message. They could be people, places, things, etc. - basically anything that can be extracted from a message and either used in a response or passed on to an action.

## Closing

This should at least get you started and give you a base-level understanding of Rasa and its components. There will likely be more parts to follow - my goal is to cover nearly all of Rasa's documentation in the coming weeks.

In the mean time, if you have any questions or concerns, please reach out through the [contact page](https://kmclester.com/contact/).
