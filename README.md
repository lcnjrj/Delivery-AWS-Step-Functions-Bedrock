# Delivery-AWS-Step-Functions-Bedrock
Desafio DIO  Assistente de Delivery com AWS Step Functions e Bedrock


Objetivo: construir um sistema de entrega completo, com foco em automatizar o processo de pedidos e oferecer uma experiência personalizada para os clientes.

O uso do AWS Step Functions para orquestrar o fluxo de processamento de pedidos permite que você defina os passos do processo de forma visual e gerencie as dependências entre eles. 

O AWS Lambda é ideal para executar as funções que processam cada etapa do pedido, como a criação, o processamento e a entrega. 

O AWS DynamoDB é uma ótima opção para armazenar os dados dos pedidos de forma escalável e eficiente. 

AWS Bedrock é perfeito para criar um chatbot com inteligência artificial que pode interagir com os clientes de forma natural e personalizada.

O AWS SNS para enviar notificações sobre o status dos pedidos é uma ótima maneira de manter os clientes informados sobre o andamento de suas entregas.
E a arquitetura serverless garante que o sistema seja escalável.
Com essa combinação de serviços da AWS, terá um sistema de entrega completo, eficiente e escalável.

Como funciona um assistente virtual de delivery:

1. Fazendo seu pedido:

Você usa um aplicativo ou site para fazer seu pedido.
O sistema registra seu pedido e guarda todas as informações importantes, o que você comprou, o endereço de entrega e o método de pagamento.

O sistema entra em ação, organizando cada etapa da entrega.

Usa o AWS Step Functions para definir os passos do processo:
Recebe o pedido do cliente.
Verifica a disponibilidade do produto.
Calcula o custo de entrega baseado na localização.
Confirma o pedido com base na disponibilidade do produto.
Se disponível, prossegue para o processamento de pagamento.
Se indisponível, notifica o cliente da indisponibilidade.
Processa o pagamento do cliente.
Notifica o cliente sobre a confirmação do pedido.
Acompanha o status da entrega até a conclusão.

Cada passo é realizado por um "robô" chamado AWS Lambda, que executa tarefas específicas, como enviar mensagens ou atualizar o status do pedido.

2. Acompanhamento em tempo real:

Pode acompanhar o status do seu pedido em tempo real, através de um aplicativo ou site.
O sistema envia notificações por SMS ou e-mail para te manter atualizado sobre cada etapa da entrega.

3. AWS Bedrock

O AWS Bedrock, é um chatbot com inteligência artificial.
Pode conversar com ele para tirar dúvidas sobre o seu pedido, como:
"Onde está meu pedido?"
"Quando ele vai chegar?"
"Posso mudar o endereço de entrega?"
O chatbot te responde de forma rápida e amigável, como se você estivesse falando com uma pessoa real.

4. Escalabilidade:

O sistema se adapta à demanda.
Se você fizer muitos pedidos ao mesmo tempo, ele aumenta automaticamente a capacidade.
Se você fizer poucos pedidos, ele diminui a capacidade para economizar recursos.
