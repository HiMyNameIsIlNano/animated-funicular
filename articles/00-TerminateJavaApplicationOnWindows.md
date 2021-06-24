Sometimes it happens that applications hang and their respective used port remains busy. This is a simple (git-)bash function that kills all processes running on a given port: 

```bash
function kill-proc-on-port() {
	if [ "$#" -ne 1 ]; then
		echo "At least one parameter must be provided to the function."
        	echo "${FUNCNAME[0]} HH:MM [MM]"
		return
	fi
	
	local port="$@"
	
	PIDS=$(netstat -ano | findstr :$port | awk '{print $5}')
	for pid in $PIDS
	do
		taskkill //PID $pid //F
	done
}
```

usage `kill-proc-on-port 8080`
