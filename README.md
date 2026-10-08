# Análise de Dispersão de Preços em Compras Governamentais: Cloridrato de Tramadol

## Índice
- [Introdução](#introdução)
- [Problema de Pesquisa](#problema-de-pesquisa)
- [Fonte de Dados](#fonte-de-dados)
- [Metodologia](#metodologia)
- [Análises e Resultados](#análises-e-resultados)
- [Limitações](#limitações)
- [Conclusões Principais](#conclusões-principais)
- [Como Replicar](#como-replicar)
- [Dependências](#dependências)

## Introdução

Este projeto investiga a dispersão dos preços praticados em compras governamentais, utilizando como estudo de caso o **Cloridrato de tramadol, concentração de 2 mg/mL, solução injetável, de uso veterinário**.

A análise utiliza dados públicos de compras governamentais e técnicas de Análise Exploratória de Dados (EDA) para avaliar a variação dos preços unitários, a distribuição geográfica das compras, a relação entre quantidade adquirida e preço e a ocorrência de valores extremos.

O objetivo não é explicar causalmente as diferenças de preço, mas identificar padrões e registros que possam exigir investigação adicional.

## Problema de Pesquisa

Um mesmo material adquirido por diferentes órgãos públicos apresenta diferenças relevantes de preço unitário?

Além disso, busca-se explorar se essas diferenças estão associadas à quantidade adquirida, ao estado da compra, ao período ou à presença de valores extremos.

## Fonte de Dados

Os dados foram obtidos por meio da API pública do módulo de Pesquisa de Preços do Portal de Dados Abertos de Compras Governamentais:

`https://dadosabertos.compras.gov.br/modulo-pesquisa-preco/1_consultarMaterial`

### Parâmetros utilizados

- **Tipo de consulta:** `codigoPdm`
- **Código PDM:** `30342`
- **Produto:** Cloridrato de tramadol
- **Página inicial:** `1`
- **Tamanho da página:** `500`

Não foi aplicado filtro temporal diretamente na requisição. Portanto, o período analisado corresponde aos registros disponibilizados pelo endpoint no momento da coleta.

### Recorte do estudo

Para garantir a comparabilidade dos registros, foram mantidas apenas compras que atendem simultaneamente aos seguintes critérios:

- **Código CATMAT:** `629326`
- **Concentração:** `2 mg/mL`
- **Forma farmacêutica:** solução injetável
- **Uso:** veterinário
- **Unidade de fornecimento:** mililitro

Registros com apresentações diferentes, como comprimidos de 80 mg, ou com inconsistências entre a descrição do produto e a unidade de fornecimento, não foram incluídos na análise estatística principal.

## Metodologia

### 1. Coleta dos Dados

A coleta é realizada por meio de requisições HTTP utilizando a biblioteca `requests`.

A API utiliza os parâmetros:

- `pagina`
- `tamanhoPagina`

A paginação é percorrida automaticamente quando necessário.

> Uma versão anterior do projeto utilizava o parâmetro `page`, o que fazia com que a primeira página fosse consultada repetidamente. A implementação atual utiliza corretamente o parâmetro `pagina`.

Na execução analisada, a API retornou **38 registros** relacionados ao PDM 30342.

### 2. Validação e Limpeza

Após a coleta, são realizadas as seguintes etapas:

- verificação de registros duplicados;
- remoção de duplicidades utilizando `idItemCompra`;
- verificação de valores ausentes em colunas críticas;
- conversão de `dataCompra` para o tipo `datetime`;
- seleção das variáveis relevantes para a análise;
- cálculo do valor total de cada registro:

`Valor Total = Preço Unitário × Quantidade`

### 3. Filtragem para Comparabilidade

O PDM 30342 contém diferentes apresentações de cloridrato de tramadol.

Por esse motivo, não é suficiente selecionar os dados apenas pelo nome do medicamento.

A base final exige simultaneamente:

`CATMAT 629326 + 2 mg/mL + solução injetável + uso veterinário + MILILITRO`

Na execução utilizada nesta análise, **15 registros foram considerados comparáveis**.

O período desses registros vai de **12/08/2025 a 10/09/2026**.

Registros cuja descrição indicava solução injetável, mas cuja unidade de fornecimento estava cadastrada como comprimido, foram excluídos da análise principal por apresentarem inconsistência de cadastro.

### 4. Análise Exploratória de Dados

Foram calculadas medidas estatísticas descritivas para o preço unitário em R$/mL:

- média;
- mediana;
- mínimo e máximo;
- desvio padrão amostral;
- primeiro e terceiro quartis;
- intervalo interquartil (IQR);
- coeficiente de variação (CV).

Também foram utilizadas as seguintes visualizações:

- preço médio por estado;
- quantidade total comprada por estado;
- valor total comprado por estado;
- histograma da distribuição dos preços;
- boxplot;
- mapa interativo dos preços médios observados por estado;
- gráfico de dispersão entre quantidade adquirida e preço unitário;
- evolução temporal dos preços.

Os gráficos de quantidade e valor total por estado utilizam agrupamento por UF antes da visualização, evitando a sobreposição de múltiplas barras do mesmo estado.

### 5. Análise de Quantidade e Preço

A relação entre quantidade adquirida e preço unitário foi analisada por meio dos coeficientes de:

- **Pearson**
- **Spearman**

A correlação de Spearman foi priorizada na interpretação exploratória por ser menos sensível a valores extremos e não exigir uma relação estritamente linear.

### 6. Detecção de Outliers

Valores extremos foram identificados utilizando o método do Intervalo Interquartil:

`IQR = Q3 - Q1`

Os limites foram calculados por:

`Limite inferior = Q1 - 1,5 × IQR`

`Limite superior = Q3 + 1,5 × IQR`

A classificação de um registro como outlier é exclusivamente estatística e não significa automaticamente que a compra esteja incorreta.

## Análises e Resultados

A base comparável utilizada nesta execução contém **15 registros**.

### Dispersão dos Preços

Os preços unitários apresentaram grande variabilidade:

- **Preço mínimo:** R$ 1,29/mL
- **Preço máximo:** R$ 168,57/mL
- **Média:** R$ 43,37/mL
- **Mediana:** R$ 34,30/mL
- **Desvio padrão amostral:** R$ 45,66/mL
- **Coeficiente de variação:** 105,27%
- **Q1:** R$ 4,40/mL
- **Q3:** R$ 52,45/mL
- **IQR:** R$ 48,05/mL

O coeficiente de variação superior a 100% demonstra elevada dispersão dos preços dentro da amostra analisada.

### Variação entre Estados

Foram encontrados registros comparáveis em diferentes unidades federativas.

Os gráficos mostram diferenças relevantes entre os preços observados, porém essas diferenças **não devem ser interpretadas automaticamente como efeito regional**.

Cada contratação pode apresentar características próprias relacionadas a órgão comprador, fornecedor, marca, quantidade, modalidade de compra, data, condições de entrega e outras variáveis não controladas neste estudo.

### Quantidade Adquirida e Preço

A correlação de Pearson entre quantidade e preço foi aproximadamente:

- **Pearson:** -0,225
- **p-valor:** 0,421

A correlação de Spearman foi aproximadamente:

- **Spearman:** -0,477
- **p-valor:** 0,072

A correlação negativa de Spearman indica uma associação negativa observada na amostra: registros com maiores quantidades tendem a aparecer associados a preços menores.

Entretanto, o resultado **não é estatisticamente significativo ao nível de 5%**, portanto não permite afirmar a existência de ganho de escala.

A relação deve ser considerada exclusivamente exploratória.

### Valores Extremos

Pelo critério do IQR, foi identificado um valor extremo superior.

O registro do **Pará**, com:

- **Quantidade:** 4 mL
- **Preço unitário:** R$ 168,57/mL

ficou acima do limite superior calculado pelo método do IQR.

Esse resultado não significa que a compra esteja incorreta. O registro deve ser investigado no processo de origem para verificar características como apresentação, embalagem, fornecedor, marca e condições da contratação.

O antigo registro do Rio Grande do Norte de **R$ 1,29/mL**, que aparecia como outlier na análise com apenas cinco observações, deixa de ser classificado como outlier quando a base completa de registros comparáveis é utilizada.

## Limitações

A análise apresenta algumas limitações importantes:

- os dados são utilizados conforme disponibilizados pela API;
- existem inconsistências de cadastro em alguns registros;
- marca, fornecedor, modalidade de contratação, condições de entrega e prazo de pagamento não são controlados;
- a quantidade de registros ainda é relativamente pequena;
- correlação não implica causalidade;
- diferenças observadas entre estados não representam necessariamente diferenças regionais de preços;
- o endpoint utilizado disponibiliza um período limitado de registros no momento da consulta;
- os resultados podem mudar caso novos registros sejam disponibilizados pela API.

Por essas razões, os resultados devem ser considerados exploratórios.

## Conclusões Principais

A análise identificou elevada dispersão nos preços unitários pagos pelo poder público pelo cloridrato de tramadol injetável veterinário de 2 mg/mL.

Os preços observados variaram de **R$ 1,29/mL a R$ 168,57/mL**, demonstrando que compras classificadas como comparáveis podem apresentar diferenças expressivas de preço.

Foi observada uma associação negativa entre quantidade adquirida e preço unitário, porém a correlação de Spearman não apresentou significância estatística ao nível de 5%. Dessa forma, os resultados não permitem concluir que maiores volumes de compra provoquem redução de preço.

Também foi identificado um valor extremo superior pelo método do IQR, indicando a necessidade de investigação individual do registro de origem.

Os resultados mostram como dados abertos e técnicas de análise exploratória podem auxiliar na identificação de padrões, inconsistências e registros que merecem investigação adicional nas compras governamentais.

## Como Replicar

1. Clone o repositório:

```bash
git clone <URL_DO_REPOSITORIO>
cd <NOME_DO_REPOSITORIO>
