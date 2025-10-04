# Desafio Meta Vendas/ Case Directy

## Introdução
Este projeto foi desenvolvido como parte do desafio analítico “Vendas x Metas”, com o objetivo de estruturar um painel interativo em Power BI capaz de responder a perguntas estratégicas sobre o desempenho comercial de uma empresa de comércio varejista - Case Directy. A análise cobre os anos de 2017 e 2018, com foco em metas de faturamento, variações por categoria e subcategoria, e distribuição geográfica das vendas.

A solução foi construída com base em dados desestruturados, tratados e modelados no Power Query, e organizados em uma estrutura dimensional em estrela. As medidas analíticas foram desenvolvidas em DAX, com destaque para o uso da função TREATAS para integração de metas sem relacionamento físico direto.

O painel final permite segmentações temporais, comparações entre períodos, análise hierárquica por produto e região, e visualizações narrativas que respondem diretamente às perguntas do case. A documentação a seguir detalha cada etapa do projeto segundo o framework CRISP-DM, incluindo tratamento de dados, modelagem, medidas, validação e respostas às questões propostas.

### 1. Entendimento do Negócio
#### O objetivo central era avaliar a performance comercial da Directy em relação às metas estabelecidas, com foco em:

Faturamento por período, categoria e subcategoria

Comparação entre os anos de 2017 e 2018

Identificação de variações relevantes por região

Detecção de quedas e crescimentos críticos por produto

As perguntas foram fornecidas como parte do escopo do case e serviram de guia para a construção do painel.

### 2. Entendimento dos Dados
#### As fontes de dados incluíram arquivos CSV e planilhas com estrutura heterogênea. Os principais arquivos tratados foram:

Produto.csv: dados mistos com marca, produto e subcategoria em formato textual

Localizacao.csv: campos combinados e duplicados

Subcategoria.csv: estrutura plana, convertida em tabela relacional

Clientes.csv: dados de PF e PJ com colunas assimétricas

fMetasConsolidadas: metas por ano e continente, originalmente em layout de matriz

Vendas.csv: base transacional com colunas genéricas e registros inválidos

### 3. Preparação dos Dados
#### As transformações foram realizadas no Power Query, com foco em padronização, integridade referencial e automação. As principais etapas incluíram:

Remoção de linhas em branco e registros inválidos

Separação de campos combinados por delimitadores

Conversão de tipos com localidade (ex: datas brasileiras)

Criação de colunas derivadas para segmentações (ex: região, tipo de cliente)

Padronização de nomenclaturas e estrutura tabular

Unificação de arquivos por ano via Append Queries

Despivotamento seletivo para transformar colunas em linhas

### 4. Modelagem
#### Foi adotada uma estrutura em estrela, com a tabela fato fVendas centralizando as transações e conectada às seguintes dimensões:

dCalendario: segmentações temporais

dProduto: vínculo com subcategoria e categoria

dLocalizacao: hierarquia geográfica

dCliente: perfil demográfico

dSubcategoria: agrupamento comercial

As medidas analíticas foram organizadas na tabela Medidas. A tabela fMetasConsolidadas foi integrada via DAX com uso da função TREATAS, permitindo alinhamento temporal sem duplicações.

### 5. Modelagem Analítica – Medidas DAX
#### As principais medidas desenvolvidas incluem:

Faturamento Total: soma direta da coluna Faturamento

Meta Total por Ano: cálculo via TREATAS para alinhar ano entre tabelas

% Atingimento da Meta: relação entre faturamento e meta

% Cresc YoY: variação percentual ano sobre ano

Categoria Campeã: categoria com maior faturamento líquido

% Categoria Campeã: representatividade da categoria líder

% Faturamento SubCategoria: participação percentual por subcategoria

Variação % Categoria 2018vs2017: variação percentual entre anos

Continente Maior Queda Desktops: continente com maior queda absoluta em Desktops

### 6. Avaliação – Respostas às Perguntas do Case
#### Exploratório
Faturamento total do período: R$ 1.245.000,00

Meta de faturamento: R$ 1.300.000,00

% de atendimento da meta: 95,8%

Categoria com maior faturamento: Tecnologia

Representatividade da categoria líder: 41,7%

Top 3 subcategorias por faturamento: Desktops, Celulares, Acessórios

Ranking de continentes por faturamento nas 3 subcategorias: América do Norte, Europa, Ásia

Comparativo 2017 vs 2018
Variação de faturamento entre 2018 e 2017: +12,4%

Variação positiva em todas as subcategorias? Não. Desktops apresentaram queda.

Variação de representatividade da subcategoria Desktops: −8,2%

Continente responsável pela queda em Desktops: América do Norte

Categoria com maior faturamento em 2018: Tecnologia – R$ 520.000,00

Categoria com maior variação percentual entre 2017 e 2018: Móveis – +18,7%

Top 3 subcategorias em 2018: Celulares, Acessórios, Impressoras

As mesmas subcategorias lideram em todos os períodos? Parcialmente. Desktops lideraram em 2017, mas perderam posição em 2018.

### 7. Implantação
O painel foi publicado no Power BI Online com acesso público. A estrutura permite atualização automatizada via Power Query. 
---

##  Link Público do Dashboard

 [Acesse o dashboard publicado no Power BI Online](https://app.powerbi.com/view?r=eyJrIjoiNDY1ZTVkMTEtODU4ZC00NjlkLTg2MWUtMmQxZGRhNzdlYmFlIiwidCI6IjY1OWNlMmI4LTA3MTQtNDE5OC04YzM4LWRjOWI2MGFhYmI1NyJ9)

---

##  Ilustrações do Painel

### Preview 1
![Screenshot do Dashboard - Visão Geral](https://github.com/user-attachments/assets/700f4273-4ff0-4183-8b6c-0b1d4eeab054
)


### Preview 2
![Screenshot do Dashboard - Análise Comparativa](https://github.com/user-attachments/assets/1608bd87-b6d3-4e16-bde0-a5a541f254d1
)

---
#### Componentes interativos incluem:

Slicers por ano, categoria e localização

Botões de navegação entre páginas temáticas

Tooltips com métricas detalhadas por célula

Histórias visuais para variações críticas

### 8. Considerações Técnicas
Medidas DAX otimizadas com DIVIDE, CALCULATE, SUMX, TOPN, MAXX

Uso de TREATAS para integração sem relacionamento físico

Visualizações orientadas por contexto: barras horizontais, gráficos de rosca, matriz cruzada, mapa com bolhas

Documentação completa das transformações no Power Query

Estrutura modular e escalável para inclusão de novos períodos ou variáveis
