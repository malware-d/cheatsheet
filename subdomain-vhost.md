# What is Virtual Host?
> Vhosts and subdomains are different things.
> 
> Vhosts are a feature of apache or other services.
> 
> Subdomains are DNS.

Virtual hosts are different websites on the same machine (refers to the practice of running more than one website (such as company1.example.com and company2.example.com) on a single machine. 
). In some instances, they can appear to look like sub-domains, but don’t be deceived! 

Virtual Hosting occurs when a domain is hosting other domain names on a single server or multiple other servers. This allows companies to share resources on a single server.

There are mainly 2 types of Virtual hosts:

1. **IP-based Virtual Host:** we have different IP addresses for every website.
2. **Name-based Virtual Host:** several websites are hosted on the same IP. Mostly this type is widely and preferred in order to preserve IP space.

*But when talking about `VHOST` we are generally talking about **Named-based Virtual hosts**.*
# [How does Gobuster brute force sub-domains in Vhost mode inside a private network like Hack The Box?](https://www.reddit.com/r/hackthebox/comments/nn6soh/how_does_gobuster_brute_force_subdomains_in_vhost/)

The answer is ***`Host header`***
![Vhost bruteforcing](https://user-images.githubusercontent.com/90831245/204877681-08780eef-1d25-4128-8173-ff60a4191e2e.png)

Gobuster asking apache "do you host *ABC.domain.com*?" Has nothing to do with resolving *ABC.domain.com* to an IP address.

Gobuster requests the *ABC.server.com* by providing the **`Host:`** header. That's how gobuster enumerate sub-domains, even if the entry for sub-domains are not located in our */etc/hosts* file.

This is also how the webserver will differentiate to which website it has to send my requests since many websites are being hosted on the same server with the same IP.

It's through the "Host header". The web server identifies which content to serve up once it receives the Host header from the client.

💢💢💢That's why when using the **[wfuzz](https://github.com/xmendez/wfuzz)** tool, you will have to provide the flag `-H "Host: FUZZ.domain.com"`💢💢💢

# [WFUZZ]((https://github.com/xmendez/wfuzz))
WFUZZ is a web application bruteforcer. Combined with a wordlist, it can be used to scan domain names for files, directories or sub-domain.

*If you make a request to a web server to load a sub-domain, a lot of web servers are configured to return a default web page, even if the sub domain being requested does not exist.*

This is important to know because if we are trying to find a sub-domain, we need to find a way to exclude the `default responses` to find the sub-domain we are actually interested in.

To do this, run the command without any filters initially
```console
wfuzz -H "Host: FUZZ.target-domain-name.com" -w directory-list-2.3-medium.txt 10.129.187.245
```
There will probably be a lot of 200 response codes (or you will see the value of L (Line), W (Word), C (Character) identical, it is the parameter reflecting the default page).
![Ảnh chụp Màn hình 2022-12-01 lúc 1 12 50 SA](https://user-images.githubusercontent.com/90831245/204876118-ca82ef28-a083-4ef2-8d80-90a7b8a4caae.png)
 This is because, whilst these sub-domains don’t exist on the web server, the web server is still returning the default server web page. However, now that we know the **word/line/character** count of the default web page, we can exclude these from our results using the filters.

 *default page look like:*
 
 ![screenshot_20200721_182611](https://user-images.githubusercontent.com/90831245/204877207-458a3962-d78a-46bc-9e9e-bc82c91e0fae.png)


## Filter Parameters

| Parameter | Description                                                                  |
|-----------|------------------------------------------------------------------------------|
| -hc       | Hide responses with specified response code                                  |
| -hl       | Hide responses where count of lines on response is equal to specified value  |
| -hw       | Hide responses where word count of web page is equal to specified value      |
| -hh       | Hide responses where character count of web page is equal to specified value |

According to the above display results, we will eliminate the response with W = 1036.

```console
wfuzz -H "Host: FUZZ.thetoppers.htb" --hw 1036 -w subdomains-top1million-5000.txt -t 50 10.129.187.245
```
![Ảnh chụp Màn hình 2022-12-01 lúc 1 26 20 SA](https://user-images.githubusercontent.com/90831245/204878699-9630cc89-332e-441c-a2fa-9c41199372a3.png)

Result: s3.thetopper.htb

# gobuster





