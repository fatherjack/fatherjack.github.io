
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
