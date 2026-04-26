# 📡 Teste de Conectividade: O Protocolo ICMP (Ping)

Após conseguir o acesso via SSH, o próximo passo foi entender por que, por padrão, o comando `ping` não funciona em uma instância EC2, mesmo que ela esteja ligada e acessível.

## 🛠️ O que foi feito

### 1. O bloqueio inicial
Tentei dar um `ping [IP-da-instancia]` do meu PC e recebi um *Timeout*. Isso confirmou que o meu **Security Group** estava fazendo o trabalho dele, bloqueando tudo o que eu não autorizei explicitamente.

### 2. Liberando o "Eco"
Para o Ping funcionar, tive que voltar ao Console da AWS e editar as **Inbound Rules** (Regras de Entrada):
* **Type:** All ICMP - IPv4
* **Protocol:** ICMP
* **Port Range:** All
* **Source:** My IP (Sempre mantendo a segurança de não abrir para o mundo!)

<img width="1359" height="559" alt="image" src="https://github.com/user-attachments/assets/cdea6ab3-61df-4d99-93c1-5e8200c83f01" />

---

## ✅ Resultado
Após salvar a regra, o terminal começou a responder instantaneamente. Ver os pacotes indo e voltando confirmou que a rota de rede estava 100% operacional. Voilà!! Nesta hora fiquei feliz, parece simples, mas pensa bem, é minha primeira vez, deu certo!!

<img width="822" height="353" alt="image" src="https://github.com/user-attachments/assets/c8de8b8c-f53d-4074-881c-1aa3c86c7905" />


---

## 📝 Anotações
* **Protocolos Diferentes:** O SSH (TCP) não libera o Ping (ICMP). São "idiomas" diferentes na rede.
* **Segurança Granular:** No Security Group, você controla exatamente o que entra. Se você não pediu o Ping, a AWS protege você ocultando sua máquina de testes de varredura.

---

---

### 📬 Contato

Sinta-se à vontade para se conectar comigo para trocarmos experiências sobre AWS e tecnologia!

* **LinkedIn:** [linkedin.com/in/fabio-figueiredo-5601041b2](https://www.linkedin.com/in/fabio-figueiredo-5601041b2)
* **E-mail:** [Fabio.sf10@hotmail.com](mailto:Fabio.sf10@hotmail.com)
* **Cargo:** *Analista de Suporte T.I. | Focado em AWS Cloud & AWS Certified Cloud Practitioner Path*

---
