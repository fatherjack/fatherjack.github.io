---
layout: post
title: "New-Variable"
description: "Using built-in commands to manage variables"
category: articles
tags: [variables, powershell]
---

# Why bother to use New-Variable?

Something pretty early on in your scripting experience you will have encountered is the assignment of a value to a variable. Most languages are pretty flexible in this and will allow some simple syntax like ```var = value```. This sets a value that you want to reuse into memory and you access it again my making reference to the variable name that you chose. PowerShell is no different, running the command ```$Var = "Monday"``` will set the value *Monday* into memory and it can be recalled and used whenever you need it by using the variable name *$Var1* in your script. This is pretty neat, compact, and simple to understand.

With this being so simple and probably suitable for most needs it isn't often that people use New-Variable to do the same thing. In fact many people dont even know that there are a whole set of functions that work with variables. Let's run a piece of code to find those commands. Run the command ```Get-Command -noun Variable``` and you will get results like below
![image](https://user-images.githubusercontent.com/2597535/114295546-c88a9380-9a9d-11eb-9b77-4fdc7ae920af.png)

Logically this all starts with *New-Variable* so let's take a look at a couple of the parameters that this command has. 

```-Name``` is pretty self explanatory and is mandatory - how else would we know how to get our data back ?!
```-Value``` again, it's what we want to store so it's mandatory
```-Description``` well that's new. Perhaps we could add extra value to our code if we add a description of what a variable contains in addition to its name.
```-Option``` this is a mysterious and intriguing parameter and warrants further investigation

Using New-Variable is simple but take a close look, we don't use a ```$``` in front of the variable name when we use any of these commands.
```powershell
New-Variable -Name StartDate -Value (Get-Date) -Description "To keep the date and time that we started"
```
There is no output from the command but if we reference the variable, with the leading ```$``` then we see the date has been stored as we hoped.
![image](https://user-images.githubusercontent.com/2597535/114296760-f45d4780-9aa4-11eb-885f-dafad264f347.png)
