# 🔐 Automated SSL Certificate Monitoring and Renewal with Notion and Telegram

Este projeto é um fluxo do **[n8n](https://n8n.io/)** que automatiza o **monitoramento, atualização e notificação de certificados SSL**, integrando com **Notion** e **Telegram**.

## 🚀 Funcionalidades
- 🔍 **Monitoramento automático** de certificados SSL semanalmente.
- 📊 **Registro no Notion** com datas de criação, expiração e status atualizado.
- ⚠️ **Alerta no Telegram** quando o certificado está prestes a expirar (menos de 14 dias).
- 🔄 **Renovação automática via Certbot** (usando conexão SSH com o servidor).
- ✅ **Confirmação no Telegram** após a renovação, garantindo que todos os certificados estão ativos.

## 🛠️ Tecnologias Utilizadas
- [n8n](https://n8n.io/) — Orquestrador de automações.
- [Notion API](https://developers.notion.com/) — Para armazenar e atualizar o status dos certificados.
- [Telegram Bot API](https://core.telegram.org/bots/api) — Para envio de notificações.
- [Certbot](https://certbot.eff.org/) — Para renovação automática de certificados SSL.
- [SSL Checker API](https://ssl-checker.io/) — Para verificar validade dos certificados.

## ⚙️ Como Funciona
1. O fluxo é disparado de três formas:
   - **Manual** (executando o workflow no n8n).
   - **Semanal** (trigger automático configurado).
   - **Por outro workflow** (execução encadeada).
   
2. O n8n consulta os domínios cadastrados no **Notion**.
3. Para cada domínio, o fluxo:
   - Verifica o certificado SSL via **SSL Checker API**.
   - Classifica o status:
     - ✅ Normal (mais de 14 dias restantes).
     - ⚠️ Prestes a expirar (menos de 14 dias).
     - ❌ Indisponível.
   - Atualiza os registros no **Notion**.
4. Se algum certificado estiver prestes a expirar:
   - Uma mensagem detalhada é enviada no **Telegram**.
   - O fluxo executa o comando **Certbot Renew** via SSH.
   - Após a renovação, o sistema espera e valida novamente.
   - Se tudo estiver ok, envia confirmação no **Telegram**.

## 📸 Fluxo (Visão Geral)
```
Notion → SSL Checker API → n8n → (If status) → Telegram + Certbot Renew → Confirmação
```

## 📂 Estrutura
- `Automated SSL Certificate Monitoring and Renewal with Notion and Telegram.json` → Workflow exportado do n8n.

## ▶️ Como Usar
1. **Importe** o arquivo JSON no seu n8n.
2. Configure as credenciais necessárias:
   - **Notion API** (database com os domínios).
   - **Telegram Bot API** (crie um bot com o [BotFather](https://t.me/botfather)).
   - **SSH** (para acessar seu servidor e rodar o Certbot).
3. Ajuste:
   - ID do banco de dados do Notion.
   - Caminho do Certbot no servidor (`/your/work/directory`).
4. Ative o workflow.

---

## 📑 Template do Banco de Dados no Notion

Crie uma database no Notion chamada **SSL Certificates** com as seguintes colunas:

| Coluna              | Tipo        | Descrição                                                                 |
|---------------------|-------------|---------------------------------------------------------------------------|
| **Name**            | Title       | Nome do domínio (ex: `example.com`).                                      |
| **URL**             | URL         | Endereço completo do domínio/serviço.                                     |
| **Creation Date**   | Date        | Data de emissão do certificado.                                           |
| **Expiration Date** | Date        | Data de expiração do certificado.                                         |
| **SSL Status**      | Select      | Status atual: `Normal`, `It's about to expire.`, `Unavailable`.           |
| **Last Check At**   | Date        | Última data/hora em que o fluxo verificou o certificado.                   |

> 💡 Dica: o campo **Name** é usado como chave para a API de verificação de SSL. Certifique-se de colocar apenas o domínio (sem `https://`).

---

## 📢 Notificações no Telegram
- Quando certificados estão prestes a expirar:
  ```
  [Notify] Automated SSL Certificate Monitoring
  1. exemplo.com: 10 days remaining.
  2. api.exemplo.com: 7 days remaining.
  
  The instruction to update the credentials has been automatically triggered and the system will check again.
  ```
- Quando tudo está normal:
  ```
  [Notify] Automated SSL Certificate Monitoring
  The SSL certificate has been updated and all certificates are normal.
  ```

---

👨‍💻 Criado para facilitar a **gestão proativa de SSL**, evitar downtime inesperado e centralizar informações em uma dashboard no Notion, com alertas em tempo real no Telegram.
