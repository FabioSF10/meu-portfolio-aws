# 🛡️ Gerador de CPF Serverless (AWS Lambda + Python)

Projeto desenvolvido para fins de estudo de computação em nuvem (AWS) e integração Full Stack. O sistema gera um CPF válido seguindo o algoritmo oficial de validação.

## 🛠️ Tecnologias Utilizadas

* **Frontend:** HTML5, CSS3 e JavaScript (Hospedado na **Vercel**).
* **Backend:** Python 3.x (Rodando em **AWS Lambda**).
* **Infraestrutura:** AWS Function URL com configuração de **CORS** e CloudWatch para logs.

## 🚀 Como funciona?

1. O usuário clica no botão "Gerar Novo CPF" no frontend.
2. O JavaScript faz uma requisição `fetch` assíncrona para a **Function URL** da AWS.
3. A função Lambda executa o script Python que calcula os dígitos verificadores do CPF.
4. A AWS retorna um JSON com o número gerado.
5. O frontend recebe o dado e atualiza a tela sem precisar recarregar a página.

## 🧠 Aprendizados de Infraestrutura (CORS e Cloud)
Durante o desenvolvimento, foram aplicados conceitos de:
- Configuração de **Cross-Origin Resource Sharing (CORS)** para permitir a comunicação segura entre o domínio do Vercel e a AWS.
- Implementação de arquitetura **Serverless** (sem servidor fixo), reduzindo custos e aumentando a escalabilidade.
- Deploy contínuo via GitHub e integração de serviços de nuvem.

---
Mantenha a mentalidade de aprendizado constante! 🚀
