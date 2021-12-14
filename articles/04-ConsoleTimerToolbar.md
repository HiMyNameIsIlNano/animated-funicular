I always wanted to have a way to timebox my activity, but I have never found a command line tool that helped me with that. This is a simple script to achieve what I need and alert me (with a beep) at the end of the countdown.


```python
#!/usr/bin/python

import progressbar
import ctypes
import sys, getopt
import time

def show_timer(amount_in_sec):
    widgets=[
    ' [', progressbar.Timer(), '] ',
        progressbar.Bar(),
    ' (', progressbar.ETA(), ') ',
    ]
    for i in progressbar.progressbar(range(amount_in_sec), widgets=widgets):
        time.sleep(1)

# This only works if the bell is active in the terminal
def show_popup():
    ctypes.windll.user32.MessageBoxW(0, "The time is over!", "Countdown", 1)

def main(argv):
    amount_in_sec=60

    try:
        opts, args = getopt.getopt(argv, "hs:",["seconds="])
    except getopt.GetoptError:
        print('timer.py -s 120')
        sys.exit(2)

    for opt, arg in opts:
        if opt == '-h':
            print('timer.py -s 120')
            sys.exit
        elif opt in ("-s","--seconds"):
            amount_in_sec = int(arg)
        else:
            print('timer.py -s 120')
            sys.exit(2)
    
    show_timer(amount_in_sec)
    show_popup()

if __name__ == "__main__":
    main(sys.argv[1:])__ == "__main__":
    main(sys.argv[1:])
```

The script requires `alive-progressbar` to be installed with `pip install alive-progress`. Moreover the script needs to be made executable.

```shell
chmod +x timer.py
./timer.py -s 60
```

This starts a countdown bar for 60 seconds and plays a beep sound at the end (if the bell sound is enabled).
