---
layout: post
title: "Error connecting to SQL Server - Error: 18452"
description: "Getting failed logins on SQL Server Availability Group"
category: articles
tags: [SSMS, Login, SQL Server, Error, 18452]
---

I recently had a curious case of misleading login errors.

When I attempted to connect to a SQL Server instance hosting an Availability Group (AG) replica I connected without error via SSMS. However, when trying to connect via the AG Listener I was getting the error

 > Error connecting to [AGListenerName]

 > additional information:
  Login failed. The login is from an untrusted domain and cannot be used with Windows authentication. (Microsoft SQL Server, Error: 18452)

![image](https://user-images.githubusercontent.com/2597535/177162526-324526e5-5672-465a-acea-681b72c4674c.png)

The [official documentation](https://docs.microsoft.com/en-gb/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error) was not helpful and sadly this isn't an 18456 error so we can't access the excellent content from Aaron Bertrand on [Troubleshooting Error 18456](https://sqlblog.org/2020/07/28/troubleshooting-error-18456) which covers all the failed login permutations of Error State and Error Severity.

Noone else was suffering this problem and after a little consideration of anything security related that I had witnessed recently I realised that the cause was that I had recently changed my domain logon password and had not restarted SSMS. A quick restart of SSMS (I actually restarted my jump server just to give it a refresh too!) andeverything was back to normal.
