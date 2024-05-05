---
title: "Python Injection"
description: "Demostração de um bot para telegram feito em python com vulnerabilidade de injection"
tags: ["owasp", "appsec", "python", "injection", "pt-br"]
categories:
- OWASP
- AppSec
- Python
- Telegram
- Injection
date: 2024-03-23
draft: false
showAuthor: false
authors:
  - guisso
showAuthorsBadges : false
---

## Bot do Telegram com Vulnerabilidade de Injeção

Este projeto demonstra um bot simples do Telegram implementado em Python usando a biblioteca `python-telegram-bot`. O bot inclui uma vulnerabilidade na função `echo`, que avalia qualquer entrada de texto recebida, potencialmente levando a ataques de injeção de código.

## Executando o Projeto

Siga estas etapas para executar o projeto localmente:

1. Clone o repositório:

   ```bash
   git clone https://github.com/fguisso/python-injection
   ```

2. Instale as dependências necessárias:

   ```bash
   pip install python-telegram-bot
   ```

3. Obtenha um Token de Bot do Telegram:

   - Inicie uma conversa com BotFather no Telegram. Você pode encontrá-lo pesquisando por "@BotFather" na barra de pesquisa do Telegram ou clicando [aqui](https://t.me/BotFather).
   - Envie o comando `/newbot` para iniciar o processo de criação de um novo bot.
   - Siga as instruções para escolher um nome e um username para o seu bot.
   - Após criar o bot, o BotFather irá fornecer um token. Copie esse token.

4. Defina o Token do Bot como uma Variável de Ambiente:

   ```bash
   export TOKEN_TELEGRAM="seu_token_do_bot_aqui"
   ```

5. Execute o script Python:

   ```bash
   python main.py
   ```

6. Interaja com o bot no Telegram.

## Funcionamento do Bot

O bot está configurado para receber mensagens contendo operações matemáticas como entrada. Essas operações são resolvidas no comando `echo`, que retorna o resultado da operação como resposta.

Por exemplo, ao enviar a mensagem `2 + 2` para o bot, ele retornará `4`. Isso pode ser útil para realizar cálculos simples diretamente no Telegram.

## Explicação da vulnerabilidade

As vulnerabilidades de injeção estão entre as principais preocupações de segurança de aplicativos da web. De acordo com o [OWASP Top 10](https://owasp.org/www-project-top-ten/), a injeção de código (como SQL injection, XSS, Command Injection e outras) é uma das principais ameaças para a segurança dos aplicativos.

No contexto deste bot, a vulnerabilidade de injeção ocorre na função `echo`, que utiliza a função `eval` para evoluir qualquer entrada de texto recebida. Isso permite que possíveis atacantes executem código arbitrário enviando uma entrada maliciosa. Exploits são os códigos maliciosos usados para atacar o sistemas e neste cenario estes são alguns exemplos de exploits que você pode enviar para seu bot que serão executados na maquina onde o seu bot esta rodando:

|Mensagem para o bot|Resultado do exploit|
|--|--|
|`os.getenv("TOKEN_TELEGRAM")`|Acesso a variáveis de ambiente|
|`os.system("rm -rf /")`|Execução de comandos do sistema operacional, no caso, rm vai deletar todos os arquivos do sistema.|
|`import malicious_module`|Importação de módulos maliciosos|
|`__import__("malicious_module").malicious_function()`|Execução de código malicioso.|

Os usuários são encorajados a testar esses exploits em seu próprio bot e observar os resultados. No entanto, tenha cuidado ao usar exploits, pois eles podem causar danos ao sistema.

## Documentação

- [python-telegram-bot Documentation](https://python-telegram-bot.readthedocs.io/en/stable/)
- [Telegram Bot API Documentation](https://core.telegram.org/bots/api)
- [Telegram BotFather Documentation](https://core.telegram.org/bots#botfather)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)