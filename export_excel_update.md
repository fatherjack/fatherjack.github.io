A while back I shared a Visual Studio Code snippet for people that use the ImportExcel module to speed up your scripting. Well, after some consumer testing I have made a few changes just to make it a little more fluid in usage. Here is the latest itteration:

```powershell
"ExportExcel": {
		"prefix": "_ExportExcelSplat",
		"body": [
			"\\$Outfile = \"${3:OutputFileName}.xlsx\"",
			"\\$splatExcel = @{",
			"    path       = \"\\$Outfile\"",
			"    tablestyle = \"medium15\"",
			"    autosize   = \\$true",
			"}",
			"\\$result | export-excel @splatExcel -worksheetname \"${1:Worksheetname}\" -tablename \"${2:Tablename}\"",
			"$0"
		],
		"description": "A basic splat table and Export-Excel command"
	}
  ```
  
###  What has changed?

Well, it's super simple

- there is a new line that sets up the outputfile variable and the path key in the splat table is now referencing that variable rather than it being a fixed string - this makes the filename reusable when you have multiple results sets going to the same file.
- there is a new stop in the tab stops that is for the name of the variable that is being exported - I found that I wasnt always writing a script with a variable called $Results when I wanted to use the Export-Excel function.

If you want to have a refresher on how to create a VSCode snippet then checkout [my first post about this snippet here](https://fatherjack.github.io/articles/ExportExcel)

This is going in [my public gists](https://gist.github.com/fatherjack) so go ahead and grab an update from there and perhaps take a look at the other bits and bobs there.
