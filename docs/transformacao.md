# Transformações de Dados – Power Query

Este documento detalha as etapas de tratamento e limpeza aplicadas às bases do projeto "Desafio Vendas x Metas", utilizando o Power Query no Power BI. O foco foi transformar dados desestruturados em tabelas relacionáveis e confiáveis para modelagem e visualização.

---

## 📦 Produto.csv

### Problemas detectados:
- Linhas com apenas `Marca: Nome` sem estrutura tabular.
- Nome dos Produtos com hashtag e ID do Produtos juntos na mesma coluna: 'Produto #116 - Adventure Works 20" CRT TV E15 Silver'
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

## 🌍 Arquivo Localização.csv → Tabela `dLocalização`

### 🐞 Problemas detectados
- Colunas com nomes genéricos ou inconsistentes
- Presença de valores nulos ou duplicados
- Dados combinados em uma única coluna (ex: “UF - Cidade”)

### 🔄 Transformações aplicadas no Power Query
1. **Renomear colunas**  
   Padronização dos títulos para `idLocalizacao`, `UF`, `Cidade`, `Regiao`, etc.

2. **Separação de campos combinados**  
   Coluna original dividida para extrair UF e Cidade com delimitador " - "

3. **Remoção de duplicados**  
   Aplicado "Remover duplicatas" com base em `UF + Cidade` para garantir unicidade

4. **Tratamento de valores nulos**  
   Preenchimento ou exclusão de linhas com campos essenciais ausentes

5. **Criação de coluna de região**  
   Com base na UF, foi atribuída uma região geográfica (ex: Sudeste, Norte…)

6. **Conversão de tipo e ordenação**  
   Ajuste dos tipos para garantir integridade nos relacionamentos

> 🔗 **Relacionamento na modelagem**: Conectado à tabela `fVendas` via `idLocalizacao`


## 🗂️ Arquivo Subcategoria.csv → Tabela `dSubcategoria`

### 🐞 Problemas detectados
- Arquivo em formato simples (CSV plano), com colunas não nomeadas inicialmente
- Dados contidos em uma coluna expandida a partir de outra estrutura
- Tipos de dados não estavam definidos corretamente

### 🔄 Transformações aplicadas no Power Query
1. **Conversão para tabela**  
   Foi utilizado o comando `Table.FromList` ou equivalente para transformar a estrutura inicial em formato tabular.

2. **Expansão da coluna**  
   A coluna única foi expandida para revelar `idSubcategoria`, `Subcategoria`, e `Categoria`.

3. **Conversão de tipos**  
   Aplicado `Table.TransformColumnTypes` para garantir que os três campos estejam como `type text` — conforme visto no código da barra de fórmula.

> 🔗 **Relacionamento na modelagem**: Conectado à tabela `dProduto` via `idSubcategoria`
> 
---

## 📊 fMetasConsolidadas — Metas globais por categoria e ano

### 🐞 Problemas detectados
- Dados vieram de múltiplas consultas (2018, 2019…) e precisaram ser unificados
- Estrutura original com layout em matriz, não ideal para modelagem
- Presença de valores nulos ou repetidos
- Nomes de categorias inconsistentes (ex: “Total” agrupando outras linhas)

### 🔄 Transformações aplicadas no Power Query
1. **Importação e navegação da fonte**  
   Carregadas as planilhas originais contendo metas por ano/região.

2. **Preenchimento para cima** (`Filled Up`)  
   Usado para copiar valores de categoria para linhas vazias logo abaixo.

3. **Remoção de duplicatas**  
   Aplicado para evitar sobreposição ao unir os anos.

4. **Promoção de cabeçalhos**  
   Padronizou os nomes das colunas.

5. **Criação de coluna personalizada**  
   Provavelmente usada para inserir o ano ou algum identificador extra.

6. **Renomeação e padronização de colunas**  
   Ajustado para os nomes finais: `Categoria`, `Ano`, `Continente`, `$ Value`

7. **Unificação de anos**  
   Aplicado o **Append Queries** para juntar tabelas de diferentes anos.

8. **Despivotamento seletivo**  
   Transformou colunas de valores por continente em linhas, com os rótulos `Continente` e `$ Value`.

9. **Conversão de tipos**  
   Ajustado para garantir que os dados monetários e temporais estejam corretos.

> 🔗 **Relacionamento na modelagem**: Usado para análises comparativas e metas por região/categoria, cruzando com `dProduto`, `dLocalizacao` e `fVendas`.


---

## 🧑‍💼 Arquivo Clientes.csv → Tabela `dCliente`

### 🐞 Problemas detectados
- Existência de dois tipos de clientes: Pessoa Física e Empresa
- Estrutura desigual entre os tipos, resultando em múltiplas colunas com valores `null` (ex: Nome Empresa para PF, Data Nascimento para PJ)
- Formato da data de nascimento com erro de localidade
- Variação nos padrões de texto para ocupação e educação

### 🔄 Transformações aplicadas no Power Query
1. **Promoção de cabeçalhos**  
   Reconhecimento automático dos nomes das colunas originais.

2. **Conversão de tipos padrão**  
   Aplicado `Changed Type` para transformar colunas iniciais em tipos esperados.

3. **Extração de informações textuais**  
   Utilizado `Extracted Text Between Delimiters` para isolar valores específicos (provavelmente na coluna de e-mail ou empresa).

4. **Correção de tipo com localidade**  
   Ajustado `Data de Nascimento Corrigida` para reconhecer data no formato brasileiro com `Changed Type with Locale`.

5. **Substituição de valores inconsistentes**  
   Aplicado `Replaced Value` para padronizar textos divergentes (ex: “Graduado” vs “Ensino Superior”).

6. **Criação de coluna condicional**  
   Definido tipo de cliente (PF ou PJ) com base na presença ou ausência de `Nome da Empresa`.

7. **Tratamento dos campos nulos**  
   Remoção ou substituição de valores nulos desnecessários para manter a estrutura consistente.

8. **Remoção de colunas não utilizadas**  
   Excluídas colunas redundantes ou sem utilidade analítica para o modelo.

> 🔗 **Relacionamento na modelagem**: Associada à tabela `fVendas` via `Email`; segmentações por tipo de cliente permitem análises personalizadas por perfil de consumo.

---

## 💰 Arquivo Vendas.csv → Tabela `fVendas`

### 🐞 Problemas detectados
- Presença de linhas em branco ou inválidas
- Colunas com nomenclaturas genéricas (ex: "Column1", "Column2")
- Tipos de dados não estavam corretamente atribuídos
- Possíveis valores ausentes ou mal estruturados em campos chave

### 🔄 Transformações aplicadas no Power Query
1. **Remoção de linhas em branco**  
   Utilizado `Removed Blank Rows` para eliminar registros sem informação.

2. **Filtragem inicial de dados**  
   Aplicado `Filtered Rows` para excluir linhas inválidas ou com valores nulos em campos essenciais.

3. **Promoção de cabeçalhos**  
   Reconhecimento da primeira linha como os títulos das colunas reais.

4. **Conversão de tipos**  
   Aplicado `Changed Type` para garantir que campos como datas (`Data Venda`, `Data Envio`) e IDs (`ID Produto`, `ID Cliente`, etc.) estejam com os tipos apropriados (date, text ou number).

> 🔗

---

📌 Todas as transformações foram realizadas no Power Query dentro do Power BI para garantir reusabilidade e automação. Essa abordagem permite que futuras atualizações sejam incorporadas com mínimo esforço manual.


