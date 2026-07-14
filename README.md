# monitor_ip_telegram_python
Monitore a mudança de IP

```ruby
import requests
import time
import os
from datetime import datetime

# ===== CONFIGURAÇÕES =====
INTERVALO = 60  # segundos

ARQUIVO_IP = "ultimo_ip.txt"

TOKEN = "DIGITE O TOKEN AQUI"
CHAT_ID = "DIGITE O ID AQUI"
# =========================

def obter_ip_publico():
    try:
        resposta = requests.get("https://api.ipify.org")
        return resposta.text.strip()
    except:
        return None

def ler_ip_salvo():
    if os.path.exists(ARQUIVO_IP):
        with open(ARQUIVO_IP, "r") as f:
            return f.read().strip()
    return None

def salvar_ip(ip):
    with open(ARQUIVO_IP, "w") as f:
        f.write(ip)

def enviar_telegram(mensagem):
    url = f"https://api.telegram.org/bot{TOKEN}/sendMessage"
    payload = {
        "chat_id": CHAT_ID,
        "text": mensagem
    }
    try:
        requests.post(url, data=payload)
    except:
        print("Erro ao enviar mensagem no Telegram")

print("🔎 Monitor de IP com alerta Telegram iniciado...\n")

while True:
    ip_atual = obter_ip_publico()
    ip_salvo = ler_ip_salvo()

    if ip_atual:
        if ip_salvo is None:
            salvar_ip(ip_atual)
            print(f"[{datetime.now()}] IP inicial: {ip_atual}")
        elif ip_atual != ip_salvo:
            mensagem = f"""
⚠️ MUDANÇA DE IP DETECTADA!

📅 {datetime.now()}
🔁 Antigo: {ip_salvo}
🆕 Novo: {ip_atual}
"""
            print(mensagem)
            enviar_telegram(mensagem)
            salvar_ip(ip_atual)
        else:
            print(f"[{datetime.now()}] IP permanece: {ip_atual}")

    time.sleep(INTERVALO)
```


# Bot Telegram

![Principais Comandos Linux](./telegram3.png)

