# Transformações de Dados – Power Query

Este documento detalha as etapas de tratamento e limpeza aplicadas às bases do projeto "Desafio Vendas x Metas", utilizando o Power Query no Power BI. O foco foi transformar dados desestruturados em tabelas relacionáveis e confiáveis para modelagem e visualização.

---

## 📦 Produto.csv

### Problemas detectados:
- Linhas com apenas `Marca: Nome` sem estrutura tabular.
- Produtos com aspas e vírgulas embutidas.
- Subcategorias combinadas em texto: `Subcategoria #ID – Nome`.

### Etapas aplicadas:
1. **Identificação da Marca**:
   - Criada coluna condicional: extrai texto após `"Marca:"`.
2. **Preenchimento para baixo da Marca**:
   - Recurso "Preencher para baixo" aplicado.
3. **Remoção de linhas inválidas**:
   - Excluídas linhas contendo apenas `;` ou nulas.
4. **Separação de Produto e Subcategoria**:
   - Produto: limpeza de aspas e vírgulas.
   - Subcategoria: divisão por `" - "` para obter `ID` e `Nome`.

---

## 🌍 Localizacao.csv

### Problemas detectados:
- Continentes aparecem como "blocos" de texto e não como campo.
- Localizações fragmentadas por tipo: país, estado, cidade.

### Etapas aplicadas:
1. **Detecção de Continente**:
   - Criada coluna condicional identificando blocos de continente.
2. **Preenchimento para baixo**:
   - Associado o continente às linhas subsequentes.
3. **Padronização da Hierarquia**:
   - Organização por `Tipo Localizacao`: País → Estado → Cidade.
4. **Remoção de nulos e cabeçalhos repetidos**.

---

## 🧩 Subcategoria.json

### Estrutura encontrada:
- Cada registro contém `idSubcategoria`, `Subcategoria`, e `Categoria`.

### Etapas aplicadas:
1. **Importação via JSON**.
2. **Expansão dos objetos em colunas**.
3. **Merge com Produto.csv**:
   - Extraído `ID` da subcategoria do CSV e conectado via `idSubcategoria`.

---

## 🎯 Metas.xlsx

### Ações:
- Conferência dos campos.
- Padronização de tipos de dados.
- Identificação de chave para relacionamento com Localização e Categoria.

---

## 🧍 Cliente.xlsx

### Ações:
- Conferência de duplicidades e campos nulos.
- Criação de relacionamento com Localização e Vendas.

---

## 💰 Vendas.xlsx

### Ações:
- Verificação de datas, produtos e clientes válidos.
- Preparação para cálculo de métricas de desempenho.

---

📌 Todas as transformações foram realizadas no Power Query dentro do Power BI para garantir reusabilidade e automação. Essa abordagem permite que futuras atualizações sejam incorporadas com mínimo esforço manual.


