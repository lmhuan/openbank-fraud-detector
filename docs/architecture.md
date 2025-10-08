# 🏗️ System Architecture — OpenBank Fraud & Risk Detection

> **Phiên bản:** 3.2 (Hybrid — On-prem Data + Azure Databricks Compute)  
> **Cập nhật:** 2025-10-08  
> **Mục tiêu:** Mô tả kiến trúc tổng thể cho hệ thống phát hiện gian lận & hành vi nội gián trong ngân hàng Việt Nam, đảm bảo dữ liệu không rời khỏi lãnh thổ, tuân thủ quy định NHNN.

---

## 🌐 Tổng quan kiến trúc 4 tầng

| Tầng | Vai trò chính | Thành phần |
|------|----------------|-------------|
| **1️⃣ Data Ingestion** | Thu thập dữ liệu từ nguồn on-prem | ADF, Kafka, CDC |
| **2️⃣ Lakehouse Layer** | Lưu trữ, chuẩn hóa, tổng hợp dữ liệu | Delta Lake (ADLS Gen2) |
| **3️⃣ ML / Feature Layer** | Sinh feature, huấn luyện và triển khai model | Databricks Feature Store, MLflow |
| **4️⃣ BI / Analytics Layer** | Trực quan hóa, cảnh báo, báo cáo | Power BI, Superset, Databricks SQL |

---

## 🧱 1️⃣ Data Ingestion Layer (On-Prem Sources)

### **Nguồn dữ liệu on-prem cần ingestion**

| Nhóm hệ thống | Nguồn dữ liệu | Loại dữ liệu | Ghi chú |
|----------------|----------------|---------------|----------|
| **Core Banking** | Sổ cái, giao dịch, logs truy cập | Batch / CDC | Dữ liệu nhạy cảm, mã hóa khi truyền |
| **CITAD (Thanh toán liên NH)** | Điện thanh toán, trạng thái | Batch / Streaming | Đối soát giao dịch nội địa |
| **SWIFT (Thanh toán quốc tế)** | MT103, MT202, logs | Batch | Tuân thủ SWIFT CSP |
| **ATM / POS** | Giao dịch, lỗi | Streaming | Real-time fraud detection |
| **E-Banking / Mobile Banking** | Login, session, transaction logs | Streaming | Dùng Event Hubs / Kafka |
| **CRM / HR / Risk** | Dữ liệu nhân sự, KPI, hạn mức, risk score | Batch | Hỗ trợ feature cho ML |
| **Core Access Logs / Workstation Logs** | UserID, IP, Domain, thao tác | Batch / Streaming | Phát hiện hành vi nội gián |

---

### **Cơ chế ingestion**

| Mục tiêu | Công cụ | Mô tả |
|-----------|----------|-------|
| Batch ingestion từ on-prem | **Azure Data Factory (Self-hosted IR)** | Gateway on-prem đồng bộ dữ liệu lên ADLS |
| Streaming ingestion | **Kafka / Event Hubs** | Log ATM, eBanking, workstation theo thời gian thực |
| CDC (change data capture) | **Debezium / GoldenGate** | Theo dõi thay đổi dữ liệu CoreBanking |
| Validation & kiểm thử dữ liệu | **Great Expectations / DLT Expectations** | Kiểm tra schema & chất lượng dữ liệu |
| Staging | **On-prem blob / DB** | Lưu tạm trước khi chuyển dữ liệu lên Lakehouse |

---

## 🧩 Sơ đồ kiến trúc tổng thể (Data Pipeline)

```mermaid
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
