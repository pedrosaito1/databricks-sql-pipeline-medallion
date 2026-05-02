# 🏭 Pipeline Medallion com SQL e Databricks

Pipeline de dados end-to-end implementando a **Arquitetura Medallion** (Bronze → Silver → Gold) com **SQL**, **Delta Lake** e **Databricks**, simulando um cenário industrial de monitoramento de produção.

---

## 📐 Arquitetura

```
Fonte de Dados (SCADA Industrial)
        │
        ▼
┌───────────────┐
│    BRONZE     │  Dados brutos, sem transformação
│  (Raw Ingest) │  Formato: Delta Lake
└───────┬───────┘
        │
        ▼
┌───────────────┐
│    SILVER     │  Dados limpos e padronizados
│  (Cleansed)   │  Remoção de nulos, tipagem, validação
└───────┬───────┘
        │
        ▼
┌───────────────┐
│     GOLD      │  Dados agregados para consumo analítico
│  (Analytical) │  KPIs industriais e métricas de OEE
└───────────────┘
```

---

## 🛠️ Stack

| Tecnologia | Uso |
|---|---|
| Databricks SQL | Engine de processamento e orquestração |
| Delta Lake | Formato de tabela transacional (ACID) |
| SQL | Linguagem principal de transformação |
| Databricks Visualizations | Gráficos analíticos nativos |

---

## 📁 Estrutura do Projeto

```
databricks-sql-pipeline-medallion/
├── 01_bronze.ipynb       # Ingestão de dados brutos
├── 02_silver.ipynb       # Limpeza e padronização
├── 03_gold.ipynb         # Agregações e KPIs
└── 04_analysis.ipynb      # Análises e visualizações
```

---

## 📊 Dados Simulados

O projeto utiliza dados sintéticos de uma **linha de produção industrial** (sistema SCADA):

| Campo | Descrição |
|---|---|
| `equipment_id` | ID do equipamento |
| `line_id` | Linha de produção (LINE-A, LINE-B, LINE-C) |
| `shift` | Turno (A/B/C) |
| `event_timestamp` | Data/hora da leitura |
| `production_qty` | Quantidade produzida |
| `downtime_minutes` | Tempo de parada em minutos |
| `defect_qty` | Quantidade de defeitos |
| `source_system` | Sistema de origem (SCADA) |

Os dados incluem **anomalias intencionais** para demonstrar o tratamento de qualidade na camada Silver:
- Registros com `equipment_id` nulo
- Registros com `production_qty` nulo
- Registros com `downtime_minutes` negativo (valor inválido)

---

## 🔄 Camadas do Pipeline

### Bronze — Raw Ingest
- Ingere dados brutos sem nenhuma transformação
- Adiciona metadado de ingestão (`ingested_at`)
- Preserva todos os registros, inclusive os com anomalias

### Silver — Cleansed
- Remove registros com campos obrigatórios nulos
- Aplica casting de tipos (`STRING` → `INT`, `TIMESTAMP`)
- Filtra valores inválidos (`downtime_minutes < 0`)
- Padroniza campos categóricos (uppercase, trim)

**Resultado:** de 15 registros na Bronze, 12 chegam à Silver (3 removidos por qualidade)

### Gold — Analytical
Agrega os dados e calcula KPIs industriais por `line_id`, `shift` e `equipment_id`:

| Métrica | Fórmula |
|---|---|
| `availability_pct` | `(tempo_turno - downtime) / tempo_turno × 100` |
| `quality_pct` | `(produção - defeitos) / produção × 100` |
| `oee_pct` | `availability × quality × 100` |
| `defect_rate_pct` | `defeitos / produção × 100` |

> OEE (Overall Equipment Effectiveness) é o principal indicador de eficiência em ambientes de manufatura.

---

## 📈 Análises Disponíveis

O notebook `04_analysis.ipynb` contém 4 análises com visualizações:

- **Resumo do pipeline** — contagem de registros por camada
- **OEE por equipamento** — ranking de eficiência com gráfico de barras
- **Taxa de defeitos por turno** — identifica o turno mais crítico
- **Performance por linha de produção** — comparativo entre LINE-A, LINE-B e LINE-C

---

## 🚀 Como Executar

### Pré-requisitos
- Conta no [Databricks](https://databricks.com) (Community Edition gratuita)
- SQL Warehouse ativo

### Passo a passo

**1.** Clone ou faça download dos arquivos do repositório

**2.** No Databricks, crie uma pasta para o projeto em **Workspace**

**3.** Importe cada arquivo `.ipynb` como notebook:
- Workspace → pasta do projeto → **"Import"** → selecione o arquivo

**4.** Execute na ordem:
```
01_bronze.ipynb  →  02_silver.ipynb  →  03_gold.ipynb  →  04_analysis.ipynb
```

**5.** Em cada notebook clique em **"Run all"**

---

## 🔄 Contexto Real

Este projeto simula o padrão de arquitetura utilizado em ambientes de **manufatura digital e indústria 4.0**, onde dados de equipamentos são ingeridos continuamente via sistemas SCADA, tratados em camadas e disponibilizados para dashboards analíticos (Power BI, Grafana) e decisões operacionais em tempo real.

A arquitetura Medallion com Delta Lake é o padrão adotado por empresas como Databricks, AWS (Lake Formation) e Azure (Data Lake Storage) para construção de plataformas de dados escaláveis.

---

## 📬 Contato

**Pedro Saito** — Engenheiro de Dados  
[linkedin.com/in/pedrosaito1](https://linkedin.com/in/pedrosaito1)  
[github.com/pedrosaito1](https://github.com/pedrosaito1)
