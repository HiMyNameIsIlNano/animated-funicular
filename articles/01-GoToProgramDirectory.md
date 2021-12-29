It is again time for a new useless thing. As I am trying to reduce the amount of GUI-based applications I use throughout the day, I thought I wrote myself a little bash function and included it into my .bashrc file, that searches for a given command and switches directly into its folder if that program exists.

The small gotchas is, that the file must be unique, so I guess that the script makes more sense if one is searching for a specific command rather than a document.

```bash
function cddir-of() {
	local file="$@"
	
	filefullpath=$(command -v ${file})
	
	[ ! -f "${filefullpath}" ] && { echo "File not found!"; return; }
	
    cd $(dirname $(which "${filefullpath}")) 
}
```

usage `cddir-of make`

