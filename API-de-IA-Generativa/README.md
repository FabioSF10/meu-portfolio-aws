# 🚀 API de IA Generativa (AWS + Azure)

## 📌 Descrição
Este projeto consiste na implementação de uma arquitetura **Cross-Cloud**, conectando o ambiente da **AWS** com o motor de inteligência da **Azure**. O foco principal é demonstrar a comunicação segura entre provedores distintos através de uma API escalável e de alta disponibilidade.

A aplicação processa requisições enviadas via frontend através de funções **Serverless**, permitindo que o usuário interaja com um modelo de linguagem avançado e receba respostas inteligentes em tempo real, sem a necessidade de manter servidores ativos.

------------------------------------------------------------------------

## 🛠️ Stack Tecnológica

* **Compute:** AWS Lambda (Python 3.x) executando lógica de backend *serverless*.
* **API Management:** Amazon API Gateway para exposição e segurança do endpoint HTTPS.
* **AI Engine:** Azure AI Foundry operando como provedor do modelo de linguagem.
* **Model:** GPT-4o mini (Modelo de alta performance e baixa latência para respostas rápidas).
* **Libraries:** Python `requests` para integração via API REST.
* **Build & Deploy:** Ambiente Windows (CMD) para empacotamento de dependências e gerenciamento de camadas da Lambda.

------------------------------------------------------------------------

## 🏗️ Arquitetura do Fluxo de Dados

O projeto segue um modelo de comunicação síncrona entre diferentes serviços de nuvem:

1. **Frontend:** Interface hospeda no S3 envia a requisição do usuário via HTTPS.
2. **Amazon API Gateway:** Atua como a porta de entrada, gerenciando o tráfego e encaminhando a solicitação.
3. **AWS Lambda:** Processa o evento em Python, realiza a autenticação e dispara a chamada para a IA.
4. **Azure AI Foundry:** O modelo GPT-4o mini processa o prompt e gera a inteligência da resposta.
5. **Ciclo de Retorno:** A resposta percorre o caminho inverso (Azure → Lambda → API Gateway) até ser renderizada no navegador do usuário.

------------------------------------------------------------------------

## ⚙️ Implementação Passo a Passo

### 1. Provisionamento do Modelo na Azure
O primeiro passo foi configurar o "cérebro" da aplicação no **Azure AI Foundry**:

* **Criação do Workspace:** Configurei um novo projeto dentro do Hub de IA da Azure para organizar os recursos.
* **Deploy do Modelo:** Realizei o deploy do modelo **GPT-4o mini** (identificado como `fabio-digital-model`), definindo as capacidades de tokens e limites de taxa.
* **Parâmetros de Conectividade:** Extraí as credenciais necessárias para a integração externa:
    * **Endpoint:** A URL de destino para as requisições REST.
    * **API Key:** A chave de autenticação para o cabeçalho das requisições.

------------------------------------------------------------------------

### 2. Preparação do Pacote de Dependências (Windows CMD)
Como a AWS Lambda não possui a biblioteca `requests` nativamente, realizei o empacotamento manual das dependências utilizando o CMD do Windows:

* **Isolamento de Ambiente:** Criei um diretório local para organizar os artefatos do projeto.
* **Instalação Target:** Utilizei o comando `pip install` com o parâmetro `-t .` para instalar a biblioteca diretamente na raiz da pasta, garantindo que ela seja incluída no arquivo `.zip` final.

```bash
# Criando e acessando a pasta do projeto
mkdir lambda-azure
cd lambda-azure

# Instalando a biblioteca 'requests' na raiz do diretório
pip install requests -t .
```
* **Nota Técnica: Esse processo é essencial para que o ambiente de execução da Lambda tenha acesso a todas as bibliotecas externas necessárias durante o runtime.

------------------------------------------------------------------------
### 3. Desenvolvimento da Função Lambda
Com as dependências instaladas, criei o arquivo principal `lambda_function.py` na raiz da mesma pasta. O script foi desenvolvido para atuar como o orquestrador da comunicação entre o frontend e a IA.

**O código realiza as seguintes operações:**
1. **Captura:** Recebe o corpo da requisição (JSON) vindo do API Gateway.
2. **Integração:** Formata o payload e dispara uma requisição autenticada para o endpoint da Azure.
3. **Tratamento:** Extrai apenas o conteúdo relevante da resposta da IA.
4. **Entrega:** Retorna o resultado para o usuário com os cabeçalhos de CORS configurados.

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

---

**Em resumo, o fluxo da Lambda é:**
`Receber Mensagem` ➔ `Enviar para Azure` ➔ `Tratar Retorno` ➔ `Responder ao Frontend`


------------------------------------------------------------------------


### 4. Criação do ZIP

Selecionar tudo dentro da pasta e zipar.

------------------------------------------------------------------------

### 5. Upload na AWS

-   Criar Lambda
-   Runtime: Python 3.9
-   Faça upload do .zip

------------------------------------------------------------------------

### 6. Variáveis de ambiente
Aqui você irá armazenar as chaves de acesso do API do Azure, pra chave não ficar exposta no codigo, precisa ficar dentro destas variaveis que o AMAZON AWS disponibiliza dentro do Lambda

-   AZURE_OPENAI_ENDPOINT
-   AZURE_OPENAI_KEY

------------------------------------------------------------------------

### 7. API Gateway
Aqui é a conexão da porta de saida com o codigo, pra vigiar o que entra e o que sai.
-   Criar endpoint POST
-   Integrar com Lambda

------------------------------------------------------------------------

## 8. Front End, o codigo que eu usei no site foi este, o script junto do html:

```
<script>
document.addEventListener("DOMContentLoaded", () => {
    const btnChat = document.getElementById("btn-chat");
    const chatInput = document.getElementById("chat-input");

    if (btnChat) btnChat.addEventListener("click", enviarMensagemIA);
    if (chatInput) {
        chatInput.addEventListener("keypress", (e) => {
            if (e.key === "Enter") enviarMensagemIA();
        });
    }
});

function addMensagem(texto, tipo) {
    const display = document.getElementById('chat-display');
    const div = document.createElement('div');
    
    div.style.padding = "10px";
    div.style.borderRadius = "8px";
    div.style.marginBottom = "10px";
    div.style.maxWidth = "85%";
    div.style.fontSize = "0.9em";
    div.style.lineHeight = "1.4";
    div.style.display = "block";
    div.style.clear = "both";

    if (tipo === 'user') {
        div.style.background = "#ff9900";
        div.style.color = "#232f3e";
        div.style.marginLeft = "auto";
        div.style.borderBottomRightRadius = "2px";
        div.innerHTML = `<strong>Você:</strong><br>${texto}`;
    } else {
        div.style.background = "#1e293b";
        div.style.color = "white";
        div.style.marginRight = "auto";
        div.style.borderLeft = "3px solid #ff9900";
        div.style.borderBottomLeftRadius = "2px";
        div.innerHTML = `<strong>IA:</strong><br>${texto}`;
    }

    display.appendChild(div);
    display.scrollTop = display.scrollHeight;
}

async function enviarMensagemIA() {
    const input = document.getElementById('chat-input');
    const btn = document.getElementById('btn-chat');
    const display = document.getElementById('chat-display');

    const msg = input.value.trim();
    if (!msg) return;

    addMensagem(msg, "user");
    input.value = "";
    btn.disabled = true;

    const typingIndicator = document.createElement('div');
    typingIndicator.id = "typing";
    typingIndicator.style.color = "#ff9900";
    typingIndicator.style.fontSize = "0.8em";
    typingIndicator.style.padding = "5px";
    typingIndicator.innerHTML = "<em>Processando...</em>";
    display.appendChild(typingIndicator);
    display.scrollTop = display.scrollHeight;

    try {
        const res = await fetch("https://9u6eokphxh.execute-api.us-east-1.amazonaws.com/prod/chat", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ message: msg })
        });

        const data = await res.json();
        const indicator = document.getElementById("typing");
        if (indicator) indicator.remove();

        addMensagem(data.response || "Sem resposta da IA.", "ia");

    } catch (e) {
        const indicator = document.getElementById("typing");
        if (indicator) indicator.remove();
        addMensagem("Erro ao conectar com a nuvem.", "ia");
    } finally {
        btn.disabled = false;
        input.focus();
    }
}
</script>
<section>
    <div class="project-grid">
        <div class="content">
            <h2><i class="fas fa-robot"></i> 01. API de IA Generativa</h2>
            <h3>Integração Serverless Híbrida (AWS & Azure)</h3>
            <p>Este projeto demonstra a comunicação segura e eficiente entre provedores de nuvem distintos para entregar inteligência artificial como serviço.</p>
            <ul>
                <li><strong>Gateway de API:</strong> Exposição de endpoint seguro via AWS API Gateway.</li>
                <li><strong>Processamento:</strong> AWS Lambda atuando como middleware para orquestração de chamadas.</li>
                <li><strong>Inteligência:</strong> Consumo de modelos LLM via Azure OpenAI Service (Foundry).</li>
                <li><strong>Arquitetura:</strong> Design desacoplado focado em baixa latência e escalabilidade.</li>
            </ul>
            <a href="https://github.com/FabioSF10/meu-portfolio-aws" target="_blank" style="color: var(--aws-orange); font-weight: bold; text-decoration: none;">Repositório do Projeto →</a>
        </div>
        <div class="media">
            <div class="widget chat-box">
                <h3 style="color: var(--aws-orange); text-align: center; margin-top: 0;">🤖 Fabio-IA Explorer</h3>
                <p style="font-size: 0.8em; text-align: center; margin-top: -10px; opacity: 0.7;">Integração em tempo real via Lambda</p>
                <div id="chat-display" style="height: 250px; overflow-y: auto; background: #020617; border-radius: 8px; padding: 15px; margin-bottom: 10px; font-size: 0.9em; border: 1px solid #334155;">
                    <div class="msg ia" style="background: #1e293b; padding: 8px; border-radius: 8px; margin-bottom: 10px; border-left: 3px solid var(--aws-orange);">
                        Olá! Sou uma IA conectada via AWS e Azure. Como posso ajudar seu dia hoje?
                    </div>
                </div>
                <div class="chat-input-area" style="display: flex; gap: 8px;">
                    <input type="text" id="chat-input" placeholder="Envie um comando para a IA..." style="margin-bottom: 0;">
                    <button id="btn-chat" style="width: 60px; flex-shrink: 0;">➤</button>
                </div>
            </div>
        </div>
    </div>
</section>
```
------------------------------------------------------------------------
RESULTADO

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
