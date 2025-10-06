flowchart LR
    A[CORE_ACCESS_LOGS] --> B[Ingestion Layer]
    B --> C[Lakehouse - Raw Zone]
    C --> D[Daily Aggregation Job (Airflow/Spark)]
    D --> E[Feature Store - employee_behavior_features]
    E --> F[ML Model - Insider Anomaly Detection]
    F --> G[Alert Engine / Dashboard]
