# 📘 Business Requirements — Ingestion Layer

## 🎯 Mục tiêu tổng thể

Thiết kế và triển khai lớp **Ingestion Layer** cho hệ thống **OpenBank Fraud & Risk Detection**, với các mục tiêu:

1. Thu thập, chuẩn hóa và lưu trữ dữ liệu từ tất cả các hệ thống giao dịch ngân hàng và nguồn nội bộ.  
2. Cung cấp dữ liệu tin cậy, đồng nhất cho các pipeline ETL, feature engineering, và mô hình Machine Learning.  
3. Hỗ trợ **phát hiện gian lận giao dịch** (Fraud Detection) và **rủi ro nội bộ** (Insider Behavior Monitoring).  

---

## 🧩 1. Nguồn dữ liệu đầu vào

| Nguồn | Mô tả | Mục đích |
|-------|--------|----------|
| **CoreBanking** | Dữ liệu giao dịch gốc (tài khoản, chuyển khoản, rút tiền, thu chi) | Phân tích gian lận tài chính, bất thường trong giao dịch |
| **ATM Logs** | Giao dịch rút tiền, lỗi thẻ, PIN, vị trí ATM | Phát hiện pattern rút tiền bất thường, clone thẻ |
| **E-Banking** | Giao dịch internet/mobile banking, IP, device | Nhận diện hành vi truy cập bất thường, đa thiết bị |
| **SWIFT** | Thanh toán quốc tế | Phát hiện gian lận xuyên biên giới |
| **CITAD** | Chuyển khoản nội địa liên ngân hàng (Việt Nam) | Theo dõi thanh toán nội địa bất thường |
| **PC / Workstation Logs** | Log hệ thống máy tính nội bộ (IP, domain, process, user) | Theo dõi truy cập trái phép hoặc bất thường từ thiết bị nội bộ |
| **CoreBanking Access Logs** | Lưu vết người dùng thao tác trong hệ thống nghiệp vụ | Phục vụ Insider Behavior Monitoring (nhân viên nội bộ) |

---

## ⚙️ 2. Yêu cầu kỹ thuật ingestion

| Nhóm | Yêu cầu |
|------|----------|
| **Kết nối nguồn** | Hỗ trợ batch (ETL) và real-time (Kafka / CDC). |
| **Chuẩn hóa dữ liệu** | Convert timestamp → UTC, định dạng số tiền, mask dữ liệu nhạy cảm. |
| **Chất lượng dữ liệu (DQ)** | Validate schema, checksum file, reject lỗi. |
| **Lưu trữ** | Lakehouse (parquet, delta hoặc iceberg), chia zone: raw → processed → curated. |
| **Logging & Monitoring** | Mọi batch/stream đều sinh ingestion_log (status, record_count, checksum). |
| **Metadata & Lineage** | Tích hợp Data Catalog (Glue / DataHub) theo `source_system`, `event_date`. |
| **Retry & Replay** | Hỗ trợ ingest lại file/topic lỗi mà không ghi đè dữ liệu hợp lệ. |

---

## 🧠 3. Business Requirement chi tiết

### 3.1 Fraud Transaction Monitoring

Phát hiện gian lận trong giao dịch tiền tệ:

- So sánh mô hình hành vi giao dịch của khách hàng (transaction profiling).  
- Phát hiện rút tiền/ chuyển khoản bất thường (theo thời gian, địa điểm, thiết bị).  
- Gộp dữ liệu từ CoreBanking, ATM, EBanking, CITAD, SWIFT.

**Feature gợi ý:**
- `txn_frequency`, `avg_txn_amount`, `txn_location_variance`, `is_new_device`, `cross_channel_activity`.

---

### 3.2 Real-Time Transaction Streaming

Một số kênh (E-Banking, ATM, CITAD) yêu cầu realtime detection:

- Dữ liệu stream vào Kafka topic per channel.  
- Consumer (Spark/Flink) ingest về Lakehouse (bronze zone).  
- Đồng thời publish dữ liệu tới real-time model endpoint.

**Yêu cầu hiệu năng:**
- Độ trễ < 2 giây.  
- Đảm bảo exactly-once ingestion semantics.  

---

### 3.3 Insider Behavior Monitoring

Phát hiện nhân viên có hành vi **xem, tải, xuất dữ liệu khách hàng trái phép**, đặc biệt trước khi nghỉ việc.

**Nguồn dữ liệu chính:** `CORE_ACCESS_LOGS` (ghi lại toàn bộ thao tác người dùng).  
**Mục tiêu:** Xây dựng feature hành vi nhân viên (per user/day).  

| Feature | Mô tả |
|----------|-------|
| `daily_access_count` | Số lần truy cập/ngày |
| `after_hours_access_ratio` | Truy cập ngoài giờ hành chính |
| `sensitive_function_access_count` | Truy cập chức năng chứa thông tin khách hàng |
| `unique_customer_count` | Số khách hàng khác nhau được xem |
| `export_action_ratio` | Tỷ lệ thao tác EXPORT |
| `distinct_ip_count` | Số IP hoặc thiết bị khác nhau |
| `resignation_flag` | Có cờ nghỉ việc hoặc chuẩn bị nghỉ |

**Output:**  
Feature group `employee_behavior_features` trong Feature Store, dùng cho ML model **Insider Anomaly Detection**.

---

### 3.4 Workstation / PC Activity Correlation

Kết hợp log hệ thống máy trạm nội bộ:

- IP, domain, process, thiết bị → tương quan với hành vi truy cập hệ thống.  
- Dò các hành vi như: tải dữ liệu ra USB, upload qua web, mở file báo cáo ngoài giờ.

**Feature gợi ý:**
- `file_export_count`, `unusual_process_run`, `external_domain_accessed`, `after_hours_login`.

---

## 🧩 4. Output / Downstream Integration

| Thành phần | Vai trò |
|-------------|----------|
| **Lakehouse Raw Zone** | Lưu dữ liệu gốc (immutable). |
| **Processed Zone** | Dữ liệu chuẩn hóa, làm sạch. |
| **Feature Store** | Tập trung các feature fraud/insider cho mô hình ML. |
| **Model Serving Layer** | Sử dụng feature store để huấn luyện, infer, cảnh báo. |
| **Dashboard & Alerting** | Theo dõi ingestion status, dữ liệu, cảnh báo bất thường. |

---

## 🔒 5. Bảo mật & tuân thủ

| Hạng mục | Yêu cầu |
|-----------|----------|
| **Dữ liệu nhạy cảm** | Mask/scramble số tài khoản, CMND, số thẻ. |
| **Kiểm soát truy cập** | RBAC theo nhóm (Data Engineer, Data Scientist, Auditor). |
| **Audit trail** | Log toàn bộ hành vi ingest, access, download. |
| **Tuân thủ** | Phù hợp chuẩn NHNN, GDPR (nếu dữ liệu xuyên biên giới). |

---

## 🚀 6. Mở rộng & Hiệu năng

- Hỗ trợ **horizontal scaling** qua Spark cluster / Kafka partitions.  
- Cho phép ingest dữ liệu từ **nguồn mới** (Trade Finance, Treasury, CRM...).  
- Mọi bảng ingestion cần có **schema versioning** và **data contract**.  

---

## 🧱 7. Feature Flow Diagram

```mermaid
flowchart LR
    subgraph Ingestion
        A[CORE_ACCESS_LOGS]
        B[Ingestion Layer]
    end

    subgraph Storage
        C[Lakehouse - Raw Zone]
    end

    subgraph Feature_Engineering
        D[Daily Aggregation Job (Airflow_Spark)]
        E[Feature Store - employee_behavior_features]
    end

    subgraph ML_and_Monitoring
        F[ML Model - Insider Anomaly Detection]
        G[Alert Engine / Dashboard]
    end

    A --> B --> C --> D --> E --> F --> G
