## 🎯 Estrutura de Modelagem

O projeto adota uma modelagem em estrela, com a tabela fato `fVendas` centralizando as transações, conectada a cinco tabelas de dimensão:

- `dCalendario`: permite segmentação temporal por ano, mês e dia.
- `dProduto`: contém detalhes do produto e da subcategoria.
- `dLocalizacao`: estrutura geográfica hierárquica.
- `dCliente`: perfil demográfico e cadastro de cliente.
- `dSubcategoria`: mapeia subcategorias aos grupos maiores (categorias).

As medidas analíticas estão agrupadas na tabela `Medidas`, mantendo o modelo organizado.

A tabela `fMetasConsolidadas` apresenta metas de faturamento por ano, categoria e continente. Devido à sua granularidade, **não foi possível criar relacionamentos físicos diretos**, então sua integração foi feita por meio de **DAX com `TREATAS`**, garantindo análise precisa sem duplicações.

> 📌 Layout visual: dimensões no topo, fato na base — padrão adotado para clareza de filtragem e leitura técnica.


---

## ✅ Validação da Modelagem

- Conferência via Diagrama de Relacionamentos.
- Testes com medidas de desempenho para garantir integridade.

![Modelagem Power BI](https://github.com/user-attachments/assets/b0445813-abfd-4097-9247-25aa521a5299)


> Estrutura de modelagem em estrela com tabela fato central (`fVendas`) e dimensões conectadas. Inclui também tabela `fMetasConsolidadas` tratada via DAX.

