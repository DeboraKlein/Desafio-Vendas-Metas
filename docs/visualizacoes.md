# Visualizações e KPIs – Power BI

Este documento apresenta os principais gráficos, indicadores e painéis desenvolvidos no projeto "Desafio Vendas x Metas", focando em performance, metas e insights estratégicos.

---

## 📈 Medidas DAX — Cartão de Meta e Variação YoY

Visual criado com base em medidas aplicadas ao plano de fundo como cartão analítico dinâmico:

---

### 🎯 `Meta Total por Ano`

```DAX
Meta Total por Ano = 
CALCULATE(
    SUM('fMetasConsolidadas'[Value]),
    'fMetasConsolidadas'[Categoria] <> "Total",
    TREATAS(
        VALUES(dCalendario[Ano]), 
        'fMetasConsolidadas'[Ano]
    )  
)

Utiliza TREATAS para alinhar anos entre dCalendario e fMetasConsolidadas, devido à ausência de relacionamento físico

Filtra categorias para excluir o agregado "Total"

Representa o somatório de metas consolidadas por ano
% Variação Meta YoY = 
VAR HasYearSelected = NOT(ISFILTERED(dCalendario[Ano]))
VAR MetaAtual = [Meta Total por Ano]
VAR MetaAnterior = [Meta LY]
RETURN
IF(
    HasYearSelected || ISBLANK(MetaAtual) || ISBLANK(MetaAnterior),
    0,
    DIVIDE(
        MetaAtual - MetaAnterior,
        MetaAnterior,
        0
    )
)


- **Gráficos**:
  - Barras por Categoria e Subcategoria
  - Mapa por Localização
  - Série Temporal (linha) de vendas por mês

- **Segmentações**:
  - Filtros por ano, produto, localização e cliente

> Objetivo: Proporcionar uma visão macro da performance com possibilidade de drill-down.

---

## 🔍 Painel: Desempenho por Localização

- Mapa temático com intensidade de vendas por região
- Tabela com ranking de cidades/estados por volume e metas
- Indicador de conversão por região (% de meta atingida)

---

## 🧑‍🤝‍🧑 Painel: Perfil de Clientes

- Gráfico de pirâmide etária (se houver dados de idade)
- Distribuição por gênero
- Segmentação por localização e volume de compras

---

## 🧠 Painel: Insights e Tendências

- Gráfico de dispersão: Ticket Médio × Volume de Vendas
- Série temporal de metas x vendas
- Gráfico de Pareto para identificar produtos mais rentáveis

---

## 🎨 Design e UX

- Cores baseadas em contraste para fácil leitura
- Ícones intuitivos e layout responsivo
- Uso de tooltip e drill-through para experiência aprofundada

---

📌 As visualizações foram pensadas para contar a história dos dados de forma clara, interativa e estratégica. Cada painel responde a uma pergunta de negócio e facilita a tomada de decisões.

