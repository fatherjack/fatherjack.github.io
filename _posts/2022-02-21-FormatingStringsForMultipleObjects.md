---
layout: post
title: "Working with dates in non-standard formats"
description: "How to handle dates that dont conform to normal formats."
category: articles
tags: [powershell, date, datetime, standard]
---
---

layout: post
title: "Working with formatting strings"
description: "Combining a couple of neat PowerShell techniques for handy output control."
category: articles
tags: [powershell, string, format, standard]
---

I am working with lots of video files currently and I am storing them in an Azure blob container. To put them into the container I am changing the file to have a fixed format which a combination of an ID, the video author, the title of their session, each separated by underscores.

For a long time I have been a strong advocate for using the -f formatting operator which lets you control and customise string formats. If you haven't used it much I wholly recommend taking a good look at what it can do [in the PowerShell documentation](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators?view=powershell-7.2#special-operators).

If you are comfortable with -f then you can [skip the next little bit and go to the neat bit](#Funbit) of this post. If you aren't familiar with if then lets have a swift review - in short you build your string at the left side of your script inside quotes, have the -f Format Operator in the middle and then on the right have a comma separated list of the values you want formatted in your string.

> eg "formatted string" -f [List of values],...

Here are few examples

```powershell
PS> "{0}`tProcessing item {1}. " -f (Get-date), $ItemID

# If $ItemID has a value of 6 then your output will look like
'21/02/2022 12:23:14     Processing item 6.' 

PS> "{0:dd-MMM-yy}`tProcessing item {1}. " -f (Get-date), $ItemID

# If $ItemID has a value of 6 then your output will look like
'21-Feb-22 12:23:14     Processing item 6.'

```

So the integer within the { } relates to the position of the comma separated list of items after the -f operator and we can set formatting for numbers, dates, strings, etc at will.

<a name="Funbit"></a>

Back to my list of video files. I don't have multiple variables or properties to work with for the -f format operator, everything I am working with is in the single filename property.

Let's make up some pretend videos and added them into a PowerShell array to work through our examples

```powershell
$V = @("1_Bob_How-to-train-your-new-puppy.mp4", "2_Sharon_Making-a-new-handle-for-your-hammer.mp4", "3_Gavin_Changing-a-tyre-for-new-motorists","4_Megan_Baking-for-the-large-family.mp4")

```

 I know I can separate that on the '_' character with the -split operator to get the information about the video like this

```powershell
"1_Bob_How-to-train-your-new-puppy.mp4" -split '_'

# This will give us
1
Bob
How-to-train-your-new-puppy.mp4

# Looking at one of the array elements we get the same
$V[1] -split '_'

2
Sharon
Making-a-new-handle-for-your-hammer.mp4
```

but what if I don't want to specify any one element, perhaps I want a list of all the video authors without anything else, I want the same from all of the array elements how can I get this?

```powershell
$V -split '_'

# I get everything from every array element...
1
Bob
How-to-train-your-new-puppy.mp4
2
Sharon
Making-a-new-handle-for-your-hammer.mp4
3
Gavin
Changing-a-tyre-for-new-motorists
4
Baking-for-the-large-family.mp4

```

Well, let's start by using the pipeline to handle the provision of each array element to my code and add in the -f operator to format the output that I need.

```powershell
$v.filename | ForEach-Object { "Video ID {0} is called {2} and was authored by {1}." -f (($_-split '_')) } 

# the -f operator is working with the elements output by the -split function of each array element in turn

Video ID 1 is called How-to-train-your-new-puppy.mp4 and was authored by Bob.
Video ID 2 is called Making-a-new-handle-for-your-hammer.mp4 and was authored by Sharon.
Video ID 3 is called Changing-a-tyre-for-new-motorists and was authored by Gavin.
Video ID 4 is called Baking-for-the-large-family.mp4 and was authored by Megan.
```

### Conclusion

The -f format operator is most often used with a comma separated list of objects but we can give it something that renders objects at run time too.
