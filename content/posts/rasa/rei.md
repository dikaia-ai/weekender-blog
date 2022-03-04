---
title: 'Rasa Ephemeral Installer'
date: 2022-03-03
author: Kyle McLester
summary: 🚧 UNDER CONSTRUCTION - Use the Rasa Ephemeral Installer to deploy to a Google Cloud server; gain access to useful features like Rasa X and GitHub integration
---

The Rasa Ephemeral (REI) method of installation grants users access to the Rasa X feature. Rasa X is a web interface that allows developers to rapidly build, test, and push chat assistants to a Git repository. Within Rasa X you can label entities, flag conversations for review, share your chatbot with testers and more.

For this demonstration I will be deploying REI to a Google Cloud Platform instance - if you do not already know how to create a GCP instance, check out this post first: [Getting Start with GCP](https://kmclester.com/posts/gcp/gcp-vm/)

## Getting Connected

First you must SSH into your GCP instance. This can either be done through the GCP Compute Engine instances list, or you can connect using GCloud CLI tools with the following command:

```bash
gcloud config set project $PROJECT_NAME
gcloud compute ssh $USER_NAME@$INSTANCE_NAME
```

## Download and Install REI

Run the following command to download the REI installation script and execute the script:

```bash
curl -O https://rei.rasa.com/rei.sh && bash rei.sh -y
```

If you're using a fresh vm instances, the script will start by installing docker - once this is complete, you will be prompted to reboot. You can reboot the system from the CLI with:

```bash
sudo reboot
```

Once you have rebooted, run the installation script again:

```bash
bash ./rei.sh -y
```

## Create Values Config File

To configure the rasactl cluster (based on kubernetes), create a file called `values.yml`

```bash
touch values.yml
```

In the this file, add the following information:

```yaml
rasa:
  versions:
    rasaProduction:
      enabled: true
    rasaWorker:
      enabled: true

# if you are using a custom action image, include the following:
app:
    name: "<name of your action server image>"
    tag: "<tag of your image>"
```

## Starting Rasa Instance

Using `rasactl`, create a new instance and point to the values file we just created:

```bash
rasactl start $INSTANCE_NAME --values-file ~/values.yml
```

If this is the first instance you're creating, this will likely take several minutes to deploy.
