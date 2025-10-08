# 🏗️ System Architecture — OpenBank Fraud & Risk Detection

> **Phiên bản:** 3.0 (Hybrid — On-prem Data + Azure Databricks Compute)  
> **Ngày cập nhật:** 2025-10-08  
> **Mục tiêu:** Kiến trúc tổng thể cho hệ thống phát hiện gian lận & hành vi nội gián, đảm bảo dữ liệu ngân hàng Việt Nam không rời khỏi biên giới quốc gia, tuân thủ quy định NHNN.

---

## 🌐 Tổng quan kiến trúc 4 tầng

| Tầng | Vai trò chính | Thành phần |
|------|----------------|-------------|
| **1️⃣ Data Ingestion** | Thu thập dữ liệu từ nhiều nguồn on-prem | ADF, Kafka, CDC |
| **2️⃣ Lakehouse Layer** | Lưu trữ, xử lý dữ liệu chuẩn hóa | Delta Lake (ADLS Gen2) |
| **3️⃣ ML / Feature Layer** | Sinh feature, huấn luyện mô hình | Databricks Feature Store, MLflow |
| **4️⃣ BI / Analytics Layer** | Trực quan hóa, cảnh báo | Power BI, Superset, Databricks SQL |

---

## 🧱 1️⃣ Data Ingestion Layer (On-Prem Sources)

### **Nguồn dữ liệu on-prem cần ingestion**

| Nhóm hệ thống | Nguồn dữ liệu | Loại dữ liệu | Ghi chú |
|----------------|----------------|---------------|----------|
| **Core Banking** | Sổ cái, giao dịch, thông tin khách hàng, logs truy cập | Batch / CDC | Dữ liệu nhạy cảm, cần mã hóa khi truyền |
| **CITAD (Thanh toán liên ngân hàng)** | Điện thanh toán, trạng thái giao dịch | Batch / Streaming | Phục vụ đối soát & phát hiện gian lận nội địa |
| **SWIFT (Thanh toán quốc tế)** | MT103, MT202, trạng thái message | Batch | Tuân thủ SWIFT CSP |
| **ATM / POS** | Giao dịch, lỗi giao dịch, thời gian thực | Streaming | Cần real-time fraud detection |
| **E-Banking / Mobile Banking** | Login, session, transaction logs | Streaming | Azure Event Hubs hoặc Kafka connector |
| **CRM / HR / Risk** | Dữ liệu nhân sự, KPI, hạn mức, rủi ro | Batch | Hỗ trợ feature cho ML |
| **Core Access Logs / Workstation Logs** | UserID, IP, Domain, thao tác, timestamp | Batch / Streaming | Phát hiện hành vi nội gián |

---

### **Cơ chế ingestion**

| Mục tiêu | Công cụ | Mô tả triển khai |
|-----------|----------|------------------|
| Batch ingestion từ on-prem | **Azure Data Factory (Self-hosted IR)** | Gateway tại datacenter, đồng bộ dữ liệu lên ADLS |
| Streaming ingestion | **Kafka / Azure Event Hubs** | Xử lý real-time logs từ ATM, eBanking, PC logs |
| CDC từ Core Banking | **Debezium / Oracle GoldenGate** | Ghi nhận thay đổi giao dịch tức thời |
| Validation | **Great Expectations / DLT Expectations** | Kiểm tra schema & chất lượng dữ liệu |
| Staging | **On-prem blob / DB** | Tạm lưu dữ liệu trước khi upload lên Lakehouse |

---

### **Luồng dữ liệu minh họa**

flowchart LR
    subgraph OnPrem["🏢 On-Prem Data Sources"]
        CB[Core Banking]
        CITAD[CITAD]
        SWIFT[SWIFT]
        ATM[ATM / POS]
        EB[E-Banking / Mobile]
        CRM[CRM / HR / Risk]
        PC[Workstation Logs - IP / Domain / Actions]
    end



    subgraph Ingestion["☁️ Data Ingestion Layer"]
        ADF["Azure Data Factory (Self-hosted IR)"]
        KAFKA["Kafka / Event Hubs (Streaming)"]
        CDC["Debezium / GoldenGate (CDC)"]
    end

    subgraph Lakehouse["🪣 Lakehouse Layer (ADLS Gen2 + Delta)"]
        BR["Bronze Zone"]
        SI["Silver Zone"]
        GO["Gold Zone"]
    end

    CB & CITAD & SWIFT --> ADF --> BR
    CRM --> ADF --> BR
    ATM & EB & PC --> KAFKA --> BR
    CB --> CDC --> BR
    BR --> SI --> GO

