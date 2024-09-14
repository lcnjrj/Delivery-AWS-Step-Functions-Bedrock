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

Inicialmente, a Lambda invoca a máquina de estados, uma implementação de RAG, e a utiliza como entrada para acionar a API InvokeModelWithResponseStream do Bedrock, resultando em uma resposta transmitida em stream ao solicitante. Para essa implementação, utilizamos o AWS Lambda Web Adapter em conjunto com o FastAPI, permitindo que a função Lambda seja acessada por meio de uma URL do Lambda configurada no modo de Response Stream. Graças à resposta em stream, o TTFB (Time to First Byte) é reduzido, melhorando significativamente a experiência do usuário do assistente GenAI e se adequando aos cenários de serviços voltados para os clientes finais via web.

A arquitetura de engenharia de prompt desta solução emprega a técnica de Prompt Chaining, onde as funções Lambda recebem instruções através do parâmetro system, guiando o comportamento do modelo desde a primeira invocação. Este parâmetro contém as instruções para o assistente interagir com o usuário, incluindo especificações sobre o papel, o tom e diretrizes gerais para a interação. Inicialmente, o primeiro prompt é crucial para estabelecer técnicas complexas como Role Playing e Few-Shot Prompting.

A Lambda também atua como a última subtarefa do encadeamento de prompts, pronta para utilizar modelos poderosos como os da série Claude. Essa configuração permite a implementação da API InvokeModelWithResponseStream, que, embora envolva modelos com maior tempo de resposta, aproveita o TTFB para oferecer uma resposta com menor latência, melhorando significativamente a responsividade durante as interações com os usuários.

Aqui estão alguns cenários onde este padrão pode ser usado:

RAG: Use Step Functions para invocar Bedrock, enriquecendo a entrada do usuário com palavras-chave relevantes ao contexto da conversa e, em seguida, realizar uma busca semântica através das bases de conhecimento do Amazon Bedrock. As tarefas de adicionar palavras-chave e invocar bases de conhecimento, sendo de baixa latência, permitem que a resposta ao usuário seja gerada via stream, melhorando a experiência.

Router: Uma máquina de estado do Step Functions pode atuar como um roteador para combinar cenários determinísticos e não determinísticos, como identificar um potencial churn de cliente e iniciar um workflow de retenção.

Testes A/B: Utilize testes A/B nas Step Functions para uma implementação rápida e low-code, testando diferentes experimentos, fazendo ajustes e selecionando o melhor para seu negócio. Enquanto se concentra nas regras de negócio, a função Lambda serve como uma abstração de interface, eliminando a necessidade de alterar o código ou o contrato da API para cada experimento.

Chamadas de API: A entrada do usuário pode solicitar ao LLM que gere dados em formatos como JSON, XML ou uma consulta SQL baseada em uma estrutura de tabela. Esses dados podem então ser usados pelas Step Functions para realizar tarefas que envolvem chamar APIs e executar consultas SQL em bancos de dados. Além disso, a função Lambda pode utilizar a saída das Step Functions para fornecer respostas em stream e explicar os dados gerados.

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
A segunda tarefa verifica o contexto da conversa e retorna verdadeiro/falso para validar se a recuperação de uma Base de Conhecimento é realmente necessária. Quando evitada, isso reduzirá a latência da resposta.
Se o resultado for verdadeiro, a Base de Conhecimento é invocada usando a pergunta do usuário + as palavras-chave adicionadas, caso contrário, nenhum conteúdo será adicionado. Observe que há um manipulador de erro se a tarefa de Recuperação falhar. Ele adiciona o conteúdo do erro ao contex_output e usa o prompt_chain_data para modificar as instruções originais.
Para passar uma resposta estruturada, o estado Pass é usado para formatar a saída JSON. Esta técnica pode ser usada para manter as Tarefas flexíveis e usar o estado de passagem para filtrar/formatar a saída para manter o contrato da API.
Cada Tarefa que realiza a invocação ao Bedrock tem um prompt específico, e você pode tentar modificar/adicionar/excluir para experimentar a solução. Neste exemplo, esses prompts não são usados diretamente para formatar a resposta final do usuário, e sim para determinar definir o comportamento da arquitetura RAG. O último passo de engenharia de prompt é tratado pelo lambda para executar a resposta em stream, e é possível transformar a instrução original através do parâmetro prompt_chain_data que pode ser inserido na máquina de estados do Step Functions.

Para mais detalhes da implementação acesse o repositório da solução.

Limpeza
Para excluir a infraestrutura criada, em um terminal execute:

cd serverless-genai-assistant/examples/serverless-assistant-rag
sam delete
Bash
Conclusão
Ao explorar o potencial da Inteligência Artificial Generativa para o atendimento ao cliente, fica claro que integrar tecnologias como AWS Lambda, AWS Step Functions e Amazon Bedrock pode melhorar a eficiência e a personalização das soluções. Esta solução de assistente virtual, descrita neste post, facilita a implementação de técnicas avançadas de engenharia de prompt e permite a experimentação rápida com modelos de linguagem de grande escala.

Utilizando respostas via stream e a arquitetura serverless do AWS Lambda, juntamente com a flexibilidade dos workflows do Step Functions, esta abordagem visa resolver eficazmente os desafios de latência e personalização. A implementação proposta busca oferecer uma experiência de usuário aprimorada, com respostas rápidas e contextualizadas.

Esta solução serve como um ponto de partida, a partir do qual podemos desenvolver muitas outras abordagens semelhantes, aproveitando as melhores práticas aqui apresentadas. Encorajamos os desenvolvedores a explorar e adaptar esta metodologia para aprimorar não apenas a eficiência operacional, mas também para oferecer um atendimento ao cliente mais eficaz. A experimentação e adaptação contínua são fundamentais para manter a relevância e competitividade no mercado atual.



Nas invocações ao Bedrock, definidas nas tarefas do Step Function, cada tarefa que realiza a invocação ao Bedrock pode ter seu próprio prompt. Os prompts devem ser projetados de maneira concisa e direta nas tarefas das Step Functions, com foco na resolução de problemas específicos. Isso incentiva a experimentação com diferentes modelos para otimizar os resultados.



