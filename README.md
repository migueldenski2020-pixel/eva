import pygame
import threading
import sys
import os
import webbrowser
import ctypes
from datetime import datetime

# --- CONFIGURAÇÕES DO SISTEMA ---
nome_ai = "Eva"
usuario = "Ren"
status = "Online"
imagem_eva = "eva.1.0.png"

# Tabela de consulta rápida
conhecimento = {
    "eva": f"Bem vindo {usuario}, sistema {status}. O que você deseja senhor?",
    "quem é você": f"Eu sou a {nome_ai}, sua assistente inteligente em desenvolvimento.",
    "horas": "Eu ainda não aprendi a olhar o relógio, mas posso aprender em breve."
}

# --- SISTEMA DE CARREGAMENTO (BOOT) ---
try:
    with open("memoria_eva.txt", "r", encoding="utf-8") as arquivo:
        for linha in arquivo:
            if ":" in linha:
                pergunta, resposta = linha.strip().split(":", 1)
                conhecimento[pergunta] = resposta
    print(f"SISTEMA: {nome_ai} carregou memórias externas com sucesso.")
except FileNotFoundError:
    print("SISTEMA: Criando nova base de dados...")

# --- DISPARO DA INTERFACE EM SEGUNDO PLANO ---
threading.Thread(target=aparecer_eva, daemon=True).start()

# --- SEU LOOP ORIGINAL ---
while True:
    fala = input(f"{usuario}: ").lower() 

    if "que horas são" in fala or "hora" in fala:
        agora = datetime.now()
        hora_formatada = agora.strftime("%H:%M")
        print(f"{nome_ai}: Agora são {hora_formatada}, {usuario}.")
        continue

    if "que dia é" in fala:
        hoje = datetime.now()
        data_formatada = hoje.strftime("%d/%m/%Y")
        print(f"{nome_ai}: Hoje é dia {data_formatada}, {usuario}.")
        continue

    if "abrir youtube" in fala:
        print(f"{nome_ai}: Executando comando. Abrindo YouTube.")
        webbrowser.open("https://www.youtube.com")
        continue

    if "abrir google" in fala:
        print(f"{nome_ai}: Abrindo navegador para pesquisa.")
        webbrowser.open("https://www.google.com")
        continue

    if "modo de trabalho" in fala:
        print(f"{nome_ai}: Ativando protocolo de produtividade. Preparando ambiente, {usuario}.")
        os.system("code") 
        webbrowser.open("https://gemini.google.com/app?hl=pt-PT")
        os.system("start spotify")
        print(f"{nome_ai}: Ambiente configurado. Bom trabalho, {usuario}.")
        continue

    if "sair" in fala: # Adicionado para fechar a imagem junto
        os._exit(0)

    encontrou_resposta = False
    for chave in conhecimento:
        if chave in fala:
            print(f"{nome_ai}: {conhecimento[chave]}")
            encontrou_resposta = True
            break

    if not encontrou_resposta:
        print(f"{nome_ai}: Eu não sei o que isso significa. Você pode me ensinar?")
        nova_resposta = input(f"{usuario} (me fale oque é): ")
        conhecimento[fala] = nova_resposta
        with open("memoria_eva.txt", "a", encoding="utf-8") as arquivo:
            arquivo.write(f"{fala}:{nova_resposta}\n")
        print(f"{nome_ai}: Certo, aprendi e guardei na minha memória.")

