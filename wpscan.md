# WPSCAN
> WordPress is an open source software, written in **PHP** and using **MySQL** database management system.

> Using [WPSCAN](https://github.com/wpscanteam/wpscan) to scan, detect and attack a number of vulnerabilities on Wordpress websites.

Some key features of the tool:    

    * Check the version of Wordpress Core, Plugins, Themes... to detect vulnerabilities of this version
    * Check website source code for XSS, SQL Injection, Local Attack or other vulnerabilities via CVE
    * Collect information about users (registered in the system)
    * Brute Force Attack of Website's Admin Account

WPScan will use the list of vulnerabilities published on **https://wpscan.com/** to detect vulnerabilities of the version of Wordpress in use. Vulnerabilities include: Wordpress Core, Plugins, Theme

## Update database
```console
kali@kali:~$ wpscan --update
```

## Enumeration Modes
* **Passive**: scan with a small number of requests => avoid causing DOS server
* **Aggressive**: scan with a large number of requests and continuously to the server
* **Mixed**: Combined both

By default, **Mixed** mode is used.

## Enumeration Options 
> **<mark >-e**</mark> flag is used for ***enumeration***.

*<mark >-e</mark> + enumeration option => -e vp*
* <mark > vp </mark>: Vulnerable plugins
* <mark > ap </mark>: All plugins
* <mark > p </mark> : Popular plugins
* <mark > vt </mark>: Vulnerable themes
* <mark > at </mark>: All themes
* <mark > t </mark> : Popular themes
* <mark > cb </mark>: Config backups (backups file)
* <mark > dbe </mark>: DB exports
* <mark > u </mark> : Grab all the usernames

## Examples
```console
kali@kali:~$ wpscan --url http://192.168.43.12/wordpress/ -e at –e ap –e u
```

* <mark >–e at</mark>: enumerate all themes of targeted website

* <mark >–e ap</mark>: enumerate all plugins of targeted website

* <mark >–e u</mark>: enumerate all usernames of targeted website

## Brute-force password using WPScan
After using the above command, we can generate a list of usernames and can try a brute-force password. You can use the wordlist **rockyou.txt**
```console
kali@kali:~$ wpscan --url http://192.168.43.12/wordpress/ -U user.txt -P rockyou.txt
```
## Scanning over a Proxy Server

If the WordPress web-application is running over a proxy server, we can use the **<mark>--proxy</mark>**
flag.

Ex: Wordpress webapp is now running over a proxy server with port 3333. We can bypass this proxy server:
```console
kali@kali:~$ wpscan --url http://192.168.43.12/wordpress/ --proxy http://192.168.43.12:3333
```

## Scanning with an HTTP Authentication enabled

Many websites enable HTTP authentication so that they can hide some essential and critical information from unauthenticated users.
We can use the **<mark>--http-auth</mark>** flag with our credentials.
```console
kali@kali:~$ wpscan --url http://192.168.43.12/wordpress/ --http-auth robot:123
```






