# Desafio Vendas x Metas – Power BI

Este repositório apresenta a solução para o desafio de análise de vendas versus metas, com foco em visualização estratégica através do Power BI. O objetivo foi transformar uma base de dados diversa e desorganizada em um painel interativo, gerador de insights relevantes para decisões comerciais.

## 🧩 Objetivo do Projeto

Criar um dashboard no Power BI que:
- Compare o faturamento real com as metas por continente, categoria e ao longo do tempo.
- Apresente evolução de desempenho entre os anos de 2017, 2018 e até março de 2019.
- Destaque produtos, subcategorias e regiões com maior representatividade.

## 📁 Fontes de Dados

- `Cliente.xlsx`: Cadastro de clientes com dados básicos.
- `Vendas.xlsx`: Transações entre clientes e produtos com data e valor.
- `Metas.xlsx`: Metas de vendas por categoria e localização.
- `Produto.csv`: Produtos com marca e subcategoria (precisou tratamento especial).
- `Localizacao.csv`: Hierarquia geográfica desestruturada.
- `Subcategoria.json`: Subcategorias vinculadas a suas categorias principais.

## 🔧 Tratamento dos Dados

Foi necessário aplicar limpeza e transformação, incluindo:
- Reconstrução da hierarquia de localização (continente → país → estado → cidade).
- Extração da marca e subcategoria dos produtos via lógica condicional.
- Integração do JSON de subcategorias com o CSV dos produtos.
- Remoção de linhas nulas, reorganização de colunas e padronização de formatos.

As transformações foram feitas via Power Query no Power BI, para garantir reusabilidade e consistência.

## 🧠 Modelagem e Relações

As tabelas foram relacionadas conforme chaves lógicas:
- Cliente → Vendas
- Produto → Vendas
- Subcategoria → Produto
- Localização → Cliente e Metas
- Metas → Continente + Categoria

## 📊 Visualizações Criadas

- **Visão Geral de Performance:** Metas vs Vendas com destaque por continente.
- **Ranking de Subcategorias:** Produtos que mais vendem por região.
- **Evolução Temporal:** Comparativo ano a ano com filtros dinâmicos.
- **Análise por Categoria e Localização:** Estratégias para expansão ou foco regional.

## 📌 Principais Insights

- Produtos da categoria *X* se destacam em *Y continente* com alta conversão.
- Algumas regiões têm metas superestimadas ou abaixo do histórico de faturamento.
- Subcategorias *Z* apresentam queda recorrente no primeiro trimestre.

## ⚙️ Como Utilizar

1. Baixe o arquivo `.pbix` na pasta `/powerbi`.
2. Atualize os dados via “Atualizar” no Power BI, mantendo os nomes dos arquivos.
3. Explore os filtros e páginas do dashboard para gerar insights personalizados.

---


