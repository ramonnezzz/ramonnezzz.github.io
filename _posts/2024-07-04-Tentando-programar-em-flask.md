---
layout: post
title: "Tentando programar em Flask..."
author: Ramonnezzz
date: 2024-07-04 21:15:00 -0300
categories: Flask Python
---

<p>Estudando o básico de Flask, tentei efetuar a criação de rotas, o que se demonstrou bem simples, seguindo a ideia de produto mínimo viável.</p>

<pre><code># aplicando o conceito de decorator

@app.route('/', ['GET', 'POST'])
def index():
    return redirect(url_for('login'))
</code></pre>

<p><code>@app.route('/'):</code> Este é um decorator que define a rota para a função que segue. Neste caso, a rota é a raiz do aplicativo (ou seja, o endereço principal).</p>

<p><code>return redirect(url_for('login')):</code> Dentro da função <code>index</code>, há uma instrução para redirecionar o usuário para a rota definida para a função <code>login</code>. A função <code>redirect</code> é usada para redirecionar o usuário para um URL específico, e <code>url_for('login')</code> gera o URL para a função <code>login</code>.</p>

<p>O ponto foi que acabei me precipitando. Ao tentar entender os conceitos e funcionamentos, iniciei uma frustrada tentativa de implementação de um banco de dados Firebase, o que me levou a gastar tempo tentando implementar bibliotecas para conversar com o BD remoto.</p>

<p>Deveria iniciar a implementação em um banco de dados local, assim não tomaria muito do meu tempo em um conceito que não era o foco no momento.</p>

<pre><code>import flask

from flask import Flask, render_template, request, redirect, url_for

import firebase_admin

from firebase_admin import credentials, auth

import logging

import pyrebase

app = Flask(__name__)

# Inicializar o Firebase Admin SDK
cred = credentials.Certificate('serviceAccountKey.json')
firebase_app = firebase_admin.initialize_app(cred)

config = {
    "apiKey": "your_api_key",
    "authDomain": "your_project_id.firebaseapp.com",
    "databaseURL": "https://your_project_id-default-rtdb.firebaseio.com/",
    "storageBucket": "your_project_id.appspot.com"
}

firebase = pyrebase.initialize_app(config)

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'GET':
        return render_template('register.html')
    elif request.method == 'POST':
        email = request.form['email']
        password = request.form['password']
        if len(password) < 6:
            return "A senha deve ter pelo menos 6 caracteres."
        try:
            user = auth.create_user(
                email=email,
                password=password
            )
            return redirect(url_for('login'))
        except Exception as e:
            return f"Erro na criação de login: {str(e)}"

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST']:
        email = request.form['email']
        password = request.form['password']
        try:
            user = auth.get_user_by_email(email)
            user = auth.update_user(
                user.uid,
                email=email,
                password=password
            )
            return redirect(url_for('principal'))
        except Exception as e:
            return f"E-mail ou senha incorretos: {str(e)}"

@app.route('/principal', methods=['GET', 'POST'])
def principal():
    # Verificar se o usuário está autenticado
    try:
        user = auth.verify_id_token(request.cookies.get("token"))
        # Se o token for válido, renderizar a página principal
        return render_template('principal.html', email=user['email'])
    except Exception as e:
        return redirect(url_for('login'))

if __name__ == '__main__':
    app.run(debug=True)
</code></pre>

<p>Algo que se demonstrou muito desgastante, não pelo fato de "codar" em si, mas pela ausência de um estudo breve para uma implementação adequada, funcional e utilizável. Não se tratava de um projeto que chegaria a ser lançado como parte de um portfólio (talvez kkkkkkk).</p>

<p>Seguindo um tutorial (excelente e objetivo, diga-se de passagem), erroneamente não me propus a me aprofundar nos conceitos propostos, como as bibliotecas passadas.</p>

<pre><code>import firebase_admin

from firebase_admin import credentials, auth

import logging

import pyrebase
</code></pre>

<p>Depois de muita labuta tentando fazer funcionar (que aliás não funcionou por muito tempo kkkkkkk), parei pra observar e pensei: "Por que diabos eu pensei que fazer isso de cara soaria minimamente produtivo?"</p>

<p>No momento estou iniciando um projeto com uma aplicação mais viável, menos desgastante, produtiva (pelo menos né), e que me desgastasse para aprender.</p>

<p>Link do tutorial mencionado: <a href="https://community.revelo.com.br/aprenda-a-criar-telas-de-registro-e-login-de-usuarios-com-flask-e-firebase/">Aprenda a criar telas de registro e login de usuários com Flask e Firebase</a></p>

<p>É isso, logo mais posto algo relacionado ao projeto sólido que tenho em mente.</p>
