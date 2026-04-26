# 🚀 AWS Serverless Portfolio: Mural & Cloud Infrastructure

Este projeto demonstra a construção de uma infraestrutura de nuvem moderna e escalável, utilizando o modelo **Serverless** da AWS. Integrei um frontend interativo com serviços de backend para criar uma experiência Full Stack real.

---

## 📝 01. Mural de Feedbacks (DynamoDB + NoSQL)
O diferencial deste portfólio é um mural interativo onde as mensagens são processadas e gravadas permanentemente na nuvem.

### 🛠️ Backend & Persistência
O backend foi construído com **Python 3.12** rodando em **AWS Lambda**, integrado ao **Amazon DynamoDB** para persistência NoSQL.

> **Configuração Técnica:**
> * **Banco de Dados:** Amazon DynamoDB (Tabela: `MuralDeFrases`)
> * **Partition Key:** `id` (Tipo: String)
> * **Lógica:** AWS Lambda com gatilho via Function URL.

<img width="955" height="501" alt="image" src="https://github.com/user-attachments/assets/934a3f98-1921-4b1a-8cd8-9105f8c95b5b" />


---

## 🏗️ Desafios Técnicos e Soluções (Troubleshooting)

Desenvolver em nuvem exige resolver gargalos reais de integração. Abaixo, destaco os principais desafios superados:

### 1. Gestão de CORS (Cross-Origin Resource Sharing)
Ajuste fino nos cabeçalhos de resposta e permissão de método (`POST`) na Function URL para permitir a comunicação segura entre o domínio do Frontend (Vercel) e o backend (AWS).

<img width="1318" height="517" alt="image" src="https://github.com/user-attachments/assets/76daa68b-6133-4130-af9b-ec89de6349d6" />


### 2. Segurança e Permissões (IAM)
Implementação de políticas de acesso granular (Princípio do Privilégio Mínimo) para que a Lambda tivesse permissão de leitura e escrita no DynamoDB.

<img width="1314" height="423" alt="image" src="https://github.com/user-attachments/assets/b7a45bd6-47c0-405a-af73-7c030ab0049d" />


### 3. Ajuste de Tipagem NoSQL
Resolução de erros de comunicação (Status 500) causados por divergência entre o formato da Partition Key e o dado enviado pelo Frontend.

<img width="743" height="376" alt="image" src="https://github.com/user-attachments/assets/d114e165-f76b-4923-bdfb-a4acde73d81a" />


## 🤝 Filosofia: Compaixão e Liberdade
Este projeto reflete minha mentalidade técnica e pessoal: unir a precisão da engenharia de nuvem com a transparência das conexões humanas. Um espaço onde a tecnologia serve para simplificar e conectar.

---

### 📬 Contato

Sinta-se à vontade para se conectar comigo para trocarmos experiências sobre AWS e tecnologia!

* **LinkedIn:** [linkedin.com/in/fabio-figueiredo-5601041b2](https://www.linkedin.com/in/fabio-figueiredo-5601041b2)
* **E-mail:** [Fabio.sf10@hotmail.com](mailto:Fabio.sf10@hotmail.com)
* **Cargo:** *Analista de Suporte T.I. | Focado em AWS Cloud & AWS Certified Cloud Practitioner Path*

---
