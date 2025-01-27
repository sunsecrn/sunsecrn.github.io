---
id: index
aliases: []
tags:
  - owasp
  - appsec
  - python
  - injection
  - pt-br
authors:
  - guisso
categories:
  - OWASP
  - AppSec
  - Python
  - Telegram
  - Injection
date: "2024-03-23"
description: Demonstration of a Telegram bot in Python with an injection vulnerability
draft: false
showAuthor: false
showAuthorsBadges: false
title: Python Injection
---

## Telegram Bot with Injection Vulnerability

This project demonstrates a simple Telegram bot implemented in Python using the
`python-telegram-bot` library. The bot includes a vulnerability in the `echo`
function, which evaluates any received text input, potentially leading to code
injection attacks.

## Running the Project

Follow these steps to run the project locally:

1. Clone the repository:

```bash
git clone https://github.com/fguisso/python-injection
```

```bash
pip install python-telegram-bot
```

2. Obtain a Telegram Bot Token:

   - Start a conversation with BotFather on Telegram. You can find it by
     searching for "@BotFather" in the Telegram search bar or clicking [here](https://t.me/BotFather).
   - Send the /newbot command to start the bot creation process.
   - Follow the instructions to choose a name and username for your bot.
   - After creating the bot, BotFather will provide a token. Copy this token.

3. Set the Bot Token as an Environment Variable:

```bash
 export TOKEN_TELEGRAM="your_bot_token_here"
```

4. Run the Python script:

```bash
python main.py
```

5. Interact with the bot on Telegram.

## Bot Functionality

The bot is configured to receive messages containing mathematical operations as
input. These operations are processed in the echo command, which returns the
result of the operation as a response.

For example, sending the message 2 + 2 to the bot will return 4. This feature
can be useful for performing simple calculations directly on Telegram.

## Explanation of the Vulnerability

Injection vulnerabilities are among the top concerns for web application
security. According to the OWASP Top 10, code injection (such as SQL injection,
XSS, Command Injection, and others) is one of the primary threats to application
security.

In this bot's context, the injection vulnerability occurs in the echo function,
which uses the eval function to evaluate any received text input. This allows
potential attackers to execute arbitrary code by sending malicious input.
Exploits are malicious codes used to attack systems, and in this scenario, here
are some examples of exploits you can send to your bot that will be executed on
the machine where the bot is running:

| Mensagem para o bot                                   | Resultado do exploit                                                                         |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `os.getenv("TOKEN_TELEGRAM")`                         | access environment variables                                                                 |
| `os.system("rm -rf /")`                               | execution of operating system commands, in this case, rm will delete all files on the system |
| `import malicious_module`                             | import malicious module                                                                      |
| `__import__("malicious_module").malicious_function()` | malicious code execution                                                                     |

Users are encouraged to test these exploits on their own bot and observe the
results. However, be cautious when using exploits, as they can cause system
damage.

## Documentation

- [python-telegram-bot Documentation](https://python-telegram-bot.readthedocs.io/en/stable/)
- [Telegram Bot API Documentation](https://core.telegram.org/bots/api)
- [Telegram BotFather Documentation](https://core.telegram.org/bots#botfather)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
