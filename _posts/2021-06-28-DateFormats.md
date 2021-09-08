---
layout: post
title: "Working with dates in non-standard formats"
description: "How to handle dates that dont conform to normal formats."
category: articles
tags: [powershell, date, datetime, standard]
---
It's common to have to handle dates when working with PowerShell and especially so when you also work with a database Server. Almost as common as this activity is the fact that you get to work with dates that are poorly formatted.

I was recently provided with a set of data (when I say provided I mean that is was pasted into a team chat window) with a rather peculiar format. I had a start time and finish time that looked like a little lile this:

|eventID|Start_Time|End_Time|
|---|---|---|
|1|Mon 21-Jun 10:00| Mon 21-Jun 10:12|
|2|Mon 21-Jun 10:15|Mon 21-Jun 10:35|
|3|Tue 22-Jun 09:00|Tue 22-Jun 09:45|

My first step was to paste the data into Excel and then save that as a csv so that it was simple to get into PowerShell which would mean I can use ```Write-DbaDataTable``` from the dbaTools module to shift the data into my database. 

I needed to record this data in a database table and calculate the duration of each event so needed to convert these StartTime and EndTime values into actual datetime values. My first attempt was to create a database table with matching column names and the desired data types and try to import the data, hoping that the INSERT would automatically convert the data into datetime values. I was disappointed. SQL Server couldnt convert the string values like ```Mon 21-Jun 10:00``` to a datetime value. I was going to have to do this myself and then hand converted values to the table.

My next thought was to use Regular Expressions to remove the ```Mon, ``` part of each row from the source data. Yeah, Regex. Even with the help of [Regex101](www.regex101.com) it wasn't a pretty solution, we won't bother ourselves with it here.

I was now thinking of making the conversion in the imported data right after the ```Import-Csv``` and ahead of the insert to the database. A quick search around in the datetime class I found the ParseExact method. I found this by typing ```[datetime]::``` and then pressed Ctrl + Space to get the intellisense options
![image](https://user-images.githubusercontent.com/2597535/123693292-c7246800-d84f-11eb-85e3-0c08d8721019.png)

We can see the ways to use ParseExact in the blue-ish font after the various methods are listed when we move focus to ParseExact method - they are all variations of ```datetime ParseExact(string s, string format, System.IFormatProvider provider, System.Globalization.DateTimeStyles style)```. This tells us that we get a datetime type from the method and we need to provide it with a string, a string for the format, and a datetime style.

To get the hang of this I just used a static variable set to the date values in the format I had from my csv
```powershell
$SampleDate = "Mon 21-Jun 10:00"
```
Now we can put that into the ParseExact method. I can add the format by representing the date value I was given with the format specifiers at [custom date and time format strings](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings).

```powershell
[datetime]::ParseExact($SampleDate, 'ddd, dd-MMM HH:mm', $null)
```

![image](https://user-images.githubusercontent.com/2597535/123694705-92191500-d851-11eb-9aff-cc055da6816f.png)

We get handed back a date! PowerShell has interpreted the string that we gave it once we explained what bits of the string matche which parts of the date and PowerShell has turned it into a date by adding the current year to the day(d) and month(M) information and the hours(HH) and minutes(m) have been supplemented with a zero value for seconds.

With this success in hand its now just a case of taking every value from the csv file (which we have in our $DailyData variable and running a conversion on both date columns to turn them into dates

```powershell 
$WriteData = $DailyData | ForEach-Object {
        [PSCustomObject]@{
            EventID    = $_.EventID
            Start_Time = ([datetime]::ParseExact($_.start_time, 'ddd, dd MMM HH:mm', $null) )
            End_Time   = ([datetime]::ParseExact($_.end_time, 'ddd, dd MMM HH:mm', $null) )
        }

```

Once that's done it's off to ```Write-DataTable``` with data that we are now confident will insert into our database. Or are we?

Something for the reader to add to this script example is handling for strings that are not convertable to dates. What if we had "Wed, 28-Jun 25:00"? You'll need some error handling around the ParseExact, or look for another method that might help test if a value is a date...
