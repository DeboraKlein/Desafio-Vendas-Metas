##  Análises Macro

O painel desenvolvido foi estruturado para responder de forma clara e objetiva às questões abaixo, permitindo navegação fluida e extração de insights por meio de filtros dinâmicos.

###  Exploratório

- **Qual foi o faturamento de todo o período?**
- **Qual era a meta de faturamento deste período?**
- **Qual foi o percentual de atendimento da meta?**
- **Qual foi a categoria com maior faturamento?**
- **O quanto esta categoria representa no total do faturamento, em percentual?**
- **Quais foram as 3 subcategorias com maior faturamento?**
- **Considerando estas mesmas 3 subcategorias, indique os continentes, do maior para o menor faturamento.**

###  Comparativo Entre os Anos (2017 vs 2018)

- **Qual foi a variação entre o faturamento de 2018 e o ano anterior?**
- **A variação entre o faturamento de 2018 e o ano anterior foi positiva em todas as subcategorias?**
- **Qual foi o percentual de variação de representatividade entre 2018 e o ano anterior na subcategoria *Desktops*?**
- **Qual foi o continente responsável por essa queda de faturamento na subcategoria *Desktops*?**
- **Qual foi a categoria com o maior faturamento em 2018? Qual o valor em R$?**
- **Qual foi a categoria com a maior variação percentual entre 2017 e 2018?**
- **Quais foram as 3 subcategorias com maior faturamento em 2018?**
- **São as mesmas subcategorias em destaque considerando todos os períodos?**

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

##  Solução Técnica: Evitando Duplicações com TREATAS

Durante o desenvolvimento, foi identificado que as metas estavam sendo duplicadas ao aplicar filtros por Ano. Isso ocorreu pela ausência de relacionamento direto entre a tabela de calendário (`dCalendario`) e a tabela de metas consolidadas (`fMetasConsolidadas`).

Para solucionar, foi utilizada a função `TREATAS` no DAX:

```DAX
Meta Total por Ano = 
CALCULATE(
    SUM('fMetasConsolidadas'[Value]),
    'fMetasConsolidadas'[Categoria] <> "Total",
    TREATAS(VALUES(dCalendario[Ano]), 'fMetasConsolidadas'[Ano])  
)



