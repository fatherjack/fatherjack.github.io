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

The cause was that I had recently changed my logon password and had not restarted SSMS.
