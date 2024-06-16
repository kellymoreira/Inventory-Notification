[ᴇɴɢʟɪꜱʜ](https://github.com/kellymoreira/Inventory-Notification/tree/main) | ᴘᴏʀᴛᴜɢᴜᴇꜱᴇ-ʙʀ

<p align="center">
   <img src="./images/notification-icon.png" alt="Ícone de Notificação" width="23%"/>
</p>

<h1 align="center">
    Notificação de Inventário
</h1>

<p align="center">
<b> 
<i>
   Implementando uma arquitetura serverless na AWS para rastrear e notificar níveis baixos de inventário usando <br> S3, Lambda, DynamoDB e SNS.
</i>
</b>
</p>

## **Cenário**
Estamos criando um sistema de rastreamento de inventário.
Lojas em todo o mundo irão carregar arquivos de inventário para o Amazon S3.
Queremos ser capazes de visualizar os níveis de inventário e enviar notificações quando os níveis estiverem baixos.

### Processo:
- **Carregar** um **arquivo de inventário** em um **_bucket do Amazon S3_**.
- Esse carregamento **aciona** uma **_função Lambda_** para ler o arquivo e inserir itens em uma tabela **_Amazon DynamoDB_**.
- Um aplicativo de painel web serverless usará **_Amazon Cognito_** para autenticação AWS. Ele então obterá acesso à tabela **_DynamoDB_** para exibir os níveis de inventário.
- Outra **_função Lambda_** receberá atualizações da tabela **_DynamoDB_**. Esta função enviará uma mensagem para um **tópico SNS** quando um item de inventário estiver fora de estoque.
- **_Amazon SNS_** então **enviará** uma **notificação** por serviço de mensagem curta (SMS) ou email para solicitar inventário adicional.

## **Visão Geral da Implementação**
Tradicionalmente, aplicações rodam em servidores físicos ou virtuais que exigem compra, provisionamento e gerenciamento de capacidade.
Com o **_AWS Lambda_**, você executa código sem servidores pré-alocados, acionados conforme necessário, e paga apenas pelo uso.

Esta configuração aciona uma **_função Lambda_** quando um arquivo é carregado no **_Amazon S3_**, carregando dados em uma tabela **_Amazon DynamoDB_**.
Os dados são exibidos em um painel que os obtém diretamente do **_DynamoDB_**.
Esta solução serverless escala automaticamente e gera custos mínimos quando ociosa.

### Objetivos:
- Implementar uma arquitetura serverless na AWS
- Acionar **_funções Lambda_** a partir do **_Amazon S3_** e **_DynamoDB_**
- Configurar **_Amazon SNS_** para notificações

## Arquitetura Final
![Visão Geral da Arquitetura](/images/architecture-overview.png)
