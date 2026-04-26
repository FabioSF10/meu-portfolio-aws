# 📁 Hospedagem S3 e Deploy Automatizado (CI/CD)

Neste projeto, documentei a migração da infraestrutura do meu portfólio para um ambiente nativo **AWS**, utilizando o conceito de **Hospedagem Estática** e **Entrega Contínua**.

---

## 🎯 Objetivo
Migrar de plataformas externas para a infraestrutura da **Amazon Web Services (AWS)**, garantindo controle total sobre políticas de acesso (IAM), custos e automação do ciclo de vida da aplicação.

## 🛠️ Tecnologias Utilizadas
* **AWS S3:** Hospedagem de arquivos estáticos (HTML/CSS/JS).
* **GitHub Actions:** Automação do pipeline de deploy (CI/CD).
* **AWS IAM:** Gestão de identidades e permissões seguindo o princípio de menor privilégio.
* **Markdown:** Documentação técnica do projeto.

---

## 🚀 Passo a Passo: Como eu implementei

### 1. Preparação do Bucket S3
O primeiro passo foi criar e configurar o "balde" (**S3 Bucket**) que armazenaria os arquivos:
* Criei o bucket com um nome único.
* Desativei a opção de "Bloquear todo o acesso público" (passo essencial para sites que precisam ser vistos na web).
* Habilitei a função **Static Website Hosting** nas propriedades do bucket, definindo o `index.html` como o ponto de entrada.
<img width="914" height="374" alt="image" src="https://github.com/user-attachments/assets/9d976a3b-e3bc-46c9-8721-1d70af525c08" />

### 2. Configuração de Segurança (Bucket Policy)
Para que o site seja acessível publicamente via navegador, apliquei uma **Bucket Policy** em formato JSON. Isso garante que qualquer pessoa possa ler os objetos (o site), mas impede qualquer alteração não autorizada:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::SEU-NOME-DO-BUCKET/*"
        }
    ]
}
```
<img width="918" height="369" alt="image" src="https://github.com/user-attachments/assets/054bdb28-b7c2-440e-9a98-f150f0dec897" />

### 3. Gestão de Identidade (IAM)
Para que o GitHub tenha permissão de enviar arquivos para a AWS com segurança, apliquei as melhores práticas de identidade:
* Criei um usuário no **IAM** específico para essa tarefa (ex: `github-actions-deployer`).
* Gerei as **Access Keys** (ID e Secret) exclusivas para este serviço.
* Anexei uma política de permissão restrita (inline policy), permitindo apenas as ações `s3:PutObject` e `s3:ListBucket` exclusivamente no bucket do projeto. **Segurança em primeiro lugar: o usuário não possui permissões administrativas.**
<img width="889" height="220" alt="image" src="https://github.com/user-attachments/assets/af6b791b-7f6a-494a-b3f3-e403b72f0b35" />

### 4. Pipeline de CI/CD com GitHub Actions
Implementei a automação para que o site se atualize sozinho. Toda vez que eu realizo um `git push` no meu repositório, o GitHub inicia um workflow que sincroniza os arquivos locais com o S3.

**O que eu configurei:**
1.  Armazenei as chaves da AWS nos **Secrets** do repositório do GitHub para proteger dados sensíveis.
2.  Criei o arquivo `.github/workflows/main.yml` que utiliza a AWS CLI para executar o comando de sincronização:
    `aws s3 sync . s3://meu-portfolio-fabio --delete`
    *(O parâmetro --delete garante que arquivos removidos localmente também sejam removidos do S3).*

<img width="944" height="434" alt="image" src="https://github.com/user-attachments/assets/023c6c3f-9a0a-40e0-afc2-97cd7d90c12f" />

---

## 🧠 Principais Aprendizados
1.  **Segurança (Least Privilege):** Entendi a importância de nunca usar chaves de administrador em automações, criando usuários com permissões cirúrgicas.
2.  **Agilidade com CI/CD:** A configuração inicial da automação elimina o trabalho repetitivo e o risco de erro humano no upload manual via console.
3.  **Eficiência de Custos:** O uso do S3 para sites estáticos é extremamente econômico e altamente escalável em comparação com manter servidores (EC2) ligados 24/7.

---

## 🔗 Conecte-se comigo

* **Website Online:** [Visite meu Portfólio](http://meu-portfolio-fabio.s3-website-us-east-1.amazonaws.com)
* **LinkedIn:** [linkedin.com/in/fabio-figueiredo-5601041b2](https://www.linkedin.com/in/fabio-figueiredo-5601041b2)
* **E-mail:** [Fabio.sf10@hotmail.com](mailto:Fabio.sf10@hotmail.com)

**Cargo:** *Analista de Infraestrutura de TI | AWS Cloud | Automação de Processos & Suporte*

---
*Este projeto reflete minha evolução técnica rumo à certificação AWS Solutions Architect.* 🚀
