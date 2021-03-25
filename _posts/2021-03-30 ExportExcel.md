If you use the ImportExcel PowerShell module (and you really should be if you are handling data in your scripts and sharing it out with colleagues via files ) then you will probably be writing code that takes an object variable which is the results of your script and passing it to the Export-Excel function.

Some key parameters for the function are the path for the output file and a table style for that data. I found myself using this so much as a splatting variable that I created this VSCode snippet to ease the process.

Open VSCode and press F1.
![image](https://user-images.githubusercontent.com/2597535/112467179-a3d4b300-8d5e-11eb-956f-bd2e8cfdf96b.png)

Choose PowerShell snippets.
![image](https://user-images.githubusercontent.com/2597535/112467247-ba7b0a00-8d5e-11eb-8ab0-d2d5f5cac197.png)

Add the snippet code section below into that JSON file
````powershell
"ExportExcel":{
	"prefix": "_ExportExcelSplat",
	"body": [
		"\\$splatExcel = @{",
		"    path       = ${Outfile}",
		"    tablestyle = \"medium15\"",
		"    autosize   = \\$true",
		"}",
		"\\$result | export-excel @splatExcel -worksheetname \"${1:Worksheetname}\" -tablename \"${2:Tablename}\"",
		"$0"],
	"description": "A basic splat table and Export-Excel command"
}
````
![image](https://user-images.githubusercontent.com/2597535/112467412-ec8c6c00-8d5e-11eb-871b-3c83e7648012.png)

Save the file and then open a new file in the code editor, then type ````_exp```` and you will see this tool tip
![image](https://user-images.githubusercontent.com/2597535/112467692-45f49b00-8d5f-11eb-8bd3-af1b01b4367c.png)

Press [Tab] and the snippet of code will be pasted in and your cursor position will be ready to complete the Worksheetname value. Once you have done that press [Tab] again and you can complete the TableName value. Once you have completed that press [Tab] again and you can provide the path value for the file you want created. One file press of the [Tab] button will take you to the line after the pasted snippet for you to continue your script.
