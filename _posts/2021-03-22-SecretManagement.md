---
layout: post-light-feature
title: "Managing KeePass secrets with PowerShell"
description: "Using the Secret Management module to interact with a KeePass vault."
category: articles
tags: [secrets, PowerShell, Vault, KeePass]
---

If you have secrets in a variety of secret management apps then Microsoft have a PowerShell module that should be of interest to you. You can check the full list by running the script ````find-module -tag secret -Repository psgallery | Select-Object name, description | Format-Table -Wrap```` which will show you all the modules that are tagged as working with secrets.

### What is the SecretManagement module?

There is a considerable amount of information about the SecretManagement module [from Microsoft here](https://devblogs.microsoft.com/powershell/secretmanagement-and-secretstore-are-generally-available/) and getting that installed and working isnt the intended purpose of this blog. If you are up and working with the module already then read on. If you are new to the module then check out the resources listed and you should be fine. If not, message me and we can work on a new post here to fill that gap. In short, you need to import the modules with ```Import-Module Microsoft.PowerShell.SecretManagement, Microsoft.PowerShell.SecretStore```, and then get the KeePass module with ```Import-module SecretManagement.KeePass

### Where do we start?

Let's begin by creating a KeePass database (that's their term for what the SecretManagement module would term a vault). 

![New dialog](\images\post_images\2021-03-23_01.png)

Open KeePass and click New. This gives you a couple of configuration screens and then you are presented with a template vault.

![Vanilla KeePass database](\images\post_images\2021-03-23_02.png)

As you can see, by default we get a couple of sample secrets. Normally you might delete those but Those will come in handy in a moment!

Check you have the SecretManagement module and the KeePass modules installed with ```Get-Module secret*``` and you should see these modules

![Three modules needed for this process re shown](https://user-images.githubusercontent.com/2597535/112754285-4b5a1b80-8fd3-11eb-9864-90eb4edf70da.png)

```powershell
# Set path to KeePass file and test it exists
$KeePassDB = "C:\Temp\SecretManagement\DemoDatabase.kdbx"

# set up the value for the VaultParameters parameter
$VParams = @{ Path    = $KeePassDB 
    UseMasterPassword = $true
    MasterPassword    = 'pwd' 
}
# Set a vault name and if it exists then unregister that vault in this session
$VaultName = 'KPVault01'
if (Get-SecretVault -Name $VaultName) { Unregister-SecretVault $VaultName }
# register our chosen vault
Register-SecretVault -Name $VaultName -ModuleName SecretManagement.keepass  -VaultParameters $VParams
```

What does this code segment do? Firstly we set the path of our KeePass file into a variable and then create a hash table variable to pass to the ```RegisterSecretVault``` __VaultParameters__ parameter. The ```Register-SecretVault``` command will only be successful if there is no vault already registered with the name we are using so we set the name and check the results of ```Get-SecretVault```. If this finds a vault then we are unregistering it ahead of us registering our chosen vault for this session. 

If this runs without error then we can run ```Test-SecretVault -Name KPVault01``` and if everything is OK we get True as the result.
![Script runs with success](https://user-images.githubusercontent.com/2597535/112759176-c4fd0400-8fe9-11eb-938e-6da3d1beb494.png)

### Getting secrets from a KeePass file

This is pretty simple to achieve, you just need to provide the vault name and the secret name that you are interested in
```powershell
$sec = Get-Secret -Vault KPVault01 -Name "Sample Entry" -AsPlainText

$sec | Get-Member -membertype property
```
![Details of the secret](https://user-images.githubusercontent.com/2597535/112759569-7fd9d180-8feb-11eb-9cb2-1cdf5b5da965.png)

```powershell
$sec.GetNetworkCredential().password
```
![The password](https://user-images.githubusercontent.com/2597535/112759843-b95f0c80-8fec-11eb-91d6-9fe14d958550.png)



### Adding a new secret to the KeePass file

