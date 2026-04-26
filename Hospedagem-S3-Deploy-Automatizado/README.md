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
