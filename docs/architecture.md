# 🏗️ Unified System Architecture with Lakehouse Data Pipeline

Hệ thống Fraud Detection cho ngân hàng tại Việt Nam, kết hợp dữ liệu từ nhiều nguồn (CoreBanking, ATM, E-Banking, SWIFT, CITAD, PC logs) và xử lý theo mô hình Lakehouse.

```mermaid
flowchart LR
    subgraph Sources[Data Sources]
        CB[Core Banking]
        ATM[ATM]
        EB[E-Banking]
        SWIFT[SWIFT - quoc te]
        CITAD[CITAD - lien ngan hang VN]
        PC[Workstation Logs - IP Domain Device]
    end

    subgraph Ingestion[Ingestion Layer]
        ETL[Batch ETL - Airflow Spark]
        KAFKA[Streaming - Kafka Flink]
    end

    subgraph Lakehouse[Lakehouse]
        RAW[Raw Zone]
        PROC[Processed Zone]
    end

    FS[Feature Store - Feast Redis]

    subgraph Models[Model Layer]
        TRAIN[Offline Model Training - Batch ML]
        SCORE[Realtime Scoring - API]
    end

    RS[Risk Scoring Engine - Rules SHAP LIME]

    subgraph Output[Output Layer]
        DASH[Dashboard - Streamlit Dash]
        API[API Layer - REST gRPC]
    end

    %% Connections
    CB --> ETL
    ATM --> ETL
    EB --> ETL
    SWIFT --> ETL
    CITAD --> ETL
    PC --> ETL

    CB --> KAFKA
    ATM --> KAFKA
    EB --> KAFKA
    SWIFT --> KAFKA
    CITAD --> KAFKA
    PC --> KAFKA

    ETL --> RAW
    KAFKA --> RAW
    RAW --> PROC
    PROC --> FS

    FS --> TRAIN
    FS --> SCORE
    TRAIN --> SCORE

    SCORE --> RS
    RS --> DASH
    RS --> API
