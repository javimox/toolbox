# toolbox
Some stuff here.

## grepdate v0.1

 USE: grepdate --from="DATE" --to="DATE" filename(s)[.gz]
 Options:
	-f|--from		Specify a starting DATE (XX/XX/XX not supported)
	-t|--to			Specify an ending DATE (XX/XX/XX not supported)
	-m|--matched-files	Show matched filename(s)

When --from or --to values are not specified, the output will be limited to the matched file.
It handles transparently files gz-compressed and uncompressed.

**Examples**
Print a period of time found in all the files in the directory:
`grepdate "2019-06-13 15:00:01" "2019-06-14 15:00:02" *`

Print from the match to the end of the file where it was found:
`grep --from="2019-06-13 15:00:01" *`

Print from the beginning of the file where the value was found up to the match:
`grepdate --to="2019-06-13 15:00:01" *`
