# Análise de Dispersão de Preços em Compras Governamentais: Cloridrato de Tramadol

## Índice
- [Introdução](#introdução)
- [Problema/Questão](#problema-questão)
- [Fonte de Dados](#fonte-de-dados)
- [Metodologia](#metodologia)
- [Análises e Resultados](#análises-e-resultados)
- [Conclusões Principais](#conclusões-principais)
- [Como Replicar](#como-replicar)
- [Dependências](#dependências)
- [Autor](#autor)

## Introdução
Este projeto visa investigar a dispersão de preços em compras governamentais, utilizando como estudo de caso o item **Cloridrato de tramadol, 2 mg/mL, solução injetável, uso veterinário**. A análise busca compreender as variações de preço, identificar padrões e detectar possíveis anomalias, oferecendo insights sobre a eficiência e transparência dos processos de aquisição pública.

## Problema/Questão
Um mesmo material apresenta diferenças relevantes de preço nas compras governamentais?

## Fonte de Dados
Os dados foram coletados através da API pública do módulo de Pesquisa de Preços do governo brasileiro: `https://dadosabertos.compras.gov.br/modulo-pesquisa-preco/1_consultarMaterial`.

- **Item analisado (Código PDM):** 30342 (Cloridrato de Tramadol)
- **Filtro adicional:** Solução injetável, 2 mg/mL, uso veterinário, unidade de fornecimento MILILITRO.

## Metodologia
1.  **Coleta de Dados:** Requisições HTTP paginadas à API para obter todos os registros disponíveis para o código PDM especificado.
2.  **Validação e Limpeza Inicial:**
    *   Remoção de registros duplicados (40 registros brutos reduzidos para 10 únicos).
    *   Verificação de valores ausentes em colunas críticas.
    *   Conversão de tipos de dados (ex: `dataCompra` para datetime).
3.  **Filtragem para Comparabilidade:** Redução da base de 10 registros únicos para **5 registros comparáveis** com base na `Unidade de Fornecimento = 'MILILITRO'` e homogeneidade de descrição do item (Cloridrato de tramadol, 2 mg/mL, solução injetável, uso veterinário).
4.  **Análise Exploratória de Dados (EDA):**
    *   Estatísticas descritivas (Média, Mediana, Desvio Padrão, IQR, Coeficiente de Variação).
    *   Visualizações:
        *   Histograma e Boxplot para distribuição de preços.
        *   Mapa de calor interativo da média de preços por estado.
        *   Série temporal de preços unitários.
        *   Gráfico de dispersão entre quantidade e preço unitário.
5.  **Detecção de Outliers:** Utilização do método do Intervalo Interquartil (IQR) para identificar valores atípicos nos preços.

## Análises e Resultados

As análises foram realizadas sobre uma amostra de **5 registros comparáveis**. Embora pequena, a amostra permitiu as seguintes observações:

*   **Alta Dispersão de Preços:** O preço unitário variou drasticamente de R$ 1,29 (no RN) a R$ 52,94 (no PA), resultando em um Coeficiente de Variação (CV) de 62,79%, indicando alta variabilidade.
*   **Variação Geográfica:** Notou-se forte disparidade regional, com o Pará pagando significativamente mais que o Rio Grande do Norte pelo mesmo item.
*   **Correlação Quantidade vs. Preço:** Uma correlação negativa de Spearman (-0,50) foi observada, sugerindo que maiores quantidades compradas tendem a ter preços unitários mais baixos. **Importante:** Devido ao pequeno número de observações (n=5), essa correlação é exploratória e não estatisticamente significativa (p-valores elevados).
*   **Outlier Identificado:** A compra no Rio Grande do Norte a R$ 1,29 foi classificada como um outlier de preço baixo, exigindo investigação aprofundada para entender as condições da compra.

## Conclusões Principais
Esta análise demonstra a existência de grandes assimetrias de informação e poder de barganha nas contratações públicas para o item estudado. A dispersão observada reforça a necessidade de ferramentas centralizadas de planejamento e acompanhamento para balizar negociações e proteger o erário.

## Como Replicar
1.  **Clone o Repositório:**
    ```bash
    git clone <URL_DO_SEU_REPOSITORIO>
    cd <nome_do_repositorio>
    ```
2.  **Abra o Notebook:** Abra o arquivo `.ipynb` em um ambiente como Google Colab ou Jupyter Notebook.
3.  **Instale as Dependências:** Execute a primeira célula do notebook para instalar as bibliotecas necessárias.
4.  **Execute as Células:** Prossiga executando as células sequencialmente para reproduzir a coleta de dados, limpeza, análise e visualizações.

## Dependências
As seguintes bibliotecas Python são necessárias e instaladas no início do notebook:
- `requests`
- `pandas`
- `matplotlib`
- `seaborn`
- `copy`
- `plotly.express`
- `urllib.request`
- `numpy`
- `json`
- `scipy`

## Autor
[Seu Nome/Handle do GitHub/Contato] - [Opcional]
