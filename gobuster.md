# gobuster
Gobuster is a brute-force scanner tool to enumerate directories, files, hidden dir, hidden files of websites, sub-domain and virtual host names. Gobuster is written in the Go programming language. 

The gobuster's mechanism is a trial-and-error method of finding an answer to a solution. This innately means Gobuster is a “loud” enumerator that can be more easily detected by an Intrusion Detection System (IDS). Gobuster will not recursively enumerate directories, so it’s a good idea to run Gobuster again on any discovered directories.

It has **three main modes** it can be used with:
* **dir:** the classic directory brute-forcing mode
* **dns:** DNS subdomain brute-forcing mode
* **vhost:** virtual host brute-forcing mode (not the same as DNS!)

## `dir` mode

This mode is used to scan the directories, hidden dir on web server

```console
gobuster dir -u 'http://thetoppers.htb' -w subdomains-top1million-5000.txt 10.129.187.245 -x php,txt,js,py -e -t 50 -k
```
* -e: show full URLs
* -x: specify the file extensions we are looking for
* -k: skip SSL verification (in many cases especially in CTF like events the SSL certifications are self signed and therefore not verified)


## `dns` mode

`gobuster` tries to *DNS resolve* the subdomains it tries so it can verify if they exist or not. As there are cases in pentesting where a specific DNS server is required to be used, Gobuster gives us the possibility to do so using the `-r` flag.

* -d:  target domain
* -i: print the ip beside the subdomain that was found.
```console
gobuster dns -d thetoppers.htb -w subdomains-top1million-5000.txt -t 50 10.129.187.245 -i
[+]
[+]
[+]
=========================================
Found: s3.thetoppers.htb [10.129.187.246]
=========================================
```
## `vhost` mode

This mode should not be mistaken to be the same as the DNS mode. In the DNS mode the tool attempts to DNS resolve the subdomains. In vhosts mode the tool is checking if the subdomain exists by visiting the formed url and verifying the IP address.

* -u: target URL

```console
gobuster vhost -u thetoppers.htb -w subdomains-top1million-5000.txt -t 50 10.129.187.245
```