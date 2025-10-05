# Sales vs Targets Challenge / Case Directy

## Introduction

This project was developed as part of the analytical challenge “Sales vs Targets,” with the objective of building an interactive Power BI dashboard capable of answering strategic questions about the commercial performance of a retail company — Case Directy. The analysis covers the years 2017 and 2018, focusing on revenue targets, variations by category and subcategory, and geographic distribution of sales.

The solution was built using unstructured data, cleaned and modeled in Power Query, and organized into a star-schema dimensional structure. Analytical measures were developed in DAX, with emphasis on the use of the `TREATAS` function to integrate targets without a physical relationship between tables.

The final dashboard enables time-based segmentation, period comparisons, hierarchical analysis by product and region, and narrative visualizations that directly address the case questions. The documentation below outlines each stage of the project following the CRISP-DM framework.

---

## 1. Business Understanding

The primary objective was to evaluate Directy's commercial performance against established targets, focusing on:

- Revenue by period, category, and subcategory
- Comparison between 2017 and 2018
- Identification of significant regional variations
- Detection of critical declines and growth by product

The questions provided in the case served as a guide for building the dashboard.

---

## 2. Data Understanding

The dataset was composed of heterogeneous sources, including CSV files, spreadsheets, and JSON structures. Each file presented distinct formatting and semantic challenges:

- `Produto.csv`: contained mixed textual data with brand headers, product descriptions, and subcategory references embedded in a single column. Required parsing of identifiers and normalization of product names.
- `Localizacao.csv`: included hierarchical geographic data (continent, country, state/province, city) with inconsistent formatting, null values, and prefixed labels. Demanded reconstruction of location hierarchy and deduplication.
- `Subcategoria.csv`: structured as nested objects, requiring conversion to a relational table and reconciliation with product-level data.
- `Clientes.csv`: featured asymmetric columns for individual and corporate clients, requiring schema harmonization.
- `fMetasConsolidadas`: presented targets by year and continent in matrix layout, lacking relational keys and requiring transformation for time-series analysis.
- `Vendas.csv`: transactional dataset with generic column names and invalid records, necessitating validation and enrichment.

---

## 3. Data Preparation

Transformations were executed in Power Query, with emphasis on standardization, referential integrity, and automation. Key procedures included:

- Removal of control rows, blank lines, and invalid entries across all sources.
- Delimiter-based field separation in product and location files to extract structured attributes.
- Type conversion with locale awareness, especially for date and currency formats.
- Creation of derived columns for segmentation (e.g., region, client type, product hierarchy).
- Normalization of naming conventions, including removal of prefixes and casing adjustments.
- Unification of annual datasets using Append Queries for consistent temporal analysis.
- Selective unpivoting of matrix-formatted targets to enable year-over-year comparisons.
- Integration of non-relational data (e.g., metas) using DAX functions such as TREATAS, simulating relationships without physical joins.

These steps ensured a clean, scalable, and semantically rich data model suitable for analytical exploration and dashboard development.

---

## 4. Modeling

A star-schema structure was adopted, with the fact table `fVendas` centralizing transactions and connected to:

- `dCalendario`: time-based segmentation
- `dProduto`: link to subcategory and category
- `dLocalizacao`: geographic hierarchy
- `dCliente`: demographic profile
- `dSubcategoria`: commercial grouping

Analytical measures were organized in the **Measures** table. The `fMetasConsolidadas` table was integrated via DAX using `TREATAS`.

---

## 5. Analytical Modeling – DAX Measures

Key measures developed:

- `Faturamento Total`: total revenue
- `Meta Total por Ano`: target by year using `TREATAS`
- `% Atingimento da Meta`: revenue vs target
- `% Cresc YoY`: year-over-year growth
- `Categoria Campeã`: top category by revenue
- `% Categoria Campeã`: share of top category
- `% Faturamento SubCategoria`: subcategory share
- `Variação % Categoria 2018vs2017`: category variation
- `Continente Maior Queda Desktops`: continent with largest drop in Desktops

---

## 6. Evaluation – Case Question Responses

### Exploratory Analysis  
*Note: 2019 was still in progress during the analysis.*

- **Total revenue**: R$ 171M  
  (2017: R$ 67M; 2018: R$ 85M; 2019: R$ 19M)
- **Revenue target**: R$ 205.02M  
  (2017: R$ 74.36M; 2018: R$ 80.73M; 2019: R$ 49.93M)
- **% target achievement**: 83.59% overall  
  (2017: 90.37%; 2018: 105.88%; 2019: 37.47%)
- **Top category**: Computers
- **Top category share**: 70.22%
- **Top 3 subcategories**: Projectors & Screens, Laptops, Desktops
- **Top continents in subcategories**: North America, Europe, Asia

### 2017 vs 2018 Comparison

- Revenue variation: +27.19%
- Positive variation in all subcategories: Yes
- Change in Desktops representativity: –4.23%
- Continent responsible for decline in Desktops: Europe
- Top category in 2018: Computers – R$ 60M
- Category with highest growth: TV Video (+12.34%)
- Top 3 subcategories in 2018: Projectors & Screens, Laptops, Desktops
- Same subcategories lead in all periods: Yes

---

## 7. Deployment

The dashboard was published to Power BI Online with public access. It supports automated updates via Power Query.

---

## Public Dashboard Link

[View the dashboard on Power BI Online](https://app.powerbi.com/view?r=eyJrIjoiNDY1ZTVkMTEtODU4ZC00NjlkLTg2MWUtMmQxZGRhNzdlYmFlIiwidCI6IjY1OWNlMmI4LTA3MTQtNDE5OC04YzM4LWRjOWI2MGFhYmI1NyJ9)

---

## Dashboard Illustrations

### Cover  
![Dashboard Screenshot – Overview](https://github.com/user-attachments/assets/89282b90-62c3-402a-8086-5b0855145e26)

### General Overview  
![Dashboard Screenshot – Comparative Analysis](https://github.com/user-attachments/assets/b5cb1c9e-b442-4953-a3e6-628a9344c016)

### Sales Panorama  
![Dashboard Screenshot – Comparative Analysis](https://github.com/user-attachments/assets/44c1f6cb-a1ae-4b79-a2fe-05f7a216626a)

---

## Interactive Components

- Slicers by year, category, and continent
- Navigation buttons across thematic pages
- Tooltips with detailed metrics and expandable explanatory text
- Visual stories highlighting critical variations

---

## 8. Technical Considerations

- DAX measures optimized using `DIVIDE`, `CALCULATE`, `SUMX`, `TOPN`, `MAXX`
- Use of `TREATAS` for integration without physical relationships
- Context-driven visualizations: bar charts, donut charts, cross matrices, bubble maps
- Full documentation of Power Query transformations
- Modular and scalable structure for future periods or variables

