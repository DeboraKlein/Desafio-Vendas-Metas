

# Visualizações e KPIs – Power BI


Este documento apresenta os principais gráficos, indicadores e painéis desenvolvidos no projeto "Desafio Vendas x Metas", focando em performance, metas e insights estratégicos.

##  Medidas DAX — Cartão de Meta e Variação YoY
---

Visual criado com base em medidas aplicadas ao plano de fundo como cartão analítico dinâmico:

###  `Meta Total por Ano`
````

Meta Total por Ano = 
CALCULATE(
    SUM('fMetasConsolidadas'[Value]),
    'fMetasConsolidadas'[Categoria] <> "Total",
    TREATAS(
        VALUES(dCalendario[Ano]), 
        'fMetasConsolidadas'[Ano]
    )  
)
````
Utiliza TREATAS para alinhar anos entre dCalendario e fMetasConsolidadas, devido à ausência de relacionamento físico

Filtra categorias para excluir o agregado "Total"

Representa o somatório de metas consolidadas por ano
````

% Variação Meta YoY Robusta = 
DIVIDE(
    [Meta Total por Ano] - [Meta LY Robusta],
    [Meta LY Robusta],
    0
)
````
Para utilizá-la precisa-se da medida abaixo
````
Meta LY Robusta = 
VAR AnoAtual = SELECTEDVALUE(dCalendario[Ano])
RETURN
CALCULATE(
    SUM('fMetasConsolidadas'[Value]),
    'fMetasConsolidadas'[Categoria] <> "Total",
    'fMetasConsolidadas'[Ano] = AnoAtual - 1
)
````
Verifica se há filtro de ano ativo

Calcula variação percentual entre metas do ano atual e anterior (YoY)

Protege contra divisões inválidas ou faltas de contexto

Aplicado com formatação condicional:

🔴 vermelho para quedas

🟢 verde para crescimento positivo

##  Cartão de Faturamento Total + Crescimento YoY
---

Visual analítico composto por duas medidas principais: faturamento acumulado e variação ano sobre ano.


###  `Faturamento Total`

```
Faturamento Total = 
SUM(fVendas[Faturamento])
Soma simples da coluna Faturamento na tabela de fatos fVendas

Base para cálculo de desempenho financeiro global

Recomendado aplicar filtros de período no visual

% Cresc YoY = 
VAR vFaturamento_ano_anterior = 
    CALCULATE(
        [Faturamento Total],
        DATEADD(dCalendario[Date], -12, MONTH)
    )
VAR vCrescimento = 
    DIVIDE(
        [Faturamento Total] - vFaturamento_ano_anterior,
        vFaturamento_ano_anterior
    )
RETURN
IF(
    HASONEVALUE(dCalendario[Ano]),
    COALESCE(vCrescimento, 0),
    0
)

````
Compara faturamento atual com o de 12 meses atrás usando DATEADD

Usa DIVIDE para evitar erros de divisão por zero

Só retorna resultado quando há um único ano selecionado

Formatado com ícones ou cores: 🟢 alta no faturamento, 🔴 queda no desempenho


##  Cartão de Atingimento da Meta
---

Indicador fundamental que expressa o quanto do objetivo foi cumprido em relação ao faturamento.


###  `% Atingimento da Meta`

```
% Atingimento da Meta = 
[Faturamento Total] / [Meta Total por Ano]

````
Relaciona o faturamento atual com o valor alvo anual

Ideal para destacar progresso em dashboards executivos

Pode ser combinado com formatação condicional ou barras de progresso 📈


###  Cartão de Lucro Total

Indicador básico que mostra o total de lucro gerado pelas vendas.

---

###  `Lucro Total`

```
Lucro Total = 
SUM(fVendas[LucroVenda])

````
Realiza a soma de todos os valores da coluna LucroVenda na tabela fVendas

Pode ser filtrado por período, categoria, região etc.

Utilizado para avaliação direta da rentabilidade 


##  Gráfico de Barras Horizontais — % Faturamento por Subcategoria
---

Indicador visual para avaliar a representatividade de cada subcategoria no total de faturamento, com barras horizontais para melhor leitura comparativa.


###  `% Faturamento SubCategoria`

```
% Faturamento SubCategoria = 
DIVIDE(
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        )
    ),
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        ),
        ALL(dSubcategoria[Subcategoria])
    ),
    0
)

````
SUMX é usado para calcular o faturamento líquido (preço * quantidade - desconto)

CALCULATE aplica o contexto de filtro para cada subcategoria

ALL remove filtro de subcategoria no denominador, garantindo cálculo percentual

A medida deve ser visualizada com barras horizontais por subcategoria, ordenadas por valor decrescente para melhor análise 📉


##  Cartão: Categoria Campeã — Destaque no Faturamento
---

Esse visual revela qual categoria lidera o faturamento e quantifica sua representatividade no total, trazendo à tona o "MVP" comercial do período.


###  `Categoria Campeã`

```
Categoria Campeã = 
CALCULATE(
    MAXX(
        TOPN(
            1,
            SUMMARIZE(
                dSubcategoria,
                dSubcategoria[Categoria],
                "Faturamento",
                CALCULATE(
                    SUMX(
                        fVendas,
                        (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
                    )
                )
            ),
            [Faturamento],
            DESC
        ),
        dSubcategoria[Categoria]
    )
)

````
Encontra a categoria com maior faturamento líquido

TOPN(1) busca o primeiro colocado, ordenado de forma decrescente

SUMMARIZE prepara a agregação por categoria

MAXX extrai o nome da vencedora
````
% Categoria Campeã = 
VAR CategoriaTop = [Categoria Campeã]

VAR FaturamentoTop =
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        ),
        dSubcategoria[Categoria] = CategoriaTop
    )

VAR FaturamentoTotal =
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        )
    )

RETURN
DIVIDE(FaturamentoTop, FaturamentoTotal)

````
Calcula o faturamento da categoria vencedora no contexto total

Ideal para cartão com visual narrativo, estilo Enlighten: “A categoria com maior faturamento é [#Categoria Campeã] e representa [#% Categoria Campeã]% do faturamento total.”


##  Gráfico de Rosca — % Faturamento por Categoria
---

Visual analítico que mostra a representatividade de cada categoria no total de faturamento. Ideal para destacar “quem leva qual fatia do bolo”!

###  `% Faturamento Categoria`

```
% Faturamento Categoria = 
DIVIDE(
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        )
    ),
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        ),
        ALL(dSubcategoria[Categoria])
    ),
    0
)

````
O SUMX calcula o faturamento líquido: Preço × Quantidade − Desconto

CALCULATE aplica o filtro da categoria atual e remove no denominador para obter a proporção

DIVIDE protege contra divisões por zero

Ideal para visualização em gráfico de rosca (ou pizza), destacando cada categoria com cor própria


##  Matriz de Receita — Continente x Subcategoria
---

Visual interativo que cruza a dimensão geográfica (continente) com a dimensão comercial (subcategoria), revelando padrões de faturamento por região e categoria de produto.

###  `Faturamento Total`

```
Faturamento Total = 
SUM(fVendas[Faturamento])

````
Cálculo direto da soma de faturamento

Usado como valor na célula da matriz

Pode ter filtro de tempo aplicado (mês, ano, trimestre)


##  Tooltip Vinculado à Matriz — Visão Detalhada por Subcategoria
---

O tooltip fornece um aprofundamento analítico diretamente sobre a matriz principal `Continente x Subcategoria`, revelando volume, valor médio e variação anual de faturamento.

###  Contexto do Visual

**Matriz Principal:**
- Linhas: `Continente`  
- Colunas: `Subcategoria`  
- Valor: `Faturamento Total`

**Tooltip Vinculado:**
- Visualizado ao passar o mouse sobre uma célula da matriz
- Apresenta os seguintes indicadores:

---

###  Cartões no Tooltip

####  `Qtd Vendida`

```
Qtd Vendida = 
SUM(fVendas[Quantidade])
Reflete o volume total de unidades vendidas para o contexto selecionado

Ticket Médio = 
DIVIDE(
    SUMX(
        fVendas,
        (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
    ),
    COUNTROWS(fVendas),
    0
)

````
Mostra o valor médio por transação, útil para insights sobre precificação e margem

###  Matriz YoY no Tooltip

% Cresc YoY por Subcategoria
````

% Cresc YoY = 
VAR vFaturamento_ano_anterior = 
    CALCULATE(
        [Faturamento Total],
        DATEADD(dCalendario[Date], -12, MONTH)
    )
VAR vCrescimento = 
    DIVIDE(
        [Faturamento Total] - vFaturamento_ano_anterior,
        vFaturamento_ano_anterior
    )
RETURN
IF(
    HASONEVALUE(dCalendario[Ano]),
    COALESCE(vCrescimento, 0),
    0
)
````
Avaliação comparativa do faturamento por subcategoria em relação ao ano anterior

Permite identificar crescimento ou retração diretamente no contexto do tooltip


##  Scroller — Subcategorias e Faturamento Relativo
--- 
Visual em formato de **scroller horizontal** ou **gráfico de lista vertical** que permite navegação fluida entre subcategorias, exibindo tanto o valor total quanto a representatividade percentual de cada uma.

###  `Faturamento Total`

```
Faturamento Total = 
SUM(fVendas[Faturamento])
````
Soma direta da coluna Faturamento da tabela de fatos

Exibe o valor bruto gerado por subcategoria

###  % Faturamento SubCategoria
````

% Faturamento SubCategoria = 
DIVIDE(
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        )
    ),
    CALCULATE(
        SUMX(
            fVendas,
            (fVendas[Preço Unitário] * fVendas[Quantidade]) - fVendas[Valor Desconto]
        ),
        ALL(dSubcategoria[Subcategoria])
    ),
    0
)

````
Representa a participação percentual da subcategoria no faturamento líquido total

SUMX calcula o faturamento por linha: Preço × Quantidade − Desconto

ALL remove o filtro da subcategoria no denominador, garantindo cálculo proporcional


##  Decomposition Tree — Faturamento por Hierarquia
--- 

Visual interativo que permite explorar o faturamento de forma hierárquica, partindo de categoria até subcategoria.

###  Medida Base: `Faturamento Total`

```
Faturamento Total = 
SUM(fVendas[Faturamento])

````
Mede o faturamento bruto por item vendido

É o campo raiz do Decomposition Tree


##  Mapa com Bolhas — Crescimento Acumulado por Continente
---

Visual que mostra a **evolução de faturamento** ano a ano em diferentes regiões geográficas. Cada bolha representa um continente, com tamanho proporcional ao crescimento percentual.


###  Medida: `Crescimento Acumulado Anual`

Crescimento Acumulado Anual = 
VAR Crescimento = 
    DIVIDE(
Compara o faturamento acumulado no ano atual (YTD) com o mesmo período do ano anterior (YTD LY)

Retorna o crescimento percentual

Usa HASONEVALUE para garantir que o cálculo é feito por ano específico

COALESCE previne retorno vazio


##  Enlighten Story — Categoria com Maior Variação de Faturamento
---

História visual que revela **qual categoria teve a maior mudança percentual e em valor monetário** no faturamento entre os anos de 2017 e 2018. Ideal para relatórios gerenciais ou dashboards executivos.

### Texto " A categoria # realizou # em vendas o que representa um aumento de # em relação ao ano anterior."

###  Medida **Categoria com maior variação**
````
Categoria com maior variação = 
VAR TabelaVariacao = 
    ADDCOLUMNS(
        VALUES(dSubcategoria[Categoria]),
        "Variação", [Variação % Categoria 2018vs2017]
    )
VAR CategoriaMaiorVariacao =
    TOPN(1, TabelaVariacao, [Variação], DESC)

RETURN
    MAXX(CategoriaMaiorVariacao, dSubcategoria[Categoria])
Variação % Categoria 2018vs2017 = 
VAR Percentual2017 = 
    CALCULATE(
        [% Faturamento Categoria],
        dCalendario[Ano] = 2017
    )
````
### Medida **Valor Variação Categoria Maior Crescimento**
````
Valor Variação Categoria Maior Crescimento = 
VAR TabelaVariacao = 
    ADDCOLUMNS(
        VALUES(dSubcategoria[Categoria]),
        "Variação", [Variação % Categoria 2018vs2017],
        "Valor2018", CALCULATE([Faturamento Total], dCalendario[Ano] = 2018),
        "Valor2017", CALCULATE([Faturamento Total], dCalendario[Ano] = 2017)
    )

VAR CategoriaMaiorVariacao =
    TOPN(1, TabelaVariacao, [Variação], DESC)

RETURN
    MAXX(
        CategoriaMaiorVariacao,
        [Valor2018] - [Valor2017]
    )
````


### Medida **Valor % Variação Categoria Maior Crescimento**
````
Valor % Variação Categoria Maior Crescimento = 
VAR TabelaVariacao = 
    ADDCOLUMNS(
        VALUES(dSubcategoria[Categoria]),
        "Variação", [Variação % Categoria 2018vs2017]
    )

VAR CategoriaMaiorVariacao =
    TOPN(1, TabelaVariacao, [Variação], DESC)

RETURN
    MAXX(CategoriaMaiorVariacao, [Variação])

````
Compara a participação percentual e o valor monetário da categoria no faturamento total entre 2017 e 2018.

Retorna variação positiva ou negativa


##  Enlighten Story — Queda nas Vendas de Desktops por Continente
---

Narrativa visual que destaca o mercado mais afetado pela queda de faturamento de desktops, com base na análise ano a ano e impacto na representatividade global.

###  Medida 1 — `Variação Representatividade Desktops`

```
Variação Representatividade Desktops = 
VAR Rep2018 = 
    CALCULATE(
        [% Representatividade Desktops],
        dCalendario[Ano] = 2018
    )

VAR Rep2017 = 
    CALCULATE(
        [% Representatividade Desktops],
        dCalendario[Ano] = 2017
    )

RETURN
DIVIDE(Rep2018 - Rep2017, Rep2017)
````
Mede a mudança percentual na participação dos Desktops dentro do faturamento total

Usa uma medida pré-existente: % Representatividade Desktops

Valor negativo indica perda de relevância
````

Continente Maior Queda Desktops = 
CALCULATE(
    MAX(dLocalizacao[Continente]),
    TOPN(
        1,
        ADDCOLUMNS(
            VALUES(dLocalizacao[Continente]),
            "Queda",
            CALCULATE(
                [Faturamento Desktops],
                dCalendario[Ano] = 2018
            ) - CALCULATE(
                [Faturamento Desktops],
                dCalendario[Ano] = 2017
            )
        ),
        [Queda],
        ASC
    )
)
````
Cria uma tabela de variação absoluta no faturamento por continente

Usa TOPN com ordenação crescente para capturar a maior queda

Retorna o nome do continente

##  Frase Narrativa para o Enlighten Story
---

"Entre 2017 e 2018, vendas de Desktops recuaram #Variação Representatividade Desktops, influenciadas principalmente pelo continente #Continente Maior Queda Desktops."


##  Design e UX
---

- Cores baseadas em contraste para fácil leitura
- Ícones intuitivos e layout responsivo
- Uso de tooltip para experiência aprofundada


 As visualizações foram pensadas para contar a história dos dados de forma clara, interativa e estratégica. Cada painel responde a uma pergunta de negócio e facilita a tomada de decisões.

###  Controles de Navegação e Filtro Temporal
---

Componentes de interatividade que facilitam a exploração do relatório por ano e seção, mantendo a experiência fluida e intuitiva.


####  Slicer de Ano

- Campo: `dCalendario[Ano]`
- Tipo: ***Dropdown** 
- Valores fixos: 2017, 2018, 2019

####  Slicer de Continente

- Campo: `dLocalizacao[Continente]`
- Tipo: ***Dropdown** 
- Valores fixos: Asia, Europe, North America


####  Slicer de Categoria

- Campo: `dSubcategoria[Categoria]`
- Tipo: ***Dropdown** 
- Valores fixos: Audio, Computers, TV and Video
---

####  Botões de Navegação

- Objetivo: Alternar entre páginas de forma elegante e guiada
- Componentes:
  - Botão " Ir para o Dashboard" > Acessa o Dashboard a partir da capa levando para a " Visão Geral"
  - Botão “ Seta para a direita” > Leva para a página " Panorama de Vendas"
  - Botão “ Seta para a esquerda” > Retorna para a " Visão Geral"
  - Botão “ Home” > Retorna para a " Capa" do Dashboard
  
- Configuração:
  - Botão “ Home”: Ícone de Casa + Botão em Branco 
  - Botão padrão: forma própria com ícone
  - Ação: **Navegar para página específica**
  - Posicionamento: área superior a esquerda
 
  ####  Botão de Informação
  - Objetivo: Oeientar ao usuário sobre o detalhameneto de informações com o tooltip
  - Componente:
    - Botão " i" > abre uma visualização explicativa 
    - Botão " Reset" > Retorna à navegação da página " Visão Geral"
   
 - Configuração:
   - Botão padrão " i" e "Reset" nativos do Power BI
   - Ação: **Navegar entre  Bookmarkers**
   - Posisionamento: No canto inferior direito do gráfico de rosca  e vinculado ao gráfico de rosca e a tabela na página " Visão Geral"

````

