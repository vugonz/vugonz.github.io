---
date: 2025-01-07T00:18:00Z
author: "Gonçalo"
title: "Estágio HS-API"
description: Tasks para o estágio na HS-API 
draft: true
toc: true
---

# Tarefas 
- [ ] Setup Inicial
- [ ] Modelos
- [ ] CRUD
- [ ] Autenticação

---

## Setup Inicial
**Objetivo**:
Configurar o ambiente de desenvolvimento de uma aplicação básica em Flask.

**Tarefa**:
Criar uma aplicação simples que retorna `Hello world!` no endpoint `/`.

**Passos**
1. **Instalar e configurar corretamente o Python >= 3.7**
    - A instalação varia de plataforma para plataforma; pesquisem a forma apropriada de instalar na vossa!
    - Verifiquem se o executável do Python está no vosso **PATH** (o que será isso do PATH? 🤔).
    - Verifiquem se o `pip` está bem configurado (o que será isso do pip? 🤔).
2. **Criar um ambiente virtual para a vossa aplicação**
    - Existem muitas formas de fazer isto; a que aconselho é utilizar o pacote `venv`.
3. **Instalar dependências**
    - Instalem todas as dependências no vosso ambiente virtual utilizando o `pip`. Por agora, será apenas necessário instalar o pacote `Flask`.
4. **Programar a aplicação**
    - O único requisito é que o endpoint `/` retorne `Hello world!` em **plaintext**.
5. **Correr a aplicação localmente**
    - Dependendo de como escreveram o código, podem simplesmente correr `python <ficheiro>` ou usar o próprio Flask para descobrir a vossa aplicação. Podem ver mais sobre isso [aqui](https://flask.palletsprojects.com/en/stable/quickstart/) (por enquanto não é algo muito importante)
    - Testem o endpoint utilizando o vosso browser ou qualquer outra ferramenta (o próprio Python com o pacote `requests` é outra forma)

**Coisas úteis**

Alguns comandos que vos podem vir a ser úteis:

- `python --version` - Verificar que o Python está corretamente instalado e no vosso **PATH**.
- `python -m pip --version` ou `pip --version` - Verificar que o `pip` está configurado (os comandos não fazem exatamente a mesma coisa, mas se um deles funcionar, pelo menos uma versão funcional do `pip` está pronta).
- `python -m ensurepip --upgrade` - Instalar o `pip`. Em princípio, deveria vir bem configurado com o Python mas pode não acontecer.
- `python -m <package>` - forma de invocar pacotes de Python como scripts. Utilizada por muitos pacotes, como o `ensurepip`, `venv` e até o próprio `pip` 🤯.

**Conclusão**

Após completarem esta tarefa, terão um ambiente pronto para desenvolver uma aplicação em Flask! Utilizar novos pacotes é tão simples como instalá-los no ambiente virtual e importá-los no código. Para executarem uma aplicação Flask já em desenvolvimento, como a HS-API, o processo é idêntico: criar um ambiente virtual, instalar todas as dependências e executar a aplicação localmente de forma apropriada (pois este processo pode variar de projeto para projeto).

---

## Modelos
Soon™