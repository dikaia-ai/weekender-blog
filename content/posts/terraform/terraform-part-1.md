---
author: Ryan Wesslen
date: 2022-04-19
format: hugo
summary: Using Terraform to deploy cloud applications
title: Part 1 of Infrastructure as code
---

Terraform is an open source tool to use cloud technologies that uses ``infrastructure-as-code.'' It works for either AWS, Google Cloud, Azure, or Docker, which are the most popular options for cloud services. In this first post, I'll provide details on running your first terraform project to deploy your first Terraform example. In the next post, we'll then use a pre-existing GitHub repository to deploy an instance of [Prodigy](https://prodi.gy/), a python-based annotation tool for creating training and evaluation data for machine learning models.

## Introduction

![](/posts/terraform/img/01-terraform.png)

There are three steps in using Terraform:

1. Write: we’ll **initialize** what we need from our **infrastructure**
2. Plan: we’ll review the **changes Terraform will make** to the infrastructure
3. Apply: Terraform will **provision** resources

We'll review each of these steps below -- but if you're interested in learning more basics about Terraform, watch this video.

<p><a href="https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code?in=terraform%2Faws-get-started&amp;wvideo=mo76ckwvz4"><img src="https://embed-fastly.wistia.com/deliveries/41c56d0e44141eb3654ae77f4ca5fb41.jpg?image_play_button_size=2x&amp;image_crop_resized=960x540&amp;image_play_button=1&amp;image_play_button_color=1563ffe0" width="400" height="225" style="width: 400px; height: 225px;"></a></p><p><a href="https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code?in=terraform%2Faws-get-started&amp;wvideo=mo76ckwvz4">What is Infrastructure as Code with Terraform? | Terraform - HashiCorp Learn</a></p>

## Getting Started with First Terraform Project

To begin, we'll use [Terraform Cloud](https://cloud.hashicorp.com/products/terraform), which enables a browser-based interface for running cloud instances. 

### Step 1

Install [Terraform on your local computer](https://learn.hashicorp.com/tutorials/terraform/install-cli#install-terraform), depending on your operating system.

I'll follow the MacOS instructions using Homebrew.

First, we'll install Hashicorp's tap packages.

```console
$ brew tap hashicorp/tap
```

Then we'll install terraform:

```console
$ brew install hashicorp/tap/terraform
```

Now you can check to see if terraform was installed:

```console
$ terraform -help
Usage: terraform [-version] [-help] <command> [args]

The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.
```

### Step 2

Sign up for a [Terraform Cloud Account](https://app.terraform.io/signup/account?utm_source=cloud_landing&utm_content=offers_tfc). You'll then need to confirm your account via a follow up email after signing up. 

### Step 3

After you confirm your account, you can select "Try an example configuration" to follow the instructions for the initial example.

![](/posts/terraform/img/02-terraform.png)

We'll follow these instructions.

### Step 4

Since we have Terraform installed locally, run:

```console
terraform login
```

This should open a new window in your browser for your to sign into Terraform Cloud. 

![](/posts/terraform/img/03-terraform.png)

You can now create an API key in Terraform Cloud which you can copy/paste back into your terminal. After you provide your API key, you should now see this screen:

![](/posts/terraform/img/04-terraform.png)

### Step 5

Locally, navigate in your terminal to a folder that you want to clone a GitHub repo. For now, we'll use this [GitHub repo](https://github.com/hashicorp/tfc-getting-started) but in a later post we can modify this to a custom GitHub repo.

```console
git clone https://github.com/hashicorp/tfc-getting-started.git
```

### Step 6

After cloning the repo locally, run:

```console
cd tfc-getting-started
./scripts/setup.sh
```

This will run local setup scripts and your terminal should now look like:

![](/posts/terraform/img/05-terraform.png)

You can see the setup of the `backend.tf` file and the creation of the workspace.

## Three parts to Terraform workspace

As mentioned in the beginning, there are three steps for a Terraform workspace.

### terraform init

In the same terminal, you may press any key to run the `terraform init` step. This step writes and initializes the specifications for the infrastructure.  This initializes the back end, plug ins, and creates a lock file. 

![](/posts/terraform/img/06-terraform.png)

### terraform plan

Again, we can press any key to now run `terraform plan`. 

![](/posts/terraform/img/07-terraform.png)

After running, we can then view multiple services like the production database and the load balancer. Notice that these are labeled as "fakeservices" as they are placeholders but would be replaced with specifications to set up cloud database, load balancer, etc. There are total of five services but we'll show the vpc (virtual private cloud), `primary_vpc`. 

![](/posts/terraform/img/08-terraform.png)

But what's also great about Terraform is that it can provide you estimates for costs as shown above.

### terraform apply

In the last step, we can press a key to run the last step of `terraform apply`.

After doing this, you should now see this output:

![](/posts/terraform/img/09-terraform.png)

At the end, you can now see a link to <https://app.terraform.io/fake-web-services> that will provide the following screen of info about the fake web services:

![](/posts/terraform/img/10-terraform.png)

At this point, you can return back to Terraform Cloud to view your workspace via a browser. For example, the workspace would look like this:

![](/posts/terraform/img/11-terraform.png)

## Try at Home

As is listed on the page above, you can try to modify the resources by running this task:

You can change your example configuration to provision more mock resources. See the [Fake Web Services provider documentation](https://registry.terraform.io/providers/hashicorp/fakewebservices/latest) to explore the available resources and configuration options.

1. Try pasting this code in *main.tf* to add a few more servers.
    
    `resource "fakewebservices_server" "server-3" {
      name = "Server 3"
      type = "t2.macro"
    }
    
    resource "fakewebservices_server" "server-4" {
      name = "Server 4"
      type = "t2.macro"
    }`
    
2. Re-run `terraform apply` and view the changes on this page.

## Conclusion and next part

Now you've created your first Terraform workspace!

In the next part of this blog, we'll set up our first real infrastructure using these steps:

1. Set up a real provider and some real resources. You can follow the documentation for cloud providers such as [AWS](https://registry.terraform.io/providers/hashicorp/aws/latest/docs), [GCP](https://registry.terraform.io/providers/hashicorp/google/latest/docs), [Azure](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs), and [more](https://registry.terraform.io/browse/providers).
2. Go to the variables tab in [your workspace](https://app.terraform.io/app) and set up variables with credentials for your chosen cloud provider.
3. Run `terraform apply` and you’re all set!

Good luck learning!