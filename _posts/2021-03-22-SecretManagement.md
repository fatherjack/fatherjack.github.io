---
layout: post-light-feature
title: "Managing KeePass secrets with PowerShell"
description: "Using the Secret Management module to interact with a KeePass vault."
category: articles
tags: [secrets, PowerShell, Vault, KeePass]
---

If you have secrets in a variety of secret management apps then Microsoft have a PowerShell module that should be of interest to you. You can check the full list by running the script ````find-module -tag secret -Repository psgallery | Select-Object name, description | Format-Table -Wrap```` which will show you all the modules that are tagged as working with secrets.

### What is the SecretManagement module?

There is a considerable amount of information about the SecretManagement module from Microsoft here (...) and getting that installed and working isnt the intended purpose of this blog. If you are up and working with the module already then read on. If you are new to the module then check out the resources listed and you should be fine. If not, message me and we can work on a new post here to fill that gap.

### Where do we start?

Let's begin by creating a KeePass database (that's their term for what the SecretManagement module would term a vault). 

![New dialog](\images\post_images\2021-03-23_01.png)

Open KeePass and click New. This gives you a couple of configuration screens and then you are presented with a template vault.

![Vanilla KeePass database](\images\post_images\2021-03-23_02.png)

As you can see, by default we get a couple of sample secrets. Normally you might delete those but Those will come in handy in a moment!

Check you have the SecretManagement module installed with ```Get-Module``` and you should see