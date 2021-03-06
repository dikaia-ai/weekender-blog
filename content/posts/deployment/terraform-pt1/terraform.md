---
title: Getting Started with Terraform (pt. 1)
date: "2022-04-10"
author: Kyle McLester
summary: Gain a basic understanding of "Infrastructure as Code" (IaC) using Terraform
bibliography: /Users/kmclester/Documents/weekender-blog/content/posts/deployment/terraform-pt1/citations.bib
format: hugo
---



Infrastructure as Code or **IaC** is the process of managing and
provisioning computer data centers through machine-readable definition
files, rather than physical hardware configuration or interactive
configuration tools (Wittig, Wittig, and Whaley 2016).

One such example of an **IaC** tool is
[Terraform](https://www.terraform.io/). Terraform enables you to quickly
deploy and manage infrastructure from hundreds of providers and codifies
cloud API's into declarative configuration files. This declarative
phrasing is important -- traditional deployments follow procedural
paths, meaning they must be executed step by step and it a particular
order. A declarative approach allows us to set an end-goal, and
terraform will fill in the necessary blanks to fulfill said declaration.
It will feel a bit wrong if you come from a traditional background but
bare with me -- there's some really cool things Terraform enables us to
do.

## Installing Terraform

Another great thing about Terraform is that it installs as a 50mb binary
file -- and that's all it needs to run.

Depending on your system, run the appropriate command(s) below:

``` bash
# macOS
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

``` bash
# Linux
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install terraform
```

``` bash
# Windows/FreeBSD/OpenBSD/Solaris
# download binary file from website: https://www.terraform.io/downloads
```

To confirm Terraform has been installed correctly, head to your terminal
and try:

``` bash
terraform --version
```

You should see the following, if you see something other than the
version number, try reinstalling:

``` bash
Terraform v1.1.8
on darwin_amd64
```

## Getting Oriented with Terraform

The main file type you will be working with uses the extension **.tf**.
Terraform files are relatively straight forward and are plain text files
that follow a json-type format. An example file is shown below:

    provider "aws" {
        # if you don't fill anything, terraform will use environment vars
    }

    resource "aws_vpc" "first-vpc" {
        cidr_block = "10.0.0.0/16"
        tags = {
            Name = "production"
        }
    }

    resource "aws_subnet" "subnet-1" {
      vpc_id     = aws_vpc.first-vpc.id
      cidr_block = "10.0.1.0/24"

      tags = {
        Name = "prod-subnet"
      }
    }

    resource "aws_instance" "first-server" {
      ami           = "ami-04505e74c0741db8d"
      instance_type = "t2.micro"
      tags = {
          Name = "ubuntu"
      }
    }

Don't worry about the content of this file yet, just know this is what
they look like.

### Main Commands

The primary functions you will use in the next few examples will be
**init**, **plan**, **apply**, and **destroy**.

**Init**: Creates the necessary state and lock files used by Terraform,
and downloads any designated plugins.

**Plan**: Generates a report showing what resources will be created,
changed, or destroyed given the current configuration

**Apply**: Generates a similar report as **Plan** but executes the plan.
This will provision resources according to your configuration.

**Destroy**: Removes all of the resources you have provisioned in the
configuration file. You will not use this very often.

## Terraform and AWS

As you can see by the title we will be using AWS for this demo. Head on
over to the [sign-up
page](https://portal.aws.amazon.com/billing/signup#/start/email) and
create an account. Everything I show here will fall under the free tier
of AWS, and if you are charged anything, it will at most be \~ \$1.

### Create Access Key

From the AWS Console, select your username in the top right and then
**Security credentials**:

![AWS username -\> security
credentials](/posts/deployment/terraform-pt1/aws-options.png)

Then select **Create New Access Key**:

![Create new access key](/posts/deployment/terraform-pt1/create-key.png)

This will automatically generate a new access key. For security, the
private key is only shown once -- so be sure to store it in a secure
location at this time. For this demo, also be sure to download the key
and remember where it is stored.

![Private access key](/posts/deployment/terraform-pt1/key.png)

Note - this access key has already been deleted. Use your own key.

### Create Key Pair

1.  From the AWS Console, search for the **EC2** service. Select it.
2.  Select **Key Pairs** from the left-hand menu (under Network and
    Security).
3.  Select **Create key pair** in the top-right. This will take you to a
    new screen to fill in key information.
    -   Enter a key name and choose a key pair type. Note - key type
        **ED25519** can only be used with Linux and Mac instances.
    -   Then select the private key format -- since I am using macOS and
        plan to use SSH, I selected the ".pem" option. I would suggest
        using the ".ppk" format if you plan to use PuTTY.
4.  Upon selecting "Create key," you will be prompted to download the
    key. Save this in a secure location. We will use it later.

## Part 2

That's enough setup for now. You should have successfully created an AWS
account, created access and key pairs, and have a very high level
understanding of what IaC is. I know we didn't get to actually use
Terraform in this section but that's where we will pick up in Part
2...so I will see you there!

------------------------------------------------------------------------

## Sources

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-wittig_wittig_whaley_2016" class="csl-entry">

Wittig, Andreas, Michael Wittig, and Ben Whaley. 2016. *Amazon Web
Services in Action*. Manning.

</div>

</div>
