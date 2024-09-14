Teoria e resumo

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

![image](https://github.com/user-attachments/assets/9da6d367-a2a8-42ed-a8eb-c432bbe53c6d)

Figura 1. Arquitetura da solução, tenda a Lambda sendo exposta ao cliente via Lambda URL, invocando uma máquina de estado do Step Functions, para depois invocar o modelo via Bedrock com response stream.


Como Funciona?
O exemplo usa Lambda para operar uma máquina de estados construída com o Workflow Express Síncrono do AWS Step Functions, projetada especificamente para orquestrar múltiplas solicitações às APIs do Amazon Bedrock.

Inicialmente, a Lambda invoca a máquina de estados, uma implementação de RAG, e a utiliza como entrada para acionar a API InvokeModelWithResponseStream do Bedrock, resultando em uma resposta transmitida em stream ao solicitante.

Para essa implementação, utilizamos o AWS Lambda Web Adapter em conjunto com o FastAPI, permitindo que a função Lambda seja acessada por meio de uma URL do Lambda configurada no modo de Response Stream. 

Graças à resposta em stream, o TTFB (Time to First Byte) é reduzido, melhorando significativamente a experiência do usuário do assistente GenAI e se adequando aos cenários de serviços voltados para os clientes finais via web.

A arquitetura de engenharia de prompt desta solução emprega a técnica de Prompt Chaining, onde as funções Lambda recebem instruções através do parâmetro system, guiando o comportamento do modelo desde a primeira invocação.

Este parâmetro contém as instruções para o assistente interagir com o usuário, incluindo especificações sobre o papel, o tom e diretrizes gerais para a interação. 

Inicialmente, o primeiro prompt é crucial para estabelecer técnicas complexas como Role Playing e Few-Shot Prompting.

A Lambda também atua como a última subtarefa do encadeamento de prompts, pronta para utilizar modelos poderosos como os da série Claude.

Essa configuração permite a implementação da API InvokeModelWithResponseStream, que, embora envolva modelos com maior tempo de resposta, aproveita o TTFB para oferecer uma resposta com menor latência, melhorando significativamente a responsividade durante as interações com os usuários.

Aqui estão alguns cenários onde este padrão pode ser usado:

RAG: Use Step Functions para invocar Bedrock, enriquecendo a entrada do usuário com palavras-chave relevantes ao contexto da conversa e, em seguida, realizar uma busca semântica através das bases de conhecimento do Amazon Bedrock. 

As tarefas de adicionar palavras-chave e invocar bases de conhecimento, sendo de baixa latência, permitem que a resposta ao usuário seja gerada via stream, melhorando a experiência.

Router: Uma máquina de estado do Step Functions pode atuar como um roteador para combinar cenários determinísticos e não determinísticos, como identificar um potencial churn de cliente e iniciar um workflow de retenção.

Testes A/B: Utilize testes A/B nas Step Functions para uma implementação rápida e low-code, testando diferentes experimentos, fazendo ajustes e selecionando o melhor para seu negócio. 

Enquanto se concentra nas regras de negócio, a função Lambda serve como uma abstração de interface, eliminando a necessidade de alterar o código ou o contrato da API para cada experimento.

Chamadas de API: A entrada do usuário pode solicitar ao LLM que gere dados em formatos como JSON, XML ou uma consulta SQL baseada em uma estrutura de tabela.

Esses dados podem então ser usados pelas Step Functions para realizar tarefas que envolvem chamar APIs e executar consultas SQL em bancos de dados. 

Além disso, a função Lambda pode utilizar a saída das Step Functions para fornecer respostas em stream e explicar os dados gerados.

----------------------------------------------------------------------------------------------

Passo a Passo
Pré-requisitos
Para rodar a solução você irá precisar:

AWS Account
AWS SAM
Acesso aos modelos Anthropic Claude 3 (Sonnet e Haiku) e Meta Llama 3
Criar uma Knowledge base for Rag de exemplo
Se você não fornecer um ID de KnowledgeBaseId, você receberá uma resposta do LLM informando um erro do Bedrock.
Para clonar o repositório, execute:

git clone https://github.com/aws-samples/serverless-genai-assistant.git
Bash
Deployment
Depois de clonar o repositório, execute os seguintes comandos do AWS SAM para construir e publicar a solução em sua conta.

cd serverless-genai-assistant/examples/serverless-assistant-rag
sam build sam deploy --guided
Bash
Para o passo a passo de deployment, defina as seguintes opções:

Stack Name []: # Escolha um nome para o stack

AWS Region [us-east-1]: # Selecione uma Região que suporte o Amazon Bedrock e os outros serviços AWS
Parameter KnowledgeBaseId []: # Insira o ID da KB https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base-manage.html#kb-

# Mostra as mudanças nos recursos a serem implantados e requer um 'Y' para iniciar a implantação

Confirm changes before deploy [y/N]:

# O SAM precisa de permissão para criar funções para conectar aos recursos em seu template
Allow SAM CLI IAM role creation [Y/n]:

# Preserva o estado dos recursos previamente provisionados quando uma operação falha
Disable rollback [y/N]:
FastAPIFunction Function Url has no authentication. Is this okay? [y/N]: y
Save arguments to configuration file [Y/n]:
SAM configuration file [samconfig.toml]:
SAM configuration environment [default]:

Bash
Copie o valor de TheFastAPIFunctionUrl, é a URL para invocar o Lambda.

Execução
Via Terminal

cd serverless-genai-assistant/examples/serverless-assistant-rag/tests
curl --no-buffer -H "Content-Type: application/json" -X POST -d @test.json <lambda-url>

Bash
Via Python

cd serverless-genai-assistant/examples/serverless-assistant-rag/tests
pip install requests
python test_stream_python_requests.py --lambda-url <lambda-url>

Bash

Com Streamlit
cd serverless-genai-assistant/examples/serverless-assistant-rag/tests
pip install requests
pip install streamlit
streamlit run st_serverless_assistant.py -- --lambda-url <lambda-url>

Bash
Detalhes da Implementação

Figura 2. Implementação da RAG com maquina de estado.

Usamos a máquina de estados em examples/serverless_assistant_rag para exemplificar como o fluxo de trabalho funciona, ela implementa uma arquitetura RAG que pode ser usada com modelos Claude que utilizam a API de Mensagens. Você também pode combinar com outros modelos que usam a API de Completamento, tratando o payload usando ASL:

Recebe os dados de entrada do lambda e os paraleliza para duas tarefas da API Bedrock.

A primeira tarefa aprimora a pergunta do usuário adicionando palavras-chave para aumentar a busca semântica.

A segunda tarefa verifica o contexto da conversa e retorna verdadeiro/falso para validar se a recuperação de uma Base de Conhecimento é realmente necessária.
Quando evitada, isso reduzirá a latência da resposta.

Se o resultado for verdadeiro, a Base de Conhecimento é invocada usando a pergunta do usuário + as palavras-chave adicionadas, caso contrário, nenhum conteúdo será adicionado. 
Observe que há um manipulador de erro se a tarefa de Recuperação falhar. 

Ele adiciona o conteúdo do erro ao contex_output e usa o prompt_chain_data para modificar as instruções originais.

Para passar uma resposta estruturada, o estado Pass é usado para formatar a saída JSON. 
Esta técnica pode ser usada para manter as Tarefas flexíveis e usar o estado de passagem para filtrar/formatar a saída para manter o contrato da API.

Cada Tarefa que realiza a invocação ao Bedrock tem um prompt específico, e você pode tentar modificar/adicionar/excluir para experimentar a solução.

Neste exemplo, esses prompts não são usados diretamente para formatar a resposta final do usuário, e sim para determinar definir o comportamento da arquitetura RAG. 

O último passo de engenharia de prompt é tratado pelo lambda para executar a resposta em stream, e é possível transformar a instrução original através do parâmetro prompt_chain_data que pode ser inserido na máquina de estados do Step Functions.

Para mais detalhes da implementação acesse o repositório da solução.

Limpeza
Para excluir a infraestrutura criada, em um terminal execute:

cd serverless-genai-assistant/examples/serverless-assistant-rag

sam delete

Bash

Conclusão

Utilizando respostas via stream e a arquitetura serverless do AWS Lambda, juntamente com a flexibilidade dos workflows do Step Functions, esta abordagem visa resolver eficazmente os desafios de latência e personalização.

A implementação proposta busca oferecer uma experiência de usuário aprimorada, com respostas rápidas e contextualizadas.

----------------teoria -----------------

Criar uma máquina de estado a partir de um fluxo em branco para gerenciar um processo de entrega.

### Passo 1: Configurar AWS Lambda

1. **Crie uma conta na AWS**: Se ainda não tiver uma conta na AWS, acesse [aws.amazon.com](https://aws.amazon.com/) e siga as instruções para criar uma.

2. **Acesse o AWS Lambda**: No Console de Gerenciamento da AWS, vá para o serviço AWS Lambda.

3. **Crie uma Função Lambda**:
   - Clique em **"Create function"**.
   - Escolha **"Author from scratch"**.
   - Nomeie a função, por exemplo, `ReceiveOrderFunction`.
   - Escolha um runtime (Python é uma escolha comum para iniciantes).
   - Clique em **"Create function"**.

4. **Desenvolva a Função Lambda**:
   - No editor de código, insira o código que processa o pedido. 
     ```python
     import json

     def lambda_handler(event, context):
         # Simula a recepção de um pedido
         return {
             'statusCode': 200,
             'body': json.dumps('Pedido recebido')
         }
     ```

5. **Repita o processo** para criar funções Lambda para os seguintes passos:
   - **`CheckAvailabilityFunction`**: Verifica a disponibilidade do produto.
   - **`CalculateDeliveryCostFunction`**: Calcula o custo de entrega.
   - **`ConfirmOrderFunction`**: Confirma o pedido.
   - **`NotifyCustomerFunction`**: Notifica o cliente.
   - **`ProcessPaymentFunction`**: Processa o pagamento.
   - **`TrackDeliveryFunction`**: Acompanha o status da entrega.

### Passo 2: Criar a Máquina de Estado com AWS Step Functions

1. **Acesse o AWS Step Functions**: No Console de Gerenciamento da AWS, vá para o serviço AWS Step Functions.

2. **Crie uma Nova Máquina de Estado**:
   - Clique em **"Create state machine"**.
   - Selecione **"Write your workflow in code"**.
   - Escolha um nome para a sua máquina de estado, por exemplo, `DeliveryWorkflow`.

3. **Defina o Fluxo de Trabalho**:
   - No editor de código, definina o fluxo de trabalho usando a linguagem de definição de estado (Amazon States Language). Exemplo básico do fluxo de trabalho descrito:
     ```json
     {
       "Comment": "Assistente de Delivery",
       "StartAt": "ReceiveOrder",
       "States": {
         "ReceiveOrder": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ReceiveOrderFunction",
           "Next": "CheckAvailability"
         },
         "CheckAvailability": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:CheckAvailabilityFunction",
           "Next": "IsAvailable"
         },
         "IsAvailable": {
           "Type": "Choice",
           "Choices": [
             {
               "Variable": "$.availability",
               "BooleanEquals": true,
               "Next": "CalculateDeliveryCost"
             }
           ],
           "Default": "NotifyCustomer"
         },
         "CalculateDeliveryCost": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:CalculateDeliveryCostFunction",
           "Next": "ConfirmOrder"
         },
         "ConfirmOrder": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ConfirmOrderFunction",
           "Next": "ProcessPayment"
         },
         "ProcessPayment": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ProcessPaymentFunction",
           "Next": "NotifyCustomer"
         },
         "NotifyCustomer": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:NotifyCustomerFunction",
           "Next": "TrackDelivery"
         },
         "TrackDelivery": {
           "Type": "Task",
           "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:TrackDeliveryFunction",
           "End": true
         }
       }
     }
     ```
   - Substitua `REGION` e `ACCOUNT_ID` pelos valores apropriados para a sua conta AWS.

4. **Configure Permissões**:
   - As funções Lambda precisam de permissões para serem invocadas pela AWS Step Functions. Certifique-se de que a política de execução da Step Functions inclua permissões para invocar suas funções Lambda.

5. **Crie e Execute o Fluxo de Trabalho**:
   - Clique em **"Create state machine"**.
   - Depois, inicie uma nova execução para testar o fluxo de trabalho e verificar se todas as funções Lambda estão sendo chamadas conforme esperado.

### Passo 3: Testar e Monitorar

1. **Teste o Fluxo de Trabalho**: Envie um pedido de teste para a sua máquina de estado e observe o progresso dos estados no console do Step Functions.

2. **Monitore e Depure**: Utilize o console do AWS Lambda para verificar logs e depurar funções individuais. O console do Step Functions fornece visualizações úteis sobre o status de cada execução e onde podem ocorrer falhas.

### Considerações Finais

- **Segurança**: Garanta que as permissões e configurações de segurança estejam corretas para proteger suas funções Lambda e seus dados.
- **Custos**: Fique atento aos custos associados ao uso do AWS Lambda e AWS Step Functions, especialmente se o volume de transações for alto.

Nas invocações ao Bedrock, definidas nas tarefas do Step Function, cada tarefa que realiza a invocação ao Bedrock pode ter seu próprio prompt. 
Os prompts devem ser projetados de maneira concisa e direta nas tarefas das Step Functions, com foco na resolução de problemas específicos.

Para criar um Assistente de Delivery usando o AWS Step Functions Workflow Studio e a Amazon States Language (ASL)

O Workflow Studio é uma interface visual para criar e editar fluxos de trabalho com o AWS Step Functions, enquanto o ASL é a linguagem JSON usada para definir os fluxos de trabalho.

### Passo 1: Criar Funções Lambda

Antes de começar com o Workflow Studio, você deve ter as funções Lambda configuradas.
Aqui está um lembrete rápido para criar essas funções:

1. **Crie uma Função Lambda para Cada Etapa do Processo**:
   - `ReceiveOrderFunction`
   - `CheckAvailabilityFunction`
   - `CalculateDeliveryCostFunction`
   - `ConfirmOrderFunction`
   - `ProcessPaymentFunction`
   - `NotifyCustomerFunction`
   - `TrackDeliveryFunction`

### Passo 2: Acessar o AWS Step Functions Workflow Studio

1. **Acesse o AWS Step Functions**:
   - No Console de Gerenciamento da AWS, vá para o serviço **AWS Step Functions**.

2. **Inicie o Workflow Studio**:
   - Clique em **"Create state machine"**.
   - Selecione **"Workflow Studio"** para usar a interface visual.

### Passo 3: Criar o Fluxo de Trabalho no Workflow Studio

1. **Iniciar um Novo Fluxo de Trabalho**:
   - Clique em **"Create new workflow"**.

2. **Adicionar os Passos**:
   - **Receber o Pedido**:
     - Arraste e solte um bloco de **"Task"** na tela.
     - Configure a **"Task"** para usar a função Lambda `ReceiveOrderFunction`.
     - Nomeie o bloco como "ReceiveOrder".
     - 
   - **Verificar Disponibilidade**:
     - Arraste outro bloco de **"Task"** e conecte-o ao bloco "ReceiveOrder".
     - Configure este bloco para usar a função Lambda `CheckAvailabilityFunction`.
     - Nomeie o bloco como "CheckAvailability".
     - 
   - **Condições de Disponibilidade**:
     - Arraste um bloco de **"Choice"** e conecte-o ao bloco "CheckAvailability".
     - Configure as condições no bloco **"Choice"**:
       - Se `$.availability` for verdadeiro, prossiga para o bloco **"CalculateDeliveryCost"**.
       - Caso contrário, prossiga para o bloco **"NotifyCustomer"**.
       - 
   - **Calcular Custo de Entrega**:
     - Adicione um bloco de **"Task"** e conecte-o ao bloco **"Choice"** para o caso positivo.
     - Configure para usar a função Lambda `CalculateDeliveryCostFunction`.
     - Nomeie o bloco como "CalculateDeliveryCost".
     - 
   - **Confirmar Pedido**:
     - Arraste outro bloco de **"Task"** e conecte-o ao bloco "CalculateDeliveryCost".
     - Configure para usar a função Lambda `ConfirmOrderFunction`.
     - Nomeie o bloco como "ConfirmOrder".
     - 
   - **Processar Pagamento**:
     - Arraste um bloco de **"Task"** e conecte-o ao bloco "ConfirmOrder".
     - Configure para usar a função Lambda `ProcessPaymentFunction`.
     - Nomeie o bloco como "ProcessPayment".
     - 
   - **Notificar Cliente**:
     - Adicione um bloco de **"Task"** e conecte-o ao bloco "ProcessPayment".
     - Configure para usar a função Lambda `NotifyCustomerFunction`.
     - Nomeie o bloco como "NotifyCustomer".
     - 
   - **Acompanhar Entrega**:
     - Adicione um bloco de **"Task"** e conecte-o ao bloco "NotifyCustomer".
     - Configure para usar a função Lambda `TrackDeliveryFunction`.
     - Nomeie o bloco como "TrackDelivery".

3. **Configurar Transições**:
   - Certifique-se de que todos os blocos estão conectados corretamente de acordo com o fluxo do processo.

4. **Definir o Estado Final**:
   - No bloco "TrackDelivery", defina-o como o **"End"** do fluxo de trabalho.

### Passo 4: Definir e Configurar a Máquina de Estado

1. **Salve e Publique**:
   - Clique em **"Save"** para salvar o seu fluxo de trabalho.
   - Clique em **"Publish"** para disponibilizar o fluxo de trabalho.

2. **Verificar e Testar**:
   - No console do AWS Step Functions, vá para a nova máquina de estado e inicie uma execução para testar o fluxo de trabalho.
   - 
   - Monitore a execução para garantir que todas as funções Lambda são chamadas corretamente e que o fluxo segue conforme esperado.

### Passo 5: Configurar Permissões

1. **Permissões**:
   - Certifique-se de que as funções Lambda têm permissões para serem invocadas pela Step Functions.
   - 
   - Verifique a política de execução da máquina de estado para garantir que ela tenha permissões para invocar as funções Lambda.

### Passo 6: Monitorar e Depurar

1. **Monitorar Executions**:
   - Utilize o Console do Step Functions para monitorar o progresso das execuções e depurar qualquer problema que possa surgir.

2. **Verificar Logs**:
   - Use o Console do AWS Lambda para acessar os logs e verificar o funcionamento das funções Lambda.

### Exemplo de Definição ASL

Se preferir definir o fluxo de trabalho usando ASL diretamente, o código JSON equivalente ao Workflow Studio seria algo assim:

```json
{
  "Comment": "Assistente de Delivery",
  "StartAt": "ReceiveOrder",
  "States": {
    "ReceiveOrder": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ReceiveOrderFunction",
      "Next": "CheckAvailability"
    },
    "CheckAvailability": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:CheckAvailabilityFunction",
      "Next": "IsAvailable"
    },
    "IsAvailable": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.availability",
          "BooleanEquals": true,
          "Next": "CalculateDeliveryCost"
        }
      ],
      "Default": "NotifyCustomer"
    },
    "CalculateDeliveryCost": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:CalculateDeliveryCostFunction",
      "Next": "ConfirmOrder"
    },
    "ConfirmOrder": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ConfirmOrderFunction",
      "Next": "ProcessPayment"
    },
    "ProcessPayment": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ProcessPaymentFunction",
      "Next": "NotifyCustomer"
    },
    "NotifyCustomer": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:NotifyCustomerFunction",
      "Next": "TrackDelivery"
    },
    "TrackDelivery": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:TrackDeliveryFunction",
      "End": true
    }
  }
}
```

Substitua `REGION` e `ACCOUNT_ID` pelos valores corretos para suas funções Lambda.

Este fluxo de trabalho e a definição ASL criarão uma máquina de estado que gerencia o processo de delivery de forma automática e eficiente usando AWS Step Functions e Lambda.

Para integrar o AWS Bedrock com o AWS Step Functions e configurar um fluxo de trabalho que inclua a verificação de disponibilidade de modelos e permissões, você precisará seguir alguns passos para configurar o ambiente e criar um fluxo de trabalho eficiente. Vou descrever o processo em detalhes para você.

### Passo 1: Entender o AWS Bedrock

O AWS Bedrock é um serviço da AWS que fornece acesso a modelos de linguagem pré-treinados para criação de soluções de inteligência artificial. Para usar o Bedrock com Step Functions, você precisará:

1. **Verificar a Disponibilidade do Modelo**: Verificar se o modelo desejado está disponível na sua região.
2. **Solicitar o Modelo**: Configurar e solicitar o modelo para uso.
3. **Configurar Permissões**: Garantir que seu perfil de usuário e o serviço AWS tenha as permissões adequadas para acessar e usar o modelo.

### Passo 2: Verificar a Disponibilidade do Modelo

1. **Acesse o AWS Bedrock**:
   - No Console de Gerenciamento da AWS, vá para o serviço AWS Bedrock.
   - Verifique se o modelo desejado está listado e disponível na sua região.

2. **Confirmar Disponibilidade na Região**:
   - Se o modelo não estiver disponível na região padrão, você pode precisar mudar para uma região onde o modelo está disponível ou solicitar acesso.

### Passo 3: Configurar o Modelo

1. **Solicitar e Configurar o Modelo**:
   - No console do AWS Bedrock, solicite o modelo desejado (por exemplo, "Haiku").
   - Configure o modelo de acordo com suas necessidades (e.g., ajustar parâmetros de execução).

2. **Permissões de Modelo**:
   - Certifique-se de que você tem permissões para acessar e usar o modelo. Pode ser necessário configurar permissões adicionais no IAM (Identity and Access Management) para acessar o modelo de forma programática.

### Passo 4: Configurar Permissões para o Usuário e Serviço

1. **Permissões do Perfil do Usuário**:
   - No Console de IAM, crie ou edite um perfil de usuário para garantir que ele tenha permissões adequadas para interagir com o AWS Bedrock e os modelos.
   - A política de permissões pode incluir permissões para acessar o serviço Bedrock e o modelo específico.

2. **Permissões do Serviço AWS**:
   - Certifique-se de que o serviço Step Functions tenha permissões para invocar o Bedrock e acessar o modelo.
   - Atualize a política de execução do Step Functions para incluir permissões para invocar as funções Lambda e o serviço Bedrock.

3. **Permissões do Modelo "Haiku"**:
   - Configure permissões específicas para acessar e usar o modelo "Haiku" no AWS Bedrock.

### Passo 5: Integrar AWS Step Functions com AWS Bedrock

1. **Criar Funções Lambda para Interagir com o Bedrock**:
   - Desenvolva funções Lambda que invocarão o modelo Bedrock para processamento de dados.
   - Exemplo de código Lambda em Python para chamar um modelo do Bedrock:

     ```python
     import boto3

     def lambda_handler(event, context):
         client = boto3.client('bedrock')
         response = client.invoke_model(
             ModelId='model-haiku-id',  # Substitua com o ID do seu modelo
             Body=event['body'],
         )
         return {
             'statusCode': 200,
             'body': response['Body']
         }
     ```

2. **Configurar o Fluxo de Trabalho no Step Functions**:
   - No Workflow Studio, adicione uma nova **"Task"** para invocar a função Lambda que interage com o Bedrock.
   - Configure a Task para chamar a função Lambda que usa o modelo "Haiku".

3. **Exemplo de Definição ASL com Bedrock**:

   ```json
   {
     "Comment": "Assistente de Delivery com Bedrock",
     "StartAt": "ReceiveOrder",
     "States": {
       "ReceiveOrder": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ReceiveOrderFunction",
         "Next": "CheckAvailability"
       },
       "CheckAvailability": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:CheckAvailabilityFunction",
         "Next": "IsAvailable"
       },
       "IsAvailable": {
         "Type": "Choice",
         "Choices": [
           {
             "Variable": "$.availability",
             "BooleanEquals": true,
             "Next": "CalculateDeliveryCost"
           }
         ],
         "Default": "NotifyCustomer"
       },
       "CalculateDeliveryCost": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:CalculateDeliveryCostFunction",
         "Next": "ConfirmOrder"
       },
       "ConfirmOrder": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ConfirmOrderFunction",
         "Next": "ProcessPayment"
       },
       "ProcessPayment": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:ProcessPaymentFunction",
         "Next": "NotifyCustomer"
       },
       "NotifyCustomer": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:NotifyCustomerFunction",
         "Next": "TrackDelivery"
       },
       "TrackDelivery": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:TrackDeliveryFunction",
         "End": true
       }
     }
   }
   ```

### Passo 6: Testar e Validar

1. **Teste o Fluxo de Trabalho**:
   - No Console de Step Functions, inicie uma nova execução do fluxo de trabalho e observe se a função Lambda interage corretamente com o AWS Bedrock.

2. **Monitorar Logs e Resultados**:
   - Use o console do AWS Lambda para monitorar logs e depurar qualquer problema com a integração.

### Considerações Finais

- **Verifique Configurações Regionais**: Assegure-se de que os serviços e recursos estejam disponíveis na região escolhida.
- **Gerencie Permissões com Cuidado**: Garanta que as permissões estejam configuradas corretamente para evitar problemas de acesso.

Com esses passos, você configurará um fluxo de trabalho usando AWS Step Functions e AWS Bedrock, garantindo que as permissões e a configuração do modelo estejam adequadas para seu ambiente.
