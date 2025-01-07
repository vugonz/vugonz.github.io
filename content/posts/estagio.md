---
date: 2025-01-07T00:18:00Z
author: "Gonçalo"
title: "Estágio HS-API"
description: Tasks para o estágio na HS-API 
draft: false
toc: true
---

# Tarefas 
- [ ] Setup Inicial
- [ ] Modelos
- [ ] CRUD
- [ ] Autenticação

---

## Setup Inicial

**Objetivo**: Configurar o ambiente de desenvolvimento de uma aplicação básica em Flask.

**Tarefa**: Criar uma aplicação simples que retorna `Hello world!` no endpoint `/`.

**Passos:**
1. **Instalar e configurar corretamente o Python >= 3.7**
    - A instalação vai variar de plataforma para plataforma, pesquisem a forma apropriada de instalar na vossa!
    - Verificar que o executável do Python está no vosso PATH (o que será isso do PATH? 🤔)
    - Verificar que o pip está bem configurado (o que será isso do pip? 🤔)
2. **Criar um virtual environment para a vossa aplicação**
    - Existem muitas formas de fazer isto, a que aconselho é utilizar o package `venv`
3. **Instalar dependências**
    - Instalar as dependências todas no vosso virtual environment utilizando o `pip`. Por enquanto será apenas necessário instalar o package `Flask`
4. **Programar a aplicação**
    - O único requisito é que o endpoint `/` retorne `Hello world!` em plaintext
5. **Correr a aplicação localmente**
    - Aqui, dependendo de como fizeram o código, podem simplesmente correr `python <ficheiro>` ou utilizar o próprio Flask para descobrir a vossa aplicação, vejam mais sobre isso [aqui](https://flask.palletsprojects.com/en/stable/quickstart/) (por enquanto não é algo muito importante)
    - Testem o endpoint usando o vosso browser ou qualquer outra ferramenta (o próprio Python com o package `requests` é outra forma)

**Coisas úteis**

Alguns comandos que vos podem vir a ser úteis:

- `python --version` - verificar que o Python está corretamente instalado e no vosso PATH
- `python -m pip --version` ou `pip --version` - verificar que o pip está bem configurado (os comandos não fazem a mesma coisa, mas se um funcionar pelo menos uma versão funcional do pip está pronta)
- `python -m ensurepip --upgrade` - instalar o pip (em princípio deveria vir bem configurado com o Python mas pode não acontecer)
- `python -m <package>` - forma de invocar packages de Python como scripts (utilizada por muitos pacotes, como o `ensurepip` e o `venv`)