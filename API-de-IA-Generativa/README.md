# 🚀 API de IA Generativa (AWS + Azure)

## 📌 Descrição

Este projeto demonstra a integração entre AWS Lambda e Azure OpenAI para
criação de uma API de IA Generativa.

A aplicação permite que um usuário envie mensagens via frontend e receba
respostas inteligentes em tempo real.

------------------------------------------------------------------------

## 🧠 Tecnologias Utilizadas

-   AWS Lambda (Python)
-   AWS API Gateway
-   Azure OpenAI (Foundry)
-   Python (requests)
-   ChatGPT Mini (modelo leve e rápido)
-   Windows (CMD para build das dependências)

------------------------------------------------------------------------

## 🏗️ Arquitetura

Frontend → API Gateway → Lambda → Azure OpenAI → Resposta → Frontend

------------------------------------------------------------------------

## ⚙️ Como foi feito

### 1. Criação do modelo no Azure

-   Acesso ao Azure AI Foundry
-   Criação de um projeto
-   Deploy do modelo (ex: fabio-digital-model)
-   Coleta de:
    -   Endpoint
    -   API Key

------------------------------------------------------------------------

### 2. Desenvolvimento da Lambda

Código em Python utilizando requests:

```python
import os
import json
import requests

def lambda_handler(event, context):

    try:
        body = json.loads(event.get('body', '{}'))
    except:
        body = {}

    user_message = body.get('message', 'Olá!')

    base_url = os.environ.get("AZURE_OPENAI_ENDPOINT")
    api_key = os.environ.get("AZURE_OPENAI_KEY")

    endpoint = f"{base_url}openai/deployments/fabio-digital-model/chat/completions?api-version=2024-02-15-preview"

    headers = {
        "Content-Type": "application/json",
        "api-key": api_key
    }

    data = {
        "messages": [
            {
                "role": "system",
                "content": "Você é um assistente útil, direto e amigável."
            },
            {
                "role": "user",
                "content": user_message
            }
        ],
        "temperature": 0.7
    }

    response = requests.post(endpoint, headers=headers, json=data)

    result = response.json()

    ai_response = result["choices"][0]["message"]["content"]

    return {
        "statusCode": 200,
        "headers": {"Access-Control-Allow-Origin": "*"},
        "body": json.dumps({"response": ai_response})
    }

```
A Lambda: - Recebe mensagem - Envia para Azure - Retorna resposta

------------------------------------------------------------------------

### 3. Instalação das dependências (Windows CMD)

``` bash
mkdir lambda-azure
cd lambda-azure

pip install requests -t .
```

------------------------------------------------------------------------

### 4. Criação do ZIP

Selecionar tudo dentro da pasta e zipar.

------------------------------------------------------------------------

### 5. Upload na AWS

-   Criar Lambda
-   Runtime: Python 3.9
-   Upload do .zip

------------------------------------------------------------------------

### 6. Variáveis de ambiente

-   AZURE_OPENAI_ENDPOINT
-   AZURE_OPENAI_KEY

------------------------------------------------------------------------

### 7. API Gateway

-   Criar endpoint POST
-   Integrar com Lambda

------------------------------------------------------------------------

## 💬 Resultado

-   Chat funcional
-   Integração real entre AWS e Azure
-   Comunicação em tempo real

------------------------------------------------------------------------

## 🚀 Possíveis melhorias

-   Implementar RAG
-   Interface melhorada
-   Logs avançados

------------------------------------------------------------------------

## 👨‍💻 Autor

Fabio Stefano de Figueiredo
