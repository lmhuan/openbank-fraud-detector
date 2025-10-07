# 🏗️ Final Architecture — On-Prem / Hybrid 

Phiên bản kiến trúc này giữ **dữ liệu nhạy cảm trên hạ tầng trong nước (on-prem)**, loại bỏ Exadata và dùng **Delta Lake + Spark + Kafka + Feast + MLflow** làm nền tảng Lakehouse. Hỗ trợ **hybrid compute** (compute trên cloud chỉ khi có private link / policy cho phép) — nhưng **raw data không rời khỏi on-prem**.

---

```mermaid
flowchart TB
    %% Sources
    subgraph Sources["Data Sources (On-Prem)"]
        CB["CoreBanking"]
        ATM["ATM"]
        EB["E-Banking / Mobile"]
        SWIFT["SWIFT"]
        CITAD["CITAD (VN)"]
        PC["Workstation Logs"]
        ACCESS["Core Access Logs"]
    end

    %% Ingestion
    subgraph Ingestion["Ingestion Layer"]
        KAFKA["Kafka (On-Prem)"]
        BATCH["Batch Loader (Airflow + Spark)"]
        AUTOLOADER["Autoloader / Structured Streaming"]
    end

    %% Lakehouse zones
    subgraph Lakehouse["Delta Lakehouse (On-Prem)"]
        BRONZE["Bronze - Raw (Delta)"]
        SILVER["Silver - Cleaned (Delta)"]
        GOLD["Gold - Curated / Feature Ready (Delta)"]
    end

    %% Feature & ML
    subgraph Feature_ML["Feature & ML"]
        FEAT_PIPE["Feature Pipelines (Spark)"]
        FEAST["Feast Feature Store (On-Prem)"]
        MLFLOW["MLflow (On-Prem)"]
        MODEL_REG["Model Registry"]
        SERVE["Realtime Scoring / Serving"]
    end

    %% Governance & Infra
    subgraph Gov["Governance & Security"]
        RANGER["Apache Ranger (RBAC)"]
        ATLAS["Apache Atlas (Lineage)"]
        KMS["KMS / HSM (On-Prem)"]
        SIEM["SIEM / Audit Logs"]
    end

    %% Visualization & Alerts
    subgraph Ops["Visualization & Alerting"]
        DASH["Superset / PowerBI (On-Prem)"]
        ALERT["Alert Engine / SOC Integration"]
    end

    %% Connections
    CB & ATM & EB & SWIFT & CITAD & PC & ACCESS --> KAFKA
    CB & ATM & EB & SWIFT & CITAD & PC & ACCESS --> BATCH

    KAFKA --> AUTOLOADER
    BATCH --> BRONZE
    AUTOLOADER --> BRONZE

    BRONZE --> SILVER --> GOLD
    GOLD --> FEAT_PIPE --> FEAST
    FEAST --> MLFLOW
    MLFLOW --> MODEL_REG --> SERVE
    SERVE --> ALERT
    GOLD --> DASH

    %% Governance links (policy/control)
    Gov -.-> BRONZE
    Gov -.-> SILVER
    Gov -.-> GOLD
    Gov -.-> FEAST
    Gov -.-> MLFLOW
    Gov -.-> SERVE
