# Desafio Meta Vendas/ Case Directy

## Introdução
Este projeto foi desenvolvido como parte do desafio analítico “Vendas x Metas”, com o objetivo de estruturar um painel interativo em Power BI capaz de responder a perguntas estratégicas sobre o desempenho comercial de uma empresa de comércio varejista - Case Directy. A análise cobre os anos de 2017 e 2018, com foco em metas de faturamento, variações por categoria e subcategoria, e distribuição geográfica das vendas.

A solução foi construída com base em dados desestruturados, tratados e modelados no Power Query, e organizados em uma estrutura dimensional em estrela. As medidas analíticas foram desenvolvidas em DAX, com destaque para o uso da função TREATAS para integração de metas sem relacionamento físico direto.

O painel final permite segmentações temporais, comparações entre períodos, análise hierárquica por produto e região, e visualizações narrativas que respondem diretamente às perguntas do case. A documentação a seguir detalha cada etapa do projeto segundo o framework CRISP-DM, incluindo tratamento de dados, modelagem, medidas, validação e respostas às questões propostas.

### 1. Entendimento do Negócio
#### O objetivo central era avaliar a performance comercial da Directy em relação às metas estabelecidas, com foco em:

- Faturamento por período, categoria e subcategoria

- Comparação entre os anos de 2017 e 2018

- Identificação de variações relevantes por região

- Detecção de quedas e crescimentos críticos por produto

- As perguntas foram fornecidas como parte do escopo do case e serviram de guia para a construção do painel.

### 2. Entendimento dos Dados
#### As fontes de dados incluíram arquivos CSV e planilhas com estrutura heterogênea. Os principais arquivos tratados foram:

- ``Produto.csv``: continha dados textuais mistos com cabeçalhos de marca, descrições de produtos e referências de subcategoria combinadas em uma única coluna. Exigiu parsing de identificadores e normalização dos nomes dos produtos.

- ``Localizacao.csv``: incluía dados geográficos hierárquicos (continente, país, estado/província e cidade) com formatação inconsistente, valores nulos e rótulos prefixados. Demandou reconstrução da hierarquia de localização e deduplicação

- ``Subcategoria.json``: estruturado como objetos aninhados, exigiu conversão para tabela relacional e reconciliação com os dados de produto.

- ``Clientes.xlsx``:apresentava colunas assimétricas para clientes pessoa física e jurídica, exigindo harmonização de esquema.

- ``Metas.xlsx``: apresentava metas por ano e continente em layout matricial, sem chaves relacionais, exigindo transformação para análise temporal.

- ``Vendas.xlsx``: base transacional com nomes genéricos de colunas e registros inválidos, exigindo validação e enriquecimento.

### 3. Preparação dos Dados
#### As transformações foram realizadas no Power Query, com foco em padronização, integridade referencial e automação. As principais etapas incluíram:

- Remoção de linhas de controle, linhas em branco e registros inválidos em todas as fontes.

- Separação de campos combinados por delimitadores nos arquivos de produto e localização para extrair atributos estruturados.

- Conversão de tipos com reconhecimento de localidade, especialmente para formatos de data e moeda.
- Criação de colunas derivadas para segmentações (ex: região, tipo de cliente)

- Criação de colunas derivadas para segmentações (ex: região, tipo de cliente, hierarquia de produto).

- Normalização de nomenclaturas, incluindo remoção de prefixos e ajustes de capitalização.

- Unificação de arquivos anuais por meio de Append Queries para permitir análise temporal consistente.

- Despivotamento seletivo de metas em formato matricial para viabilizar comparações ano a ano.

- Integração de dados sem relacionamento físico (ex: metas) utilizando funções DAX como TREATAS, simulando relacionamentos sem junções físicas.

### 4. Modelagem
#### Foi adotada uma estrutura em estrela, com a tabela fato fVendas centralizando as transações e conectada às seguintes dimensões:

- ``dCalendario``: segmentações temporais

- ``dProduto``: vínculo com subcategoria e categoria

- ``dLocalizacao``: hierarquia geográfica

- ``dCliente``: perfil demográfico

- ``dSubcategoria``: agrupamento comercial


Além das dimensões principais, foi criada a tabela auxiliar fMetasConsolidadas, responsável por consolidar os dados de metas de faturamento dos anos de 2017, 2018 e 2019. Essa tabela foi estruturada manualmente a partir de planilhas em formato matricial, convertida em formato tabular com colunas normalizadas para:

- Ano

- Continente

- Categoria

- Meta de Faturamento

Como não há relacionamento físico entre fMetasConsolidadas e as demais tabelas do modelo, sua integração foi realizada via DAX utilizando a função TREATAS, permitindo o alinhamento temporal e geográfico das metas com os dados reais de vendas sem duplicações ou conflitos de cardinalidade.


### 5. Modelagem Analítica – Medidas DAX
#### As principais medidas desenvolvidas incluem:

- **Faturamento Total**: soma direta da coluna Faturamento

- **Meta Total por Ano**: cálculo via TREATAS para alinhar ano entre tabelas

- **% Atingimento da Meta**: relação entre faturamento e meta

- **% Cresc YoY**: variação percentual ano sobre ano

- **Categoria Campeã**: categoria com maior faturamento líquido

- **% Categoria Campeã**: representatividade da categoria líder

- **% Faturamento SubCategoria**: participação percentual por subcategoria

- **Variação % Categoria 2018vs2017**: variação percentual entre anos

- **Continente Maior Queda Desktops**: continente com maior queda absoluta em Desktops

### 6. Avaliação – Respostas às Perguntas do Case
#### Exploratório
##### Considerando que o ano de 2019 ainda estava em curso durante a análise:

- Faturamento total do período: Total R$ 171M (2017: R$67M; 2018: R$85M; 2019: R$19M)

- Meta de faturamento: Total R$ 205,02M (2017: R$74,36M; 2018: R$80,73M; 2019: R$49,93M)

- % de atendimento da meta: Total 83,59% (2017: 90,37%; 2018: 105,88%; 2019: 37,47%)

- Categoria com maior faturamento: Computers

- Representatividade da categoria líder: 70,22%

- Top 3 subcategorias por faturamento: Projectors & Screens, Laptops, Deescktops

- Ranking de continentes por faturamento nas 3 subcategorias: América do Norte, Europa, Ásia

- Comparativo 2017 vs 2018

- Variação de faturamento entre 2018 e 2017: +27,19%

- Variação positiva em todas as subcategorias? Sim

- Variação de representatividade da subcategoria Desktops: -4,23%

- Continente responsável pela queda em Desktops: Europa

- Categoria com maior faturamento em 2018: Computers – R$ 60M

- Categoria com maior variação percentual entre 2017 e 2018: TV Video +12,34% de aumento nas vendas

- Top 3 subcategorias em 2018:  Projectors & Screens, Laptops, Descktops

- As mesmas subcategorias lideram em todos os períodos? sim

### 7. Implantação
O painel foi publicado no Power BI Online com acesso público. A estrutura permite atualização automatizada via Power Query. 
---

####  Link Público do Dashboard

 [Acesse o dashboard publicado no Power BI Online](https://app.powerbi.com/view?r=eyJrIjoiNDY1ZTVkMTEtODU4ZC00NjlkLTg2MWUtMmQxZGRhNzdlYmFlIiwidCI6IjY1OWNlMmI4LTA3MTQtNDE5OC04YzM4LWRjOWI2MGFhYmI1NyJ9)

---

####  Ilustrações do Painel

#### Capa
![Screenshot do Dashboard - Visão Geral](https://github.com/user-attachments/assets/89282b90-62c3-402a-8086-5b0855145e26
)


#### Visão Geral
![Screenshot do Dashboard - Análise Comparativa](https://github.com/user-attachments/assets/b5cb1c9e-b442-4953-a3e6-628a9344c016
)


#### Panorama de vendas
![Screenshot do Dashboard - Análise Comparativa](https://github.com/user-attachments/assets/44c1f6cb-a1ae-4b79-a2fe-05f7a216626a
)

---
#### Componentes interativos incluem:

- Slicers por ano, categoria e continente

- Botões de navegação entre páginas temáticas

- Tooltip com métricas detalhadas com botão que abre texto explicativo

- Histórias visuais para variações críticas

### 8. Considerações Técnicas

- Medidas DAX otimizadas com DIVIDE, CALCULATE, SUMX, TOPN, MAXX

- Uso de TREATAS para integração sem relacionamento físico

- Visualizações orientadas por contexto: barras horizontais, gráficos de rosca, matriz cruzada, mapa com bolhas

- Documentação completa das transformações no Power Query

- Estrutura modular e escalável para inclusão de novos períodos ou variáveis
