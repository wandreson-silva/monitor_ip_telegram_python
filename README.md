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

```text
Este script é um monitor de endereço IP público com alertas automáticos via Telegram. Ele serve para avisar você caso o IP da sua rede mude (o que é muito comum em conexões residenciais com IP dinâmico).
```

# 1. Configurações Iniciais
```text
INTERVALO = 60: Define que o script vai rodar em um loop eterno, checando o IP a cada 60 segundos (1 minuto).

ARQUIVO_IP = "ultimo_ip.txt": Nome do arquivo de texto que será criado na mesma pasta do script para salvar o último IP conhecido.

TOKEN e CHAT_ID: São as credenciais do seu bot do Telegram. O script precisa que você insira o token do bot e o ID do chat (seu usuário ou grupo) para onde as mensagens serão enviadas.
```

# 2. Funções Auxiliares
```text
obter_ip_publico(): Faz uma requisição web para o serviço api.ipify.org, que retorna apenas o IP externo atual da máquina. Se a internet cair ou o site falhar, ela retorna None.

ler_ip_salvo(): Abre o arquivo ultimo_ip.txt e lê o IP que foi guardado na última verificação. Se o arquivo não existir (primeira execução), retorna None.

salvar_ip(ip): Grava o IP atual no arquivo ultimo_ip.txt, substituindo o valor antigo.

enviar_telegram(mensagem): Faz uma requisição POST para a API do Telegram para enviar o texto formatado diretamente para o seu chat usando o bot configurado.
```

# 3. O Fluxo de Execução (O Loop while True)

```text
Quando o script é iniciado, ele entra em um loop infinito que realiza os seguintes passos a cada 60 segundos:

1.Obtém o IP atual da internet e lê o IP salvo localmente.

2.Se for a primeira execução (o arquivo ultimo_ip.txt ainda não existe):

Ele salva o IP atual no arquivo e exibe a mensagem de "IP inicial" no terminal.

3.Se o IP atual for diferente do IP salvo:

Identifica que houve uma mudança na rede.

Monta uma mensagem com a data, hora, o IP antigo e o novo IP.

Envia essa mensagem para o seu celular via Telegram e atualiza o arquivo local com o novo IP.

4.Se o IP continuar igual:

Apenas imprime no terminal que o IP permanece o mesmo (para você saber que o script continua rodando).

5.Aguarda 60 segundos antes de começar tudo de novo.
```

# Bot Telegram

![Principais Comandos Linux](./telegram3.png)

