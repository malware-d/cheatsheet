# REDIS Pentesting
> Remote Dictionary Server
* Default port: 6379 
```console
PORT     STATE SERVICE REASON         VERSION
6379/tcp open  redis   syn-ack ttl 63 Redis key-value store 5.0.7
```
* **`In-memory`** data structure store (temporary storage on RAM)
* Text based protocol
* key-value
* Datatypes: string, list, set, hash, sorted set
* By default and commonly Redis uses a plain-text based protocol, but it can implement **SSL/TLS**

## Enumeration
Scan
```console
nmap --script redis-info -sV -p 6379 10.129.31.62
msf> use auxiliary/scanner/redis/redis_server
```
Interacting with Redis server 
```console
nc -vn 10.129.31.62 6379
redis-cli -h 10.129.31.62
```
Obtaining the information and statistics about the Redis server
```console
10.129.31.62:6379> info
```
If something like the following is returned: **`-NOAUTH Authentication required.`** -> This means we need valid credentials to access the Redis server.
## Authentication

```console
10.129.31.62:6379> auth <username> <password> 
```
By default, Redis can be accessed **`without credentials`**. It can be configured to support:
* Only password
* Username + password

In **`redis.conf`** file:
* Set **password** with parameter *requirepass*
  
  *(If only password is configured -> default username: "default")*
* Set **username** with parameter *masteruser*

## Authenticated enumeration
```console
10.129.31.62:6379> info

# connected clients
10.129.31.62:6379> client list

# get config
10.129.31.62:6379> config get *
```

## Dumping Database
Tools can be used: [redis-dump](https://www.npmjs.com/package/redis-dump) and [redis-utils](https://www.npmjs.com/package/redis-dump).

Inside Redis the databases are numbers **`starting from 0`**
```console
10.129.31.62:6379> info keyspace
# Keyspace
db0:keys=4,expires=0,avg_ttl=0
db1:keys=2,expires=0,avg_ttl=0
```
In above example the database 0 and 1 (db0, db1) are being used.
* Database 0 contains 4 keys
* Database 1 contains 1 key
  
By default Redis will use database 0. In order to dump for example database 1:
```console
# indicate the database
10.129.31.62:6379> select 1
OK

#get keys
10.129.31.62:6379> keys *
1) "numb"
2) "flag"
3) "stor"
4) "temp"

10.129.31.62:6379> get flag         #get <key>
"03e1d2b376c37ab3f5319922053953eb"

```
If somethings like the following is returned: **`-WRONGTYPE Operation against a key holding the wrong kind of value`** while running **`GET <KEYS>`** -> it's because the key may be something else than a *string* or an *integer* and requires a special operator to display it.

To know the type of the key:
```console
# type of key
10.129.31.62:6379> type <KEY>

# get list items
10.129.31.62:6379> lrange <keys> 0 -1

#get hash items
10.129.31.62:6379> hget <key> <field>
```
