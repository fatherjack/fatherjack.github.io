---
layout: post-light-feature
title: "Managing KeePass secrets with PowerShell"
description: "Using the Secret Management module to interact with a KeePass vault."
category: articles
tags: [secrets, PowerShell, Vault, KeePass]
---


If you have secrets in a variety of secret management apps then Microsoft have a PowerShell module that should be of interest to you. You can check the full list by running the script ````find-module -tag secret -Repository psgallery | Select-Object name, description | Format-Table -Wrap````.
