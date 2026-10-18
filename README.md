# 📊 Sales Data Engineering & BI Pipeline

Este projeto consiste num pipeline completo de **Engenharia de Dados e Business Intelligence** para processamento, transformação e visualização de dados de vendas operacionais. 

A arquitetura abrange desde a ingestão de dados em ficheiros Excel, tratamento e modelagem dimensional (*Star Schema*) via **Pentaho Data Integration (PDI)**, armazenamento num banco de dados relacional **PostgreSQL** containerizado via **Docker**, até à construção de relatórios e dashboards analíticos no **Power BI Desktop**.

---

## 🛠️ Tecnologias Utilizadas

### **Engenharia & ETL**
* **[Pentaho Data Integration (PDI / Kettle)](https://www.hitachivantara.com/):** Ferramenta de ETL responsável pela extração, limpeza, conversão de tipos, tratamento de valores nulos, cálculo de métricas (SLA, lucro, sazonalidade) e carga no Data Warehouse.
* **[Python](https://www.python.org/):** Utilizado na geração programática e simulação do conjunto de dados analíticos (`openpyxl`).

### **Armazenamento & Infraestrutura**
* **[PostgreSQL 15](https://www.postgresql.org/):** Banco de dados relacional para alojar o Data Warehouse (Tabelas Fato e Dimensões).
* **[Docker & Docker Compose](https://www.docker.com/):** Containerização do PostgreSQL, garantindo ambiente isolado, portabilidade e rápida execução.

### **Business Intelligence & Visualização**
* **[Power BI Desktop](https://powerbi.microsoft.com/):** Modelagem multidimensional, criação de medidas em **DAX** (Faturamento, Lucro, Margem %, Ticket Médio) e construção de dashboards interativos com visualizações geográficas.

---

## 🏗️ Arquitetura do Data Warehouse (Star Schema)

O modelo dimensional foi desenhado segundo as melhores práticas de Data Warehousing para garantir performance em consultas analíticas:

* **`fato_vendas` (Tabela Fato):** Armazena as métricas transacionais (`quantidade`, `valor_total`, `custo_total`, `lucro_bruto`, `desconto_aplicado`, `tempo_entrega_dias`, `avaliacao_cliente`) e as chaves estrangeiras.
* **`dim_cliente` (Dimensão):** Atributos do cliente (`nome_cliente`, `tipo_cliente` - B2B/B2C).
* **`dim_produto` (Dimensão):** Detalhes do catálogo (`produto`, `categoria`, `subcategoria`, `preco_unitario`, `custo_unitario`).
* **`dim_vendedor` (Dimensão):** Dados comerciais e localização (`vendedor`, `regiao`, `estado` - UF).

---

## 🔄 Fluxo de Processamento (ETL no Pentaho)

```text
[ Ficheiro Excel (.xlsx) ] 
         │
         ▼
[ Microsoft Excel Input ] ──► (Leitura da aba stg_vendas)
         │
         ▼
[ Select Values ] ──────────► (Tipagem de dados e padronização)
         │
         ▼
[ If field value is null ] ─► (Substituição de Nulos em SLA/Avaliação por 0)
         │
         ▼
[ Calculator ] ─────────────► (Extração de Ano e Mês da Data de Venda)
         │
         ▼
[ Dimension Lookup/Update ] ─► (Processamento encadeado das SKs: Cliente ➔ Produto ➔ Vendedor)
         │
         ▼
[ Table Output ] ───────────► (Carga dos registros na tabela fato_vendas no PostgreSQL)
