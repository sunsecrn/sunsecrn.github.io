---
id: index
aliases: []
tags:
  - Threat-Intel
  - Phishing
  - GenAI
authors:
  - "cirilo"
categories:
  - sql-injection
date: "2025-08-09"
description: ""
draft: false
showAuthor: false
showAuthorsBadges: true
title: "Detectando sites phishing criados e hospedados em plataformas de GenAI"
---

Recentemente estava navegando pelo Instagram e vi um perfil suspeito com um link que ia para uma página do FGTS.
Logo, reparei no alto grau de sofisticação técnica do phishing e estava hospedado em um domínio ao qual eu desconhecia:
**`{vercel.app}`**.

Pesquisando mais a fundo, encontrei um [artigo interessante da equipe de Threat Intelligence da Okta](https://www.okta.com/newsroom/articles/okta-observes-v0-ai-tool-used-to-build-phishing-sites/?utm_source=newsletter&utm_medium=email&utm_campaign=newsletter_axioscodebook&stream=top#_ga=2.231461471.1533468390.1754615520-1803662451.1754615520)
falando sobre a utilização de plataformas de GenAI para criação e distribuição de sites falsos,
o que me levantou a ideia de realizar o mapeamento de sites falsos utilizando plataformas de GenAI, focado no cenário brasileiro.

## Ideia: Por que não utilizar Dorks para mapear esses sites?

Utilizando também GenAI, criei diversas dorks utilizando palavras-chave de empresas, termos e instituições brasileiras e documentei no seguinte [repositório](https://github.com/sunsecrn/Dorks-Vercel-e-Lovable).

## Demonstração Prática

Em umas das Dorks consegui encontrar o phishing apresentado na evidencia abaixo:

`![Demonstração POC](poc.gif)`

---

Contribua adicionando no repositório com novas Dorks! 🔥

## 🤝 Como Contribuir

1. Faça um **fork** deste repositório.
2. Adicione suas dorks seguindo o padrão acima.
3. Envie um **Pull Request** com suas sugestões.
4. Sinta-se à vontade para sugerir melhorias no README ou na organização do repositório!

---

## ⚠️ Aviso Legal

> Este repositório é destinado **exclusivamente para fins educacionais e de pesquisa em segurança**. Não incentive ou realize atividades ilegais. Sempre reporte sites maliciosos às autoridades e aos provedores de hospedagem.

---

## 💬 Contato

Dúvidas, sugestões ou parcerias? Abra uma issue ou entre em contato!

---

Feito com 💙 para a comunidade de segurança.
