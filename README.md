# Case
MVP de plataforma de Data &amp; Analytics para vendas de bebidas. Implementação de pipeline de engenharia de dados com PySpark/Python/SQL, modelagem dimensional (Star Schema) e arquitetura de solução em Cloud (Data Lake/BD). Solução focada em responder KPIs de negócio e escalabilidade.

# Arquitetura de dados medallion architecture
<img src="https://www.databricks.com/sites/default/files/inline-images/building-data-pipelines-with-delta-lake-120823.png" alt="Preview do projeto" width="900"/>

A arquitetura **Medallion** organiza o fluxo de dados em três camadas: **Bronze**, **Silver** e **Gold**, garantindo qualidade, consistência e facilidade de análise.

### 🟤 Bronze — Dados Brutos

- **Objetivo:** Armazenar os dados em seu formato original, sem transformações.
- **Fontes:** Vendas em pontos de venda, logs de distribuição, sensores de estoque.
- **Público:** Engenheiros de dados.
- **Exemplo:** Arquivos CSV/JSON com todas as transações de vendas diárias.

---

### ⚪ Silver — Dados Limpos e Integrados

- **Objetivo:** Limpar, validar e integrar dados da Bronze.
- **Processos:** Remoção de duplicatas, preenchimento de dados faltantes, integração com catálogo de produtos e clientes.
- **Público:** Analistas de dados e cientistas de dados.
- **Exemplo:** Tabela de vendas consolidada por SKU, com informações de cliente e região.


---

### 🟡 Gold — Dados Enriquecidos para Negócios

- **Objetivo:** Dados refinados e prontos para análise e tomada de decisão.
- **Processos:** Agregações, cálculo de KPIs como faturamento, ticket médio e margem de lucro.
- **Público:** Gestores, executivos e equipe de BI.
- **Exemplo:** Dashboard de vendas regionais e por canal de distribuição.

---

### 🔄 Fluxo de Dados

1. Dados **raw** chegam na camada **Bronze**.  
2. Transformações e validações na **Silver**.  
3. Dados agregados e prontos para **relatórios e dashboards** na **Gold**.  

---

### 🛠 Tecnologias Utilizadas

![PySpark](https://img.shields.io/badge/-PySpark-EE4C2C?logo=apache-spark&logoColor=fff&style=for-the-badge)
![Python](https://img.shields.io/badge/-Python-3776AB?logo=python&logoColor=fff&style=for-the-badge)
![Delta Lake](https://img.shields.io/badge/-Delta_Lake-1E90FF?logo=databricks&logoColor=fff&style=for-the-badge)
![AWS](https://img.shields.io/badge/-AWS-FF9900?logo=amazon-aws&logoColor=fff&style=for-the-badge)
![Databricks](https://img.shields.io/badge/-Databricks-FC6A2A?logo=databricks&logoColor=fff&style=for-the-badge)
![Streaming Table](https://img.shields.io/badge/-Streaming_Table-00BFFF?logo=apache-spark&logoColor=fff&style=for-the-badge)
![SQL](https://img.shields.io/badge/-SQL-4479A1?logo=postgresql&logoColor=fff&style=for-the-badge)
![Unity Catalog](https://img.shields.io/badge/-Unity_Catalog-8A2BE2?logo=databricks&logoColor=fff&style=for-the-badge)

---

### 📊 Benefícios da Arquitetura Medallion

- Qualidade e confiabilidade dos dados.  
- Escalabilidade para grandes volumes de vendas.  
- Agilidade na criação de **dashboards e relatórios estratégicos**.  
- Base para **modelos preditivos** e análises avançadas.

# Pipeline
# Pipeline: de_job_lakehouse_ambev

Este pipeline automatiza a ingestão, transformação e organização de dados no **Lakehouse da Ambev**, seguindo a arquitetura **Bronze → Silver → Gold**.

---

## Estrutura do Pipeline

**Nome do Job:** `de_job_lakehouse_ambev`

### Trigger
- **pause_status:** `UNPAUSED` — o job está ativo e pode ser executado automaticamente.
- **file_arrival:** Dispara quando arquivos chegam no diretório `/Volumes/rodrigo_lakehouse/bronze/brz_vol_ambev/`.

---

## Tasks (Etapas do Pipeline)

### Bronze
- **Objetivo:** Ingestão dos dados brutos no Lakehouse.
- **Notebook associado:** `/Workspace/DataTeam/1_bronze/landing_to_bronze`
- **Dependências:** Nenhuma (primeira etapa do pipeline)

### Silver
- **Objetivo:** Transformação e limpeza dos dados brutos, criando datasets prontos para análises intermediárias.
- **Notebook associado:** `/Workspace/DataTeam/2_silver/bronze_to_silver`
- **Dependências:** `bronze`
- **Warehouse usado:** `4c3eccbf842bbe66`

### Gold
- **Objetivo:** Transformação final e preparação de dados analíticos e dashboards.
- **Notebook associado:** `/Workspace/DataTeam/3_gold/silver_to_gold`
- **Dependências:** `silver`
- **Warehouse usado:** `4c3eccbf842bbe66`

---

## Configurações adicionais
- **Queue enabled:** `true` — o pipeline usa uma fila de execução para otimização de performance.
- **Performance target:** `STANDARD` — define a configuração de performance padrão para execução dos notebooks.

<img src="https://drive.google.com/uc?export=view&id=1ncVP3U61ddTjWHcmr2GgpK1WON2CrTIR" alt="Pipeline" width="900"/>


# Modelagem gold

## Star Schema
# Data Warehouse de Vendas - Delta Lake

<img src="https://drive.google.com/uc?export=view&id=1EKVSfJKXBDesr8aj7cCiDnK4lGd2tI0N" alt="Pipeline" width="900"/>

## Descrição do Projeto
Este projeto contém um **Data Warehouse dimensional** para análise de vendas, construído utilizando **Delta Lake**. O modelo inclui uma tabela fato (`fact_sales`) e quatro tabelas dimensão (`dim_calendar`, `dim_channels`, `dim_places`, `dim_products`). Ele permite análises de vendas por tempo, canal, local e produto, com suporte a tendências, sazonalidade e segmentações.

---

## Estrutura do Data Warehouse

### Tabelas Dimensão

#### 1. `dim_calendar`
- **skcalendar**: Chave primária (BIGINT, auto incremento)
- **date**: Data
- **day, month, year**: Componentes da data
- **insertdate**: Timestamp de inserção

#### 2. `dim_channels`
- **skchannel**: Chave primária
- **channel_id**: Identificador do canal
- **trade_chnl_desc, trade_group_desc, trade_type_desc**: Descrições do canal
- **start_date, end_date**: Validade do registro
- **current**: Indica se é registro atual
- **insertdate**: Timestamp de inserção

#### 3. `dim_places`
- **skplace**: Chave primária
- **place_id**: Identificador do local
- **Btlr_Org_LVL_C_Desc**: Descrição da organização/região
- **start_date, end_date**: Validade do registro
- **current**: Indica se é registro atual
- **insertdate**: Timestamp de inserção

#### 4. `dim_products`
- **skproduct**: Chave primária
- **product_id**: Identificador do produto
- **ce_brand_flvr, brand_nm**: Marca e sabor
- **pkg_cat, pkg_cat_desc, tsr_pckg_nm**: Categoria e embalagem
- **start_date, end_date**: Validade do registro
- **current**: Indica se é registro atual
- **insertdate**: Timestamp de inserção

---

### Tabela Fato

#### `fact_sales`
- **skcalendar**: FK para `dim_calendar`
- **sales_id**: Identificador da venda
- **skproduct**: FK para `dim_products`
- **skplace**: FK para `dim_places`
- **skchannel**: FK para `dim_channels`
- **volume**: Quantidade vendida
- **insertdate**: Timestamp de inserção

---

## Possíveis Análises e KPIs

- **Tendência de vendas**: diário, semanal, mensal, anual.
- **Sazonalidade e picos de demanda**.
- **Performance por canal**: total de vendas por `trade_group_desc`.
- **Performance por região/local**: total de vendas por `Btlr_Org_LVL_C_Desc`.
- **Performance por produto/marca/categoria**: análise de volume e participação.
- **Análises combinadas**: cruzar canal, produto e local para segmentação.
- **KPIs avançados**:
  - Crescimento ano a ano
  - Produtos mais vendidos
  - Identificação de outliers de vendas

