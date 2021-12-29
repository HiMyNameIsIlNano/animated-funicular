I always wanted to have a way to timebox my activity, but I have never found a command line tool that helped me with that. This is a simple script to achieve what I need and alert me with a notification at the end of the countdown.


```python
import ctypes
import getopt
import subprocess
import sys
import time
from sys import platform

import progressbar


def show_timer(amount_in_sec):
    widgets = [
        ' [', progressbar.Timer(), '] ',
        progressbar.Bar(),
        ' (', progressbar.ETA(), ') ',
    ]
    for i in progressbar.progressbar(range(amount_in_sec), widgets=widgets):
        time.sleep(1)


def show_popup():
    if platform == "linux" or platform == "linux2":
        subprocess.run(["notify-send", "-u", "normal", "-t", "5000", "Countdown", "The time is over!"], check=True)
    elif platform == "win32":
        ctypes.windll.user32.MessageBoxW(0, "The time is over!", "Countdown", 1)
    else:
        print("Unsupported OS")


def main(argv):
    amount_in_sec = 60

    try:
        opts, args = getopt.getopt(argv, "hs:", ["seconds="])
    except getopt.GetoptError:
        print('timer.py -s 120')
        sys.exit(2)

    for opt, arg in opts:
        if opt == '-h':
            print('timer.py -s 120')
            sys.exit()
        elif opt in ("-s", "--seconds"):
            amount_in_sec = int(arg)
        else:
            print('timer.py -s 120')
            sys.exit(2)

    show_timer(amount_in_sec)
    show_popup()


if __name__ == '__main__':
    main(sys.argv[1:])

```

The script requires `progressbar2` to be installed with `pip install progressbar2` and `notify-send` on `Linux`. On `Windows` no additional dependency is needed.  
The script can be made executable this way:

```shell
chmod +x timer.py
./timer.py -s 60
```

This starts a countdown of 60 seconds and shows a notification when the time is up. 

The source code can be found [here](https://github.com/HiMyNameIsIlNano/himynameis-utils/blob/main/timer.py)
