# Modelagem de Dados – Power BI

Este documento descreve a modelagem dimensional aplicada aos dados do projeto "Desafio Vendas x Metas". A estrutura foi pensada para garantir eficiência nas análises, evitar duplicidades e facilitar a criação de medidas e gráficos.

---

## 🔗 Relacionamentos Principais

| Tabela Fato    | Dimensões Associadas         | Tipo de Chave        |
|----------------|------------------------------|-----------------------|
| `Vendas`       | `Produto`, `Cliente`, `Data`, `Localizacao` | Chave substituta e natural |
| `Metas`        | `Localizacao`, `Produto`     | Chave composta        |

> Observação: O campo `ID Subcategoria` foi normalizado via integração com o JSON.

---

## 🧩 Tabelas Dimensão

- **Produto**:
  - Inclui colunas da subcategoria e categoria.
  - Normalizada com o arquivo `Subcategoria.json`.
  - Hierarquia: Categoria > Subcategoria > Produto.

- **Cliente**:
  - Inclui localização e ID único.
  - Utilizada para segmentação de vendas por perfil de cliente.

- **Localizacao**:
  - Inclui país, estado e cidade.
  - Hierarquia implementada via `TipoLocalizacao`.

- **Data**:
  - Tabela calendário criada via M.
  - Contém: Ano, Mês, Dia, Trimestre, Semana, Feriado.

---

## 📊 Tabelas Fato

- **Vendas**:
  - Contém: Valor, Quantidade, Data, ID Produto, ID Cliente.
  - Fato granular a nível de venda.

- **Metas**:
  - Contém: Meta de Venda por Localização e Produto.
  - Fato agregada a nível de mês.

---

## 🧠 Considerações de Performance

- Relacionamentos 1:* com filtro em cascata.
- Tipos de dados otimizados (ex: Inteiros para IDs).
- Evitou-se colunas calculadas na tabela fato, priorizando medidas DAX.

---

## ✅ Validação da Modelagem

- Conferência via Diagrama de Relacionamentos.
- Testes com medidas de desempenho para garantir integridade.

