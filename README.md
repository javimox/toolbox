# toolbox
Some stuff here.

## grepdate v0.1<br/>
<br/>
 USE: `grepdate --from="DATE" --to="DATE" filename(s)[.gz]`<br/>
 Options:<br/>
  `-f|--from`		Specify a starting DATE (XX/XX/XX not supported)<br/>
  `-t|--to`		Specify an ending DATE (XX/XX/XX not supported)<br/>
  `-m|--matched-files`	Show matched filename(s)<br/>
<br/>
When --from or --to values are not specified, the output will be limited to the matched file.<br/>
It handles transparently files gz-compressed and uncompressed.<br/>
<br/>
**Examples**<br/>
Print a period of time found in all the files in the directory:<br/>
`grepdate "2019-06-13 15:00:01" "2019-06-14 15:00:02" *`
<br/>
Print from the match to the end of the file where it was found:<br/>
`grep --from="2019-06-13 15:00:01" *`
<br/>
Print from the beginning of the file where the value was found up to the match:<br/>
`grepdate --to="2019-06-13 15:00:01" *`
