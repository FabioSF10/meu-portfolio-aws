# 🚀 Minha Primeira Instância AWS

Este repositório documenta meu aprendizado prático na configuração de um servidor **Amazon EC2**. O foco aqui foi entender como a infraestrutura de rede (IPs, Gateways e Firewalls) se conecta para permitir um acesso seguro via SSH direto do meu computador pessoal.

---

## 🛠️ O que foi implementado

### 1. Camada de Segurança (Security Group)
A primeira etapa foi configurar o firewall da AWS. Apliquei o conceito de **Least Privilege** (Privilégio Mínimo), garantindo que a "porta" SSH (22) estivesse aberta apenas para o meu **IP Público**.

> **Aprendizado:** Entendi que o IP que vejo no meu roteador (IP Privado) é diferente do IP que o mundo vê (IP Público). Configurei o Security Group para reconhecer apenas a "placa" da minha casa.

<img width="1366" height="768" alt="Configuração de Security Group" src="https://github.com/user-attachments/assets/cef94908-53b9-47f6-a193-31383506a9a1" />


---

### 2. O Desafio do SSH e Permissões no Windows
Ao tentar conectar pela primeira vez, enfrentei o erro de `Connection closed`. Isso aconteceu porque o Windows cria arquivos com permissões "herdados", o que torna a chave `.pem` insegura para os padrões do Linux.

**A Solução:**
Utilizei o comando `icacls` para limpar as permissões e garantir acesso exclusivo do meu notebookzinho veio de guerra I3 com 4GB de ram:

```cmd
icacls "chave-estudos-aws.pem" /inheritance:r
```

Depois, voilà!! O Meu servidor Linux esta rodando, e eu to acessando direto do meu CMD do meu proprio computador, que demais.

<img width="868" height="519" alt="Sevidor Linux" src="https://github.com/user-attachments/assets/49371ac0-c812-4c93-9cb8-ec9146ed74e0" />

## 📝 Glossário de Comandos e Conceitos

Para eu não esquecer e servir de consulta:

`ssh -i "chave-estudos-aws.pem" ec2-user@18.212.xxx.xxx`
* **ssh**: O protocolo que cria o túnel seguro.
* **-i (Identity)**: O parâmetro que avisa: "Vou usar um arquivo de identidade em vez de senha".
* **"chave.pem"**: Minha chave privada (meu passaporte).
* **ec2-user**: O nome do usuário padrão dentro do Linux da Amazon.
* **@18.212.xxx.xxx**: O IP Público da instância (o endereço da máquina na nuvem).
* **Fingerprint** É a "assinatura" única do servidor. Serve para garantir que estou me conectando ao servidor certo e não a um impostor. Uma vez aceito (digitando `yes`), ele fica salvo no meu PC.
* **icacls**: Ferramenta do Windows para gerenciar permissões de arquivos.
* **inheritance:r**: Significa "remover herança". Ele limpa todos os usuários extras que o Windows colocou automaticamente na chave, deixando-a privada como o SSH exige.

### O Arquivo .PEM (Privacy Enhanced Mail)
* Esse arquivo é a minha **Chave Privada**. Ele nunca deve ser compartilhado ou subido para o GitHub. Se eu perder esse arquivo, perco o acesso ao servidor. Se alguém roubar, rouba meu acesso. Por isso, ele fica protegido no meu notebookzinho guerreiro.

---

---

### 📬 Contato

Sinta-se à vontade para se conectar comigo para trocarmos experiências sobre AWS e tecnologia!

* **LinkedIn:** [linkedin.com/in/fabio-figueiredo-5601041b2](https://www.linkedin.com/in/fabio-figueiredo-5601041b2)
* **E-mail:** [Fabio.sf10@hotmail.com](mailto:Fabio.sf10@hotmail.com)
* **Cargo:** *Analista de Suporte T.I. | Focado em AWS Cloud & AWS Certified Cloud Practitioner Path*

---
