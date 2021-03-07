This is a simple (git-)bash script that kills a process running on a given port on Windows: 

```bash
#! /bin/sh

PIDS=$(netstat -ano | findstr :$port | awk '{print $5}')
for pid in $PIDS
do
    taskkill /PID $pid /F
done

```

