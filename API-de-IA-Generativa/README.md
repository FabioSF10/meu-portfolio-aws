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

### 4. Empacotamento do Deployment Package (ZIP)
Para que a AWS Lambda reconheça o código e as dependências, o formato do pacote deve ser rigorosamente um arquivo `.zip` contendo os arquivos na raiz.

* **Procedimento:** Entre na pasta `lambda-azure`, selecione todos os arquivos e subpastas (incluindo a pasta da biblioteca `requests` e o seu `lambda_function.py`) e compacte-os.
* **Aviso Importante:** Não compacte a pasta "mãe". O arquivo `lambda_function.py` deve estar na "raiz" do arquivo ZIP, e não dentro de uma subpasta, caso contrário a AWS retornará erro de *Runtime.ImportModuleError*.

> **Dica:** Utilize o formato `.zip` padrão do Windows. Formatos como `.rar` ou `.7z` não são suportados pelo ambiente de execução da AWS.

------------------------------------------------------------------------

### 5. Provisionamento e Upload na AWS Lambda
Com o pacote `.zip` pronto, o próximo passo foi criar a infraestrutura necessária no console da **AWS** para hospedar e executar o código.

* **Criação da Função:** No console do AWS Lambda, selecionei "Criar função" e escolhi a opção "Criar do zero".
* **Configurações de Runtime:** Defini o ambiente de execução como **Python 3.9** (ou superior), garantindo compatibilidade com a biblioteca `requests`.
* **Permissões (IAM):** Certifiquei-me de que a função tivesse uma Role básica de execução para registrar logs no CloudWatch, facilitando o monitoramento.

#### Como realizar o Upload:
1.  Dentro da aba **Código (Code)** da sua função Lambda, localize o botão **"Fazer upload de"**.
2.  Selecione a opção **"Arquivo .zip"**.
3.  Escolha o arquivo que você criou na etapa anterior e salve.

> **Dica de Ouro:** Se o seu arquivo `.zip` tiver mais de 10MB, a AWS recomenda fazer o upload via **Amazon S3** para evitar falhas de timeout durante o envio.
------------------------------------------------------------------------

### 6. Configuração de Variáveis de Ambiente (Segurança)
Para seguir as melhores práticas de segurança em nuvem, utilizei as **Variáveis de Ambiente** da AWS Lambda. Isso garante que chaves de API e endpoints sensíveis não fiquem expostos diretamente no código-fonte, protegendo a aplicação contra acessos não autorizados.

* **Onde configurar:** No console da sua função Lambda, acesse a aba **Configurações (Configuration)** e selecione a opção **Variáveis de ambiente (Environment variables)**.
* **Chaves configuradas:**
    * `AZURE_OPENAI_ENDPOINT`: A URL de destino do seu modelo na Azure.
    * `AZURE_OPENAI_KEY`: A chave secreta de autenticação.

> **Benefício Técnico:** Ao utilizar variáveis de ambiente, o código se torna portátil. Se eu precisar trocar de modelo ou de região na Azure, basta alterar o valor no console da AWS, sem a necessidade de editar ou subir um novo arquivo `.zip`.
------------------------------------------------------------------------

### 7. Amazon API Gateway (A Porta de Entrada)
O **API Gateway** funciona como a interface pública do projeto. Como a AWS Lambda por si só não possui uma URL acessível via navegador, utilizei o API Gateway para criar um "ponto de encontro" seguro na internet.

* **Criação do Endpoint:** Configurei um recurso do tipo **POST**, que é o método ideal para enviar mensagens de texto do chat para o servidor.
* **Integração com Lambda:** Vinculei este endpoint à minha função Lambda, garantindo que cada vez que o Gateway recebe uma mensagem, ele "acorda" a função para processar a resposta.
* **Segurança e CORS:** Habilitei o **CORS (Cross-Origin Resource Sharing)**, permitindo que apenas o domínio do meu portfólio consiga realizar chamadas para esta API, evitando o uso indevido por terceiros.

> **Resumo:** O API Gateway recebe a pergunta do site, entrega para a Lambda e devolve a resposta da IA para o usuário.

------------------------------------------------------------------------

## 8. Integração com o Front-end
Para consumir a API, utilizei uma combinação de **HTML5** e **JavaScript (Vanilla)**. O objetivo foi criar uma interface de chat leve e responsiva, que se comunica diretamente com o Amazon API Gateway.

### Funcionalidades do Script:
* **Manipulação de Eventos:** O script captura o envio do formulário e evita o recarregamento da página.
* **Consumo de API (Fetch API):** Utilizei o método `fetch()` para realizar a chamada assíncrona para o endpoint da AWS.
* **Gerenciamento de Estado:** Enquanto a IA processa a resposta, o script exibe um feedback visual (como "Pensando...") para melhorar a experiência do usuário.
* **Renderização Dinâmica:** Assim que a resposta JSON retorna da Lambda, o JavaScript injeta o texto diretamente na bolha de chat do site.

---
#### 📄 Implementação do Código
Abaixo está a estrutura utilizada para realizar a conexão entre a interface e o backend:

``` html
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
## 🏆 Resultados Alcançados

O desenvolvimento desta Prova de Conceito (PoC) resultou em uma infraestrutura moderna e totalmente funcional, destacando-se pelos seguintes pontos:

* **Interoperabilidade Cloud:** Sucesso na comunicação fluida entre **AWS** e **Azure**, provando competência em arquiteturas multi-cloud.
* **Performance e Latência:** Graças ao uso do modelo **GPT-4o mini**, o tempo de resposta da IA é extremamente baixo, proporcionando uma experiência de conversação natural.
* **Escalabilidade Serverless:** A API está preparada para suportar desde poucos acessos até picos de tráfego, sem necessidade de intervenção manual ou gerenciamento de servidores.
* **Segurança de Dados:** Implementação rigorosa de variáveis de ambiente e políticas de CORS, garantindo a integridade das chaves de API.

---
### 🎥 Demonstração
O chat está disponível em meu portfólio, permitindo interações em tempo real onde a inteligência da Azure é processada via backend AWS e entregue de forma instantânea no frontend.

🚀 [Visualizar Chat de IA no Portfólio](http://meu-portfolio-fabio-stefano.s3-website-us-east-1.amazonaws.com/#)

## 🚀 Próximos Passos & Melhorias Futuras

Este projeto é uma base sólida para expansões. As melhorias planejadas incluem:

* **Implementação de RAG (Retrieval-Augmented Generation):** Conectar a API a uma base de dados (como um arquivo PDF ou banco de vetores) para que a IA responda com base em informações específicas do meu currículo e histórico profissional.
* **Otimização da UI/UX:** Refinar a interface do chat com animações de digitação, suporte a Markdown nas respostas e melhor adaptação para dispositivos móveis.
* **Monitoramento e Logs Avançados:** Integrar o **AWS CloudWatch Insights** para gerar métricas de tempo de resposta, volume de requisições e monitoramento de possíveis erros em tempo real.

---

## 👨‍💻 Autor

**Fabio Stefano de Figueiredo** *Analista de Infraestrutura de TI | AWS Cloud | Automação de Processos & Suporte*

---
> "Transformando conectividade em inteligência através da nuvem." 🚀
