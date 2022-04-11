---
title: Google Cloud Platform
date: "2022-02-21"
author: Kyle McLester
categories:
    - Cloud Services
tags:
    - cloud services
    - google cloud
    - virtual machine
    - web hosting
    - compute engine
summary: Create a GCP Virtual Machine for Web or App Hosting
---

Creating a cloud-based platform doesn't have to be difficult. Google's Cloud Platform (GCP) has created a variety of tools and services that allow users to quickly get up and running hosting their own apps, servers, and more. I am going to walk you through the process of creating a cloud-based virtual machine and connecting via SSH.

## Accessing GCP

Creating an account with GCP is as simple as logging in with your gmail account. If you do not have a gmail account, you **will** have to create one to continue - use [this link](https://accounts.google.com/signup/v2/webcreateaccount?hl=en&flowName=GlifWebSignIn&flowEntry=SignUp) to create yours now.

Now, head to the [Google Cloud Platform](https://cloud.google.com) and [sign in](https://cloud.google.com/_d/signin?continue=https%3A%2F%2Fcloud.google.com%2Fgcp%3Futm_source%3Dgoogle%26utm_medium%3Dcpc%26utm_campaign%3Dna-US-all-en-dr-bkws-all-all-trial-e-dr-1011347%26utm_content%3Dtext-ad-none-any-DEV_c-CRE_575460480412-ADGP_Desk%2520%257C%2520BKWS%2520-%2520EXA%2520%257C%2520Txt%2520~%2520Google%2520Cloud%2520Platform%2520Core-KWID_43700068256543269-aud-1438830666471%253Akwd-26415313501%26utm_term%3DKW_google%2520cloud%2520platform-ST_google%2520cloud%2520platform%26gclid%3DEAIaIQobChMIy-rVlbaS9gIVlc13Ch1UeAodEAAYASAAEgKPyPD_BwE%26gclsrc%3Daw.ds&prompt=select_account) .

Once you have logged in, you will be taken to the main landing page. From here, click `Go to console`:

![Google Cloud Platform landing page](/posts/gcp/landing-page.png)

## Creating a Project

If you have never created a project before, you will likely be presented with this window, select `NEW PROJECT`:

![Create new google cloud platform](/posts/gcp/new-project.png)

Fill in the proper details for your project and click `CREATE`. If you do not have an organization, select `No Organization`.

![Create new project](/posts/gcp/new-project-details.png)

{{<blockquote>}}**NOTE** - you **will** have to link a billing account if you wish to use the services going forward, however you do receive a free $300 credit when you sign up for the 90-day free trial. If you do not upgrade to the paid account, you will **not** be charged when your free credit's run out. You will have 30 days to upgrade to a paid account or your resources will be lost. For more information on Google's billing rules and procedures, visit https://cloud.google.com/free/docs/gcp-free-tier.{{</blockquote>}}

It will take a few minutes for your project to initialize but once it does, you will be taken to the GCP Project Dashboard.

![GCP Project Dashboard](/posts/gcp/project-dashboard.png)

If you are **not** taken to the dashboard or if you have multiple projects, you can always select your project using the drop-down in the top left.

![Select active project](/posts/gcp/set-active-project.png)

## Compute Engine and Virtual Machines

To begin creating a virtual machine (cloud server), you will have enable the **Compute Engine**. This is done by selecting the three-lines (hamburger icon) next to the GCP logo to open your **Navigation Menu**. Now select the **Compute Engine** option. You can pin this option for quick access in the future by hovering over the item and selecting the pin icon.

![Navigation menu](/posts/gcp/navigation-menu.png)

Next, enable the compupte engine service by selecting `ENABLE`. This may take a few minutes:

![Enable compute engine](/posts/gcp/activate-compute-engine.png)

Once the service is enabled, you will be brought to the Compute Engine dashboard. Here you will find a list of your running vm's and a variety of management options. Feel free to explore these options but once you're ready, select `CREATE INSTANCE`.

![Compute Engine Dashboard](/posts/gcp/compute-engine-dash.png)

For this demo, we will create a vm with the following specifications:

![vm options 1](/posts/gcp/vm-options-1.png)
![vm options 2](/posts/gcp/vm-options-2.png)

To change your boot disk and OS properties, select `CHANGE` and choose the most appropriate options for your project. I am using Ubuntu 20.04 LTS with 50gb of storage for this demo. Whenever you are done selecting your options, click `CREATE`. This may take a few minutes. Once the initialization is complete, you will see your active vm in the **VM Instances** list on the Compute Engine dashboard.

![vm-active](/posts/gcp/vm-active.png)

## Connecting to Your Instance

From the **Compute Engine** dashboard, find your running vm instance and click `SSH` under the **Connect** column. This will automatically transfer the necessary authentication keys to the vm instance. Once the connection is ready, you will see a terminal window that is now connected to your vm.

![SSH terminal](/posts/gcp/ssh-terminal.png)

As with most OS installs, it is usually a good idea to update and upgrade once everything is up and running. To do so, run the following commands in your ssh terminal:

```bash
sudo apt update
sudo apt upgrade
```

You can now use your vm instance as a normal server/computer via the terminal. You can also install the [GCloud CLI](https://cloud.google.com/sdk/docs/install) tools to connect using your computers default terminal app.

## Removing VM Instance

If you've decided you no longer need your vm, you first need to stop the vm via the **Compute Engine** dashboard. This is done by clicking the three vertical dots in the instances list, and selecting `stop`. As with the other operations, this will take a minute.

![stopping a vm](/posts/gcp/stop-vm.png)

Once the instance is stopped, you can now delete the vm through the same menu we used to stop it.

## Removing GCP Project

If you need to delete your project, head back to the project dashboard by clicking the Google Cloud Platform logo in the upper left of the screen. From here, select `Go to project settings`.

![go to project settings](/posts/gcp/project-settings.png)

Now select `SHUT DOWN` from the top options bar. In the resulting window type your project ID and click `SHUT DOWN`. This will stop all billing, revoke access to the project, and delete all resources. You have 30 days to attempt to recover this process if it was done accidentally.

![shutdown project](/posts/gcp/shutdown-project.png)

## Closing

If you have any questions or concerns, please feel free to reach out using the [contact page](https://weekenddatascientist.netlify.app/contact/). Please share with anyone that might find this useful!!
