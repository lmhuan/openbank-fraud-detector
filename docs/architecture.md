# 🏗️ System Architecture — OpenBank Fraud & Risk Detection

> **Phiên bản:** 2.0 (Azure Databricks — On-Prem Data Residency Compliant)  
> **Ngày cập nhật:** 2025-10-08  
> **Mục tiêu:** Xây dựng kiến trúc tổng thể cho hệ thống phát hiện gian lận & hành vi bất thường trong ngân hàng Việt Nam, đáp ứng yêu cầu bảo mật và quy định của NHNN.

---
 
## 🌐 Tổng quan kiến trúc 4 tầng

Hệ thống được chia thành 4 tầng logic:

| Tầng | Vai trò chính | Thành phần chính |
|------|----------------|------------------|
| **1️⃣ Data Ingestion** | Thu thập dữ liệu từ tất cả nguồn nghiệp vụ | Kafka, Azure Data Factory, Airflow, Flink |
| **2️⃣ Lakehouse Layer** | Lưu trữ & xử lý dữ liệu chuẩn hóa | Delta Lake (ADLS Gen2), Databricks Runtime |
| **3️⃣ ML / Feature Layer** | Sinh feature, huấn luyện, triển khai mô hình | Databricks Feature Store, MLflow |
| **4️⃣ BI / Analytics Layer** | Trực quan hóa, cảnh báo, giám sát | Databricks SQL, Power BI, Alert Engine |

---

## 🧩 Kiến trúc chi tiết (Mermaid Diagram)

```mermaid
flowchart TB
    subgraph Ingestion["1️⃣ Data Ingestion Layer"]
        KAFKA["Event Hubs / Kafka (Streaming)"]
        ADF["Azure Data Factory / Airflow (Batch)"]
        CDC["Debezium / GoldenGate (CDC)"]
    end

    subgraph Lakehouse["2️⃣ Lakehouse Layer (Delta Lake on ADLS Gen2)"]
        BRONZE["Bronze Zone (Raw)"]
        SILVER["Silver Zone (Cleaned)"]
        GOLD["Gold Zone (Curated / Aggregated)"]
    end

    subgraph ML["3️⃣ ML & Feature Layer"]
        FEAST["Feature Store (Databricks Feature Store)"]
        MLFLOW["MLflow (Training + Registry)"]
        SERVE["Model Serving / Azure Function"]
    end

    subgraph BI["4️⃣ BI & Analytics Layer"]
        SQL["Databricks SQL / Power BI (Direct Lake)"]
        ALERT["Alert Engine / SOC Dashboard"]
    end

    subgraph Security["Governance & Security"]
        UC["Unity Catalog (Data Governance)"]
        KV["Azure Key Vault (CMK)"]
        MON["Azure Monitor / Log Analytics"]
    end

    KAFKA & ADF & CDC --> BRONZE --> SILVER --> GOLD
    GOLD --> FEAST --> MLFLOW --> SERVE
    GOLD --> SQL
    SERVE --> ALERT
    Security -.-> Lakehouse
    Security -.-> ML
    Security -.-> BI
