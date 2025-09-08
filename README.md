# NYC311 Customer Satisfaction (CSAT)

# 1. DOCUMENTAÇÃO

- **Informações Gerais**
    - **Título do Dataset:** 311_Customer_Satisfaction_Survey.csv
    - **Criador:** DataGov USA
    - **Período dos Dados:** Dados do ano de 2020 à 2024.
    - **Fonte:** https://catalog.data.gov/dataset/311-customer-satisfaction-survey

- **Título e Objetivo da Análise: Análise de Performance e Satisfação do Cliente no Atendimento NYC311**
    - Esta análise tem como objetivo principal explorar os dados da Pesquisa de Satisfação do Cliente (CSAT) do serviço NYC311 para extrair insights sobre a experiência do cidadão ao contatar o serviço público. Através da visualização e interpretação das respostas, buscamos entender os fatores que mais impactam a satisfação dos usuários.

- **Dicionário de Variáveis (Colunas)**

| Nome da Coluna | Tipo de Dado | Descrição |
| --- | --- | --- |
| Year | Numérico (Inteiro) | O ano em que a pesquisa de satisfação foi realizada. |
| Campaign | Texto/Categórico | Identificador da campanha de pesquisa específica. As campanhas ocorrem esporadicamente ao longo do ano. |
| Channel | Texto/Categórico | O canal de contato utilizado pelo cliente que originou o envio da pesquisa (ex: NYC311 Call Center). |
| Survey Type | Texto/Categórico | O tipo de pesquisa realizada. Neste caso, é uma Customer Satisfaction (CSAT) Survey. |
| Start Time | Data e Hora (Timestamp) | A data e a hora exatas em que o cliente iniciou o preenchimento da pesquisa. |
| Completion Time | Data e Hora (Timestamp) | A data e a hora exatas em que o cliente concluiu e enviou a pesquisa. |
| Survey Language | Texto/Categórico | O idioma no qual a pesquisa foi apresentada ao cliente (ex: English). |
| Overall Satisfaction | Categórico Ordinal | A resposta do cliente à pergunta sobre a satisfação geral com o atendimento. Provavelmente usa uma escala Likert (ex: Strongly Agree, Agree, Neutral). |
| Wait Time | Categórico Ordinal | A avaliação do cliente sobre sua satisfação com o tempo de espera antes de ser atendido. |
| Agent Customer Service | Categórico Ordinal | A avaliação do cliente sobre a qualidade do serviço prestado pelo agente (ex: cortesia, empatia). |
| Agent Job Knowledge | Categórico Ordinal | A percepção do cliente sobre o nível de conhecimento que o agente demonstrou ter para resolver a questão. |
| Answer Satisfaction | Categórico Ordinal | A satisfação do cliente com a resposta ou solução fornecida pelo agente durante o contato. |
| NPS | Numérico (Inteiro) | A nota do Net Promoter Score. É uma métrica que mede a lealdade do cliente, baseada em uma pergunta de 0 a 10 sobre a probabilidade de recomendar o serviço. |
- **Metodologia de Limpeza e Transformação (ETL):**
    - Limpeza feita em power query e substituição de termos que vieram com erro de codificação utilizando AI.
    - **Log de erros:**

| Tabela | Coluna | Problema | Linhas | Solucionável? | Magnitude | Solução |
| --- | --- | --- | --- | --- | --- | --- |
| 311_Customer_Satisfaction_Surve | Start Time | Formato de datas inconsistentes | 22792 | sim | 100.00% | padronização via power query com data local  English (US) |
| 311_Customer_Satisfaction_Surve | Completion Time | Formato de datas inconsistentes | 22792 | sim | 100.00% | padronização via power query com data local  English (US) |
| 311_Customer_Satisfaction_Surve | Survey Language | O texto original do arquivo foi salvo usando uma codificação (como UTF-8 ou ISO-8859-1), mas o software (como o Power Query) tentou abri-lo usando uma codificação diferente. Isso faz com que caracteres especiais (ñ, á, ü) apareçam de forma ilegível (Ã±, Ã¡, Ã¼). | 22792 | sim | 100.00% | Decodificação utilizando IA para solucionar os idiomas corretos, e substituição Find and preplace (Ctrl+H) para a limpeza de dados |

# 2. ANÁLISE EXPLORATÓRIA

## **1: Distribuição das Principais Métricas de Satisfação**

- **Objetivo:** Entender a frequência das respostas de `Overall Satisfaction` e, crucialmente, **classificar cada respondente** em Detrator, Passivo ou Promotor para calcular e visualizar a composição do Net Promoter Score (NPS).
- **Variáveis:** `Overall Satisfaction`, `NPS Category`
- **Gráfico & Tabela:**

<img src="/images/01_Distribuio_das_Principais_Mtricas_de_Satisfao.png" alt="Distribuição da satisfação e categorias nps" width="750">
    
- **Interpretação:**
    - **Satisfação Geral:** A percepção sobre o serviço é majoritariamente positiva. A barra de `Strongly Agree` (Concordo Totalmente) é a maior de todas, seguida por `Agree` (Concordo). As respostas negativas (`Disagree`, `Strongly Disagree`) são significativamente menores, indicando que as experiências ruins são uma minoria.
    - **Categorias NPS:** Este gráfico reforça a primeira conclusão. O número de **Promotores** (clientes leais e entusiastas) é esmagadoramente maior do que o de **Detratores** e **Passivos** somados. Isso sugere um serviço público de altíssima qualidade percebida.

## **2: Cálculo do Score NPS e Análise Temporal**

- **Objetivo:** Calcular o score NPS final para ter um único número que represente a "lealdade" do cliente. Em seguida, investigar como a composição das categorias NPS (Promotores, Passivos, Detratores) evoluiu ao longo dos anos disponíveis no dataset.
- **Variáveis:** `NPS Category` , `Year`
- **Gráfico & Tabela:**

  <img src="/images/02_Proporo_Categorias_NPS_por_Ano.png" alt="Proporção de categorias nps por ano" width="600">

| Métrica | Valor |
| --- | --- |
| **Score NPS Final** | **+52.92** |
| Total de Respostas | 22791 |
| Qtd. de Promotores | 15827 (69.44%) |
| Qtd. de Passivos | 3197 (14.03%) |
| Qtd. de Detratores | 3767 (16.53%) |
- **Interpretação:**
    - **Serviços consistentes:** Podemos observar a **consistência** do serviço ao longo dos anos de 2020 a 2023, a proporção de Promotores, Passivos e Detratores quase não se alterou. Isso sugere que a alta qualidade do atendimento não é um evento isolado, mas sim o resultado de um processo estável e bem gerenciado. Não há sinais de queda na qualidade, o que é um excelente indicador.

## **3:  Identificando os Fatores de Satisfação e Insatisfação**

### 3.1) Relação entre o Conhecimento do Agente e o NPS

- **Hipótese:** Clientes que percebem o agente como bem informado (`Agree` ou `Strongly Agree` em `Agent - Job Knowledge`) têm uma probabilidade muito maior de serem Promotores. Inversamente, a maioria dos Detratores deve ter avaliado mal o conhecimento do agente.
- **Variáveis:** `NPS Category` e `Agent - Job Knowledge`.
- **Gráfico & Tabela:**
    
<img src="/images/03.1_Impacto_Conhecimento_Agente_Categoria_NPS.png" alt="Impacto do conhecimento do agente na categoria nps" width="600">    

| **Agent - Job Knowledge** | **Strongly Disagree** | **Disagree** | **Neutral** | **Agree** | **Strongly Agree** |
| --- | --- | --- | --- | --- | --- |
| **NPS Category** |  |  |  |  |  |
| **Detrator** | 16.78% | 13.96% | 28.22% | 28.38% | 12.66% |
| **Passivo** | 2.16% | 2.69% | 12.08% | 53.60% | 29.47% |
| **Promotor** | 2.55% | 0.45% | 2.63% | 20.20% | 74.16% |
- **Interpretação:**
    - Os resultados confirmam nossa hipótese de forma muito clara. A percepção sobre o conhecimento do agente (`Agent - Job Knowledge`) tem uma correlação direta e fortíssima com a categoria NPS do cliente.
- **Conclusão-chave:**
    - Para mover clientes da categoria "Passivo" para "Promotor", um nível de conhecimento meramente "aceitável" não é suficiente. É preciso demonstrar **excelência e profundo domínio**. Para evitar "Detratores", é absolutamente crítico garantir que os agentes possuam o conhecimento necessário para resolver as questões.

### 3.2) Impacto do Atendimento (Customer Service) no NPS

- **Objetivo:** Medir como a percepção sobre a qualidade do atendimento do agente (cortesia, empatia, etc.) influencia a classificação NPS.
- **Variáveis:** `NPS Category` e `Agent - Customer Service`.
- **Gráfico & Tabela:**
    
<img src="/images/03.2_Impacto_Atendimento_Agente_Categoria_NPS.png" alt="Impacto do atendimento do agente na categoria nps" width="600">    

| **Agent - Customer Service** | **Strongly Disagree** | **Disagree** | **Neutral** | **Agree** | **Strongly Agree** |
| --- | --- | --- | --- | --- | --- |
| **NPS Category** |  |  |  |  |  |
| **Detrator** | 11.34% | 6.13% | 21.98% | 38.07% | 22.48% |
| **Passivo** | 2.03% | 1.22% | 4.91% | 45.73% | 46.11% |
| **Promotor** | 2.73% | 0.23% | 1.19% | 13.91% | 81.92% |
- **Interpretação:** Os resultados mostram que, assim como o conhecimento, a qualidade do atendimento (`Agent - Customer Service`) também tem uma forte correlação com a satisfação do cliente, mas com uma nuance importante.
    - **Para os Detratores:** Um número significativo, **17.4%**, avaliou o atendimento negativamente (`Strongly Disagree` + `Disagree`). Embora seja uma parcela relevante, é notavelmente **menor** que os 30.7% que avaliaram o *conhecimento do agente* negativamente (análise anterior). Isso já nos dá uma pista importante.
    - **Para os Promotores:** O resultado é espetacular. Quase **96%** dos promotores avaliaram o atendimento positivamente (`Agree` ou `Strongly Agree`), com a grande maioria (81.9%) na categoria máxima. Para ser um promotor, o cliente precisa sentir que foi muito bem tratado.
    - **Para os Passivos:** Este grupo teve uma experiência positiva, mas não excepcional. Quase 92% avaliaram o atendimento positivamente, mas de forma dividida entre `Agree` (45.7%) e `Strongly Agree` (46.1%).
- **Insight:**  Ao comparar esta análise com a anterior (3.1), podemos apontar que a **falta de conhecimento técnico do agente é um fator mais poderoso para criar um Detrator do que um mau atendimento.**
    - Enquanto ambos os fatores são cruciais para criar Promotores, um cliente parece mais propenso a se tornar um detrator se seu problema não for resolvido por falta de conhecimento do que por uma interação rude ou pouco empática. Em outras palavras, **a eficácia (resolver o problema) pesa mais que a cortesia (ser bem tratado) na hora de gerar uma insatisfação profunda.** Isso é extremamente valioso para priorizar treinamentos: **garantir a competência técnica é a defesa número um contra a insatisfação.**

### 3.3) Impacto do Tempo de Espera (Wait Time) no NPS

- **Objetivo:** Determinar se a percepção sobre o tempo de espera é um fator significativo na criação de Detratores ou Promotores.
- **Variáveis Analisadas:** `NPS Category` e `Wait Time`.
- **Gráfico & Tabela:**
    
<img src="/images/03.3_Impacto_Tempo_Espera_Catrgoria_NPS.png" alt="Impacto do tempo de espera na categoria nps" width="600">    

| **Wait Time** | **Strongly Disagree** | **Disagree** | **Neutral** | **Agree** | **Strongly Agree** |
| --- | --- | --- | --- | --- | --- |
| **NPS Category** |  |  |  |  |  |
| **Detrator** | 15.93% | 11.52% | 24.87% | 37.24% | 10.43% |
| **Passivo** | 2.60% | 4.04% | 13.67% | 61.46% | 18.24% |
| **Promotor** | 2.91% | 1.26% | 4.73% | 32.87% | 58.24% |
- **Interpretação:** A análise do tempo de espera (`Wait Time`) revela que este é um fator de grande impacto na experiência do cliente, quase tão importante quanto o conhecimento do agente.
    - **Para os Detratores:** A insatisfação com a espera é um motivo claro para a avaliação negativa. Somando as respostas negativas, vemos que **27.5%** dos detratores (`15.9% Strongly Disagree` + `11.5% Disagree`) ficaram insatisfeitos com o tempo que levaram para serem atendidos. A experiência deles já começou de forma negativa, antes mesmo da interação com o agente.
    - **Para os Promotores:** Mais de **91%** dos promotores avaliaram o tempo de espera positivamente (`Agree` ou `Strongly Agree`). Fica claro que uma percepção de agilidade no atendimento é um componente essencial para gerar uma experiência excepcional.
    - **Para os Passivos:** A maioria (cerca de 80%) avaliou positivamente a espera, porém, a maior parte das respostas se concentra em `Agree` (61.5%), e não em `Strongly Agree` (18.2%). Assim como nos outros casos, a falta de uma percepção "excelente" os impede de se tornarem promotores.

- O que temos até agora?
    
<img src="/images/03.4_Prioridades_Melhorias.png" alt="Prioridades de melhoria e fatores que mais geram clientes detratores" width="600">    
    
- **Insight**: **o tempo de espera é o segundo maior motivo de insatisfação**, quase empatado com a falta de conhecimento técnico. Isso significa que a jornada do cliente pode ser comprometida por um fator de processo (a fila) antes que o agente tenha qualquer chance de intervir. Um atendimento excelente pode não ser suficiente para reverter uma primeira impressão negativa causada por uma longa espera.

## 4: Desempenho por Campanha de Pesquisa

- **Hipótese:** A performance excelente que observamos é uniforme ou existem variações entre as diferentes campanhas de pesquisa (`Campaign`)? Esta análise buscará identificar se alguma campanha específica se destacou, positiva ou negativamente, em relação às outras. Isso pode indicar que eventos, mudanças de processo ou sazonalidades impactam a percepção do serviço.
- **Variáveis:** `Campaign`, `NPS Category`
- **Gráfico & Tabela:**
    
<img src="/images/04_Comparatico_Score_NPS_Campanha.png" alt="Comparativo de score NPS por campanha" width="600">        

| **NPS Category** | **NPS Score** |
| --- | --- |
| **Campaign** |  |
| **Campaign 1** | 55.05 |
| **Campaign 2** | 54.59 |
| **Campaign 4** | 52.94 |
| **Campaign 5** | 52.15 |
| **Campaign 3** | 51.96 |
| **Pilot** | 46.15 |
- **Interpretação:** A análise nos mostra um desempenho consistente entre todas as campanhas, com exceção da campanha piloto, que apesar de ainda estar dentro de um score considerado bom (46,1), ainda é visivelmente inferior. Há de se questionar o foco da campanha e como as outras foram mais bem sucedidas em comparação a ela.

# 5: Análise de Satisfação por Idioma

- **Objetivo:** Investigar se existem diferenças na satisfação do cliente entre os diferentes idiomas nos quais a pesquisa foi respondida. Esta análise é crucial para garantir que a alta qualidade do serviço seja entregue de forma equitativa a todos os cidadãos, independentemente da língua que falam.

### 5.1) Comparativo do Score NPS por Idioma

- **Objetivo Específico:** Calcular e comparar o Score NPS para cada grupo linguístico, identificando se a lealdade do cliente varia entre eles.
- **Variáveis Analisadas:** `Survey Language`, `NPS Category`
- **Gráfico & Tabela:**
    
<img src="/images/05.1_Comparativo_Score_NPS_Idioma.pngg" alt="Comparativo de score NPS por idioma" width="600">            

| **NPS Category** | **NPS Score** |
| --- | --- |
| **Survey Language** |  |
| **Español** | 72.82 |
| **English** | 50.04 |
| **Chinese (Simplified)** | 45.87 |
| **Chinese (tradicional)** | 42.21 |
| **Russian** | 40.79 |
| **Korean** | 33.33 |
- **Insights:**
    - **A Qualidade Percebida Varia por Idioma:** O insight mais importante é que a experiência do cliente não é a mesma para todos. Existem diferenças claras e mensuráveis no nível de lealdade entre os grupos linguísticos.
    - **Sucesso no Atendimento em Espanhol:** Há algo no serviço prestado aos cidadãos de língua espanhola que gera um nível de encantamento muito acima da média. A pergunta aqui é: *O que está sendo feito de tão certo neste atendimento?* Existe uma equipe dedicada? Processos diferentes? Entender a causa desse sucesso pode gerar aprendizados para aplicar aos outros grupos.
    - **Oportunidade de Melhoria nos Demais Idiomas:** A queda nos scores para os outros idiomas (chinês, russo, coreano) indica uma oportunidade. Embora o serviço ainda seja bem avaliado, há uma inconsistência na entrega da "excelência". A experiência não está sendo universalmente excepcional.

### 5.2) Análise das Métricas de CSAT por Idioma

- **Objetivo Específico:** Avaliar se a satisfação com a interação em si (CSAT) varia entre os idiomas. Para isso, vamos nos concentrar na principal métrica de CSAT: `Overall Satisfaction`.
- **Variáveis Analisadas:** `Survey Language`, `Overall Satisfaction`
- **Gráfico & Tabela:**
    
<img src="/images/05.2_Mdia_CSAT_Idioma.png" alt="Média de satisfação geral CSAT por idioma" width="600">            
    
- **Interpretação:**
    - **Consistência Operacional:** Todas as médias de satisfação (CSAT) estão muito próximas, variando apenas **0.30 pontos** entre o máximo (Espanhol, com 4.34) e o mínimo (Russo, com 4.04).
    - **Satisfação Universal:** Uma nota média acima de 4.0 para todos os grupos significa que, em média, todos os clientes **concordam** (`Agree`) que a interação foi satisfatória. Do ponto de vista puramente operacional, o serviço está sendo entregue com uma qualidade consistentemente boa para todos.
- **Insights (A Diferença Crucial entre CSAT e NPS):** A grande questão é: **Se a satisfação na chamada (CSAT) é tão parecida, por que a lealdade (NPS) é tão diferente?** A resposta está na diferença entre um serviço **"bom"** e um serviço **"excepcional"**.
    - **CSAT Mede o "Bom":** O CSAT, com sua média em torno de 4.1-4.3, nos diz que o serviço é consistentemente *bom*. Ele cumpre o que promete, resolve o problema e o cliente fica satisfeito com aquela transação específica. Isso é o que um serviço público competente deve fazer.
    - **NPS Mede o "Excepcional":** O NPS, por outro lado, mede o entusiasmo. Para um cliente dar uma nota 9 ou 10 (e se tornar um Promotor), o serviço não pode ser apenas "bom", ele precisa ser **"excepcional"**. Ele precisa gerar uma reação emocional positiva, um "Uau!".
        - A pequena diferença na média do CSAT (de 4.1 para 4.34 no grupo espanhol) indica que este grupo teve uma experiência consistentemente mais próxima do "Strongly Agree" (nota 5). Essa pequena melhora na transação foi o suficiente para criar um sentimento de entusiasmo que **disparou o número de Promotores** e, consequentemente, o score NPS.
    - **O Fator Cultural:** É também muito provável que estejamos vendo um fator cultural. Algumas culturas são naturalmente mais propensas a dar notas máximas quando satisfeitas, enquanto outras são mais reservadas. A propensão a "recomendar um serviço público" pode variar culturalmente, explicando por que grupos com satisfação transacional similar (CSAT) podem ter intenções de recomendação (NPS) tão diferentes.
    
- **Insight Final:** A diferença entre um serviço bom e um ótimo é pequena na escala de satisfação do CSAT, mas tem um **impacto desproporcional e gigantesco na lealdade do cliente (NPS)**. O serviço NYC311 é consistentemente *bom* para todos, mas é consistentemente *excepcional* para os clientes de língua espanhola, e é essa pequena margem de excelência que cria um resultado de NPS tão superior.

# **Conclusão Geral**

### **1. Sumário Executivo**

O serviço de atendimento NYC311 demonstra um nível de satisfação do cliente **excelente e notavelmente estável** ao longo dos últimos anos. Com um Net Promoter Score (NPS) geral de **+52.92**, a operação se posiciona em um patamar de alta qualidade. A análise aprofundada dos dados, no entanto, aponta que a principal causa de insatisfação não é o tratamento interpessoal, mas sim a **falta de conhecimento técnico do agente** e o **tempo de espera**. Adicionalmente, foi identificada uma disparidade significativa na percepção de qualidade entre diferentes grupos linguísticos, destacando-se um sucesso excepcional no atendimento em espanhol que serve como um modelo a ser estudado e replicado.

### **2. Principais Destaques Quantitativos**

- **NPS Geral:** **+52.92** (Classificação: "Ótimo").
- **Consistência Temporal:** A alta performance é estável, sem quedas significativas de performance entre 2020 e 2023.
- **Principal Causa de Insatisfação:** **30.7%** dos clientes "Detratores" avaliaram negativamente o conhecimento do agente.
- **Atendimento em Espanhol:** Atingiu um NPS de **+72.8** (Classificação: "Excelente"), muito acima de todos os outros grupos.

### **3. Análise dos Fatores de Sucesso e Falha**

A análise de drivers nos permitiu entender o que separa uma experiência boa de uma ruim:

- **O Que Gera Sucesso (Promotores):** Para um cliente se tornar um promotor (nota 9 ou 10), não basta uma única coisa dar certo. É necessária uma combinação de **excelente conhecimento do agente**, **ótimo atendimento interpessoal** e uma **percepção positiva sobre o tempo de espera**. A excelência em todas as frentes é o que gera "encantamento".
- **O Que Gera Falha (Detratores):** A insatisfação profunda é gerada principalmente por falhas de eficácia e processo. O ranking de problemas é claro:
    1. **Falta de Conhecimento do Agente** (30.7% dos detratores).
    2. **Tempo de Espera Elevado** (27.5% dos detratores).
    3. **Qualidade do Atendimento Interpessoal** (17.4% dos detratores).

### **4. Variações de Performance**

- **Por Campanha:** A performance é muito consistente entre as campanhas regulares. A única exceção foi uma campanha **"Piloto"** que teve um score inferior, indicando que as práticas testadas nela foram menos eficazes que o padrão, mas por ju9st7amente ser uma campanha piloto, os processos de melhorias para as campanhas seguintes demonstraram bons resultados.
- **Por Idioma:** Esta foi a descoberta mais significativa. Enquanto a satisfação com a transação em si (CSAT) foi muito parecida entre todos os idiomas, a lealdade (NPS) variou drasticamente. Isso sugere que a diferença entre um serviço "bom" e um "excepcional" é pequena, mas tem um impacto gigantesco no sentimento do cliente, com possíveis influências de fatores culturais na forma de avaliar.

### **5. Recomendações Estratégicas**

Com base nos dados, as seguintes ações são recomendadas para otimizar o serviço:

1. **Prioridade 1: Fortalecer o Conhecimento Técnico dos Agentes.** Sendo a principal causa de detratores, o investimento em treinamento contínuo, bases de conhecimento acessíveis e sistemas de suporte para os agentes deve gerar o maior retorno na redução de insatisfação.
2. **Prioridade 2: Otimizar a Gestão do Tempo de Espera.** Por ser o segundo maior "vilão", otimizar a fila de atendimento através da análise de dados de volume de chamadas ou da implementação de novas tecnologias (como sistemas de "callback") pode melhorar drasticamente a percepção do serviço antes mesmo da interação humana.
3. **Prioridade 3: Investigar e Replicar o "Modelo de Sucesso Espanhol".** É fundamental realizar uma análise qualitativa para entender o que torna o atendimento em espanhol tão excepcional. Os processos, treinamentos ou ferramentas utilizadas por esta equipe podem conter as chaves para elevar a performance de todos os outros grupos linguísticos e universalizar a excelência.
