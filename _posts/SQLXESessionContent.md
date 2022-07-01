---
layout: post
title: "Capturing SQL Server Extended Event data to file"
description: "Getting collected event data from an XE session into a csv file"
category: articles
tags: [powershell, jobs, SQL Server, Extended Events]
---

[TLDR - just show me how you did this](#solution)

## Background
I have a SQL Server Extended Event (XE) session running on a server that I manage which is only logging event to the ring_buffer, ie its in memory, not in a file on disk, but I want to collect the event information in to a file occassionally. My first reaction then is to turn to dbaTools to see if there is a command for this.

```powershell
Get-Command -module dbatools *XE*
```
The results are 33, yes THIRTY THREE, commands!! but there are some rogue results in there ( I see you 'Get-DbaQueryExecutionTime' and 'Get-DbaSsisExecutionHistory'), let's modify that a little and see if it gets better ...
```powershell
Get-Command -module dbatools *-dbaXE*
```
Ugh, still got 29 candidates!. Perhaps we can immediately reject commands with verbs like Convert, Import, Invoke, New, Start, and Stop ...
```powershell
Get-Command -module dbatools *-dbaXE*|? verb -notin ('Convert', 'Import', 'Invoke', 'New', 'Start', 'Stop')
```
Down to 17 now, so I started reading the documentation at [https://docs.dbatools.io/](https://docs.dbatools.io/) and frankly got myself in a muddle. I didn't find the content clear about what some commands do - whether they relate to XE session config or the output of the sessions etc. so I resorted to asking on Twitter. Chrissy LeMaire [@cl](http://twitter.com/cl) very kindly pointed me in the right direction by suggesting I use [Watch-DbaXeSession](https://docs.dbatools.io/Watch-DbaXESession), and specifically Example 2. which is 
```powershell
Watch-DbaXESession -SqlInstance sql2017 -Session system_health | Export-Csv -NoTypeInformation -Path C:\temp\system_health.csv
```
This seems to make sense but the help line underneath that gave me a reason to pause and think "Exports live events to CSV. Ctrl-C may not not cancel out of it - fastest way is to stop the session." - I didn't want to have to Ctrl + C my PS command or exit my PS session or stop the XE session on the server...

## <a name='solution'>Solution</a>

By the time I had returned to my work laptop (the Twitter exchange happened after hours) I had an idea to try out
```powershell
Start-Job -Name XEWatcher -ScriptBlock { 
    Watch-DbaXESession -SqlInstance sql2017 -Session system_health | Export-Csv -NoTypeInformation -Path '\\FileShare\Path\system_health.csv' -Append
} 
```
This started the Watch-DbaXeSession but running it in a job means its out of my face and I can continue coding whatever I am working on. As it observes a captured event the data is passed down the pipeline to the Export-Csv command which drops the data into my csv file. 

When I have had enough information exported I can run 
```powershell
Stop-Job XEWatcher

Remove-Job XEWatcher
```
This frees up the csv file for other app access.

I havent monitored the effect of leaving this run for a long time so I dont recommend it, just to preserver your resources and not experience an issue.

