# 🏗️ Unified System Architecture with Lakehouse Data Pipeline

Hệ thống Fraud Detection cho ngân hàng tại Việt Nam, kết hợp dữ liệu từ nhiều nguồn (CoreBanking, ATM, E-Banking, SWIFT, CITAD, PC logs) và xử lý theo mô hình Lakehouse.

```mermaid
flowchart LR
    subgraph Sources[Data Sources]
        CB[Core Banking]
        ATM[ATM]
        EB[E-Banking]
        SWIFT[SWIFT – quốc tế]
        CITAD[CITAD – liên ngân hàng VN]
        PC[PC/Workstation Logs<br/>(IP, Domain, Device)]
    end

    subgraph Ingestion[Ingestion Layer]
        ETL[Batch ETL<br/>(Airflow/Spark)]
        KAFKA[Streaming<br/>(Kafka/Flink)]
    end

    subgraph Lakehouse[Lakehouse]
        RAW[Raw Zone]
        PROC[Processed Zone]
    end

    FS[Feature Store<br/>(Feast/Redis)]

    subgraph Models[Model Layer]
        TRAIN[Offline Model Training<br/>(Batch ML)]
        SCORE[Realtime Scoring<br/>(API)]
    end

    RS[Risk Scoring Engine<br/>(Rules + SHAP/LIME)]

    subgraph Output[Output Layer]
        DASH[Dashboard<br/>(Streamlit/Dash)]
        API[API Layer<br/>(REST/gRPC)]
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
