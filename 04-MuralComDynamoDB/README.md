# 🚀 AWS Portfolio: Serverless & Cloud Infrastructure

Bem-vindo ao meu projeto de integração **Full Stack Serverless**. Aqui, demonstro como unir uma interface moderna com o poder da nuvem AWS para criar soluções escaláveis e de baixo custo.

---

## 📝 01. Mural de Feedbacks (DynamoDB + NoSQL)
Este é o coração do projeto. O que você escreve no site atravessa a nuvem e é gravado permanentemente.

* **🗄️ Database:** Utilização do **Amazon DynamoDB** para armazenamento NoSQL de alta performance.
* **⚙️ Backend:** AWS Lambda (Python 3.12) processando requisições em tempo real.
* **🔗 Integração:** Frontend (Vercel) ➡️ Function URL (CORS) ➡️ Lambda ➡️ DynamoDB.



---

## 🔢 02. Gerador de CPF (Event Driven)
Uma ferramenta utilitária que demonstra o uso de computação sob demanda.
* **Lógica:** Função Lambda isolada que gera dados válidos para testes.
* **Performance:** Resposta em milissegundos com custo zero de servidor parado.

---

## 🛠️ Arquitetura Técnica

| Serviço | Função |
| :--- | :--- |
| **AWS Lambda** | Processamento de lógica (Python) |
| **Amazon DynamoDB** | Banco de Dados NoSQL |
| **AWS IAM** | Gestão de permissões e segurança |
| **Vercel** | Hospedagem do Frontend |
| **GitHub** | CI/CD e versionamento |

---

## 💡 Aprendizados e Desafios
Durante o desenvolvimento, superei desafios reais de arquitetura de nuvem:
* **CORS Management:** Configuração de headers (`Content-Type`, `Origin`) para comunicação segura entre domínios.
* **Data Typing:** Ajuste de conflitos de tipos entre o JSON do Frontend e a Partition Key do DynamoDB.
* **Permissions:** Implementação do princípio de privilégio mínimo no IAM.

---

## 🤝 Mentalidade do Projeto
Operando sob a filosofia de **Compaixão e Liberdade**, este projeto não é apenas código; é um espaço para conexões autênticas, onde a tecnologia serve ao propósito humano e à transparência.

---

### 📬 Contato
**Fabio Stefano** *Especialista em Suporte T.I. & Aspirante a Cloud Engineer (AWS)*

> "A simplicidade é o último grau da sofisticação."
