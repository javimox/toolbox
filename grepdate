#!/bin/bash
# grepdate by jmox (mox_at_mox.sh)
# 16.06.2019
#
# grepdate v0.1
#
# USE: grepdate --from="DATE" --to="DATE" filename(s)[.gz]
# Options:
#	-f|--from		Specify a starting DATE (XX/XX/XX not supported)
#	-t|--to			Specify an ending DATE (XX/XX/XX not supported)
#	-m|--matched-files	Show matched filename(s)
#
# When --from or --to values are not specified, the output will be limited to the matched file.
# It handles transparently files gz-compressed and uncompressed.
#
# Examples
# Print a period of time found in all the files in the directory:
# grepdate "2019-06-13 15:00:01" "2019-06-14 15:00:02" *
#
# Print from the match to the end of the file where it was found:
# grep --from="2019-06-13 15:00:01" *
#
# Print from the beginning of the file where the value was found up to the match:
# grepdate --to="2019-06-13 15:00:01" *
#
# Functions
#set -x
_usage() {
	head -25 $0 | tail -n21 | cut -c 2-
}
_error() {
	case $1 in
	1) printf "Value from=\"$FROM\" not found!\nTo use -to= value to print the same file where it was found use one of these options:\n\
		$(basename $0) --from=\"$TO\" filename(s)\n\
		$(basename $0) --to=\"$TO\" filename(s)\n" ;;
	2) printf "Value to=\"$TO\" not found!\nTo use -from= value to print the same file where it was found use one of these options:\n\
		$(basename $0) --from=\"$FROM\" filename(s)\n\
		$(basename $0) --to=\"$FROM\" filename(s)\n" ;;
	3) printf "NONE value found!\n$(basename $0) did not find from=\"$FROM\" nor --to=\"$TO\".\n" ;;
	4) printf "Option unknown. Use $(basename $0) -h"
	esac
}
_lookup_from() {
	# print from $1 to the end of the file
	#sed -n "/$1/",'$p' $2
	$_CAT $2 | sed -n "/$1/",'$p'
}
_lookup_to() {
	# print all the file up to the $1
	#sed "/$1/q" $2
	$_CAT $2 | sed "/$1/q"
}
_lookup_period() {
	# print all between $1 and $2
	#sed -n "/$1/,/$2/p" $3
	$_CAT $3 | sed -n "/$1/,/$2/p"
}
# Variables used to read user input
FROM=""
TO=""
FILES=""

# Booleans
FOUND_FROM=false
FOUND_TO=false
PERIOD=false
SHOW_MATCHES=false
ENABLE_GZIP=false

# Commands - no compression
# zcat and zgrep can handle uncompressed files but process slower
_CAT="cat"
_GREP="grep"

# Variables to handle results
found=""
found_start=""
found_end=""
period_files=""
exitcode=0

for i in "$@" ; do
	case $i in
	-f=*|--from=*)
		FROM="${i#*=}"
		FOUND_FROM=true
		shift
	;;
	-t=*|--to=*)
		TO="${i#*=}"
		FOUND_TO=true
		shift
	;;
	-m|--matched-files)
		SHOW_MATCHES=true
		shift
	;;
	-h|--help)
		_usage
		exit 0
	;;
	*)
	;;
	esac
done

if [ -z "$FROM" ] && [ -z "$TO" ] ; then
	FROM="$1"
	TO="$2"
	shift
	shift
fi

if $FOUND_FROM && $FOUND_TO ; then
	PERIOD=true
	# and we set to false again
	FOUND_FROM=false
	FOUND_TO=false
fi

# the rest of parameters are files, or should be. And we sort them by date.
FILES=$(ls -tr "$@")

for i in ${FILES} ; do
	format="${i##*.}"
	if [[ "$format" = "gz" ]] ; then
		ENABLE_GZIP=true
	fi
done

if $ENABLE_GZIP ; then
	# commands
	_CAT="zcat -f"
	_GREP="zgrep"
fi

if $PERIOD ; then
	# we do not quote FILES as it would get expanded before pathname
	readarray -t found <<< $($_GREP -io -m1 -E "($FROM|$TO)" ${FILES})
	
	for i in "${found[@]}" ; do
		# we save date and time counting that's the start of each log line
		match=$(echo "$i" | cut -d":" -f2-)

		# we compare the log against the user input
		if [[ "$match" == "$FROM" ]] ; then
			FOUND_FROM=true
			file_start=$(echo "$i" | cut -d":" -f1)
		elif [[ "$match" == "$TO" ]] ; then
			FOUND_TO=true
			file_end=$(echo "$i" | cut -d":" -f1)
		fi
	done

	if $FOUND_FROM && $FOUND_TO ; then
		# now we save start file, end file, and the files in between
		# match exact files ^filename$
		period_files=$(echo "$FILES" | sed -n "/^$file_start\$/,/^$file_end\$/p")

		if $SHOW_MATCHES ; then
			echo "Seen from=\"$FROM\" in $file_start"
			echo "Seen to=\"$TO\" in $file_end"
			echo "Possible timeframe: "${period_files}""
			## Leave script
			exit 0
		fi

		# let's show the result
		_lookup_period "$FROM" "$TO" "$period_files"
	elif ! $FOUND_FROM && ! $FOUND_TO ; then
		exitcode=3 && _error $exitcode
	else
		! $FOUND_FROM && exitcode=1 && _error $exitcode
		! $FOUND_TO && exitcode=2 && _error $exitcode
	fi
else
    # only one parameter
	if [[ -z "$FROM" ]] ; then
		found=$($_GREP -il -e "$TO" ${FILES})
		if $SHOW_MATCHES ; then
			echo "Seen to=\"$TO\" in $found"
            ## Leave script
			exit 0
		else
			_lookup_to "$TO" "$found"
		fi
	elif [[ -z "$TO" ]] ; then
		found=$($_GREP -il -e "$FROM" ${FILES})
		if $SHOW_MATCHES ; then
			echo "Seen from=\"$FROM\" in $found"
			## Leave script
			exit 0
		else
      _lookup_from "$FROM" "$found"
		fi
	fi
fi
#set +x
exit $exitcode
