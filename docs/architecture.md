flowchart LR
    %% =========================
    %% 1️⃣ ON-PREM DATA SOURCES
    %% =========================
    subgraph OnPrem["🏢 On-Prem Data Sources"]
        CB[Core Banking]
        CITAD[CITAD]
        SWIFT[SWIFT]
        ATM[ATM / POS]
        EB[E-Banking / Mobile]
        CRM[CRM / HR / Risk]
        PC[Workstation Logs - IP / Domain / Actions]
    end

    %% =========================
    %% 2️⃣ INGESTION LAYER
    %% =========================
    subgraph Ingestion["☁️ Data Ingestion Layer"]
        ADF["Azure Data Factory (Self-hosted IR)"]
        KAFKA["Kafka / Event Hubs (Streaming)"]
        CDC["Debezium / GoldenGate (CDC)"]
    end

    %% =========================
    %% 3️⃣ LAKEHOUSE LAYER
    %% =========================
    subgraph Lakehouse["🪣 Lakehouse Layer (Delta Lake on ADLS Gen2)"]
        BRONZE["Bronze Zone (Raw)"]
        SILVER["Silver Zone (Cleaned)"]
        GOLD["Gold Zone (Curated / Aggregated)"]
    end

    %% =========================
    %% 4️⃣ ML & FEATURE LAYER
    %% =========================
    subgraph ML["🤖 ML / Feature Layer"]
        FEAST["Feature Store"]
        MLFLOW["MLflow Training / Registry"]
        SERVE["Model Serving / API"]
    end

    %% =========================
    %% 5️⃣ BI / ANALYTICS LAYER
    %% =========================
    subgraph BI["📊 BI / Analytics Layer"]
        SQL["Databricks SQL / Power BI (Direct Lake)"]
        ALERT["Alert Engine / SOC Dashboard"]
    end

    %% =========================
    %% DATA FLOWS
    %% =========================
    CB & CITAD & SWIFT --> ADF --> BRONZE
    CRM --> ADF --> BRONZE
    ATM & EB & PC --> KAFKA --> BRONZE
    CB --> CDC --> BRONZE

    BRONZE --> SILVER --> GOLD
    GOLD --> FEAST --> MLFLOW --> SERVE
    GOLD --> SQL
    SERVE --> ALERT
