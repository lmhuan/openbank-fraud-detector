# 🗄️ Ingestion Schema Design

## 🎯 Mục tiêu
Lớp Ingestion chịu trách nhiệm kết nối dữ liệu từ nhiều nguồn (CoreBanking, ATM, E-Banking, SWIFT, CITAD, PC logs) và đưa vào Lakehouse.  
Thiết kế schema đảm bảo:
- Có **common metadata** để quản lý trace, lineage.  
- Có **schema chi tiết cho từng nguồn** để phục vụ phân tích và huấn luyện ML.  
- Partition dữ liệu theo `event_date` và `source_system` trong Lakehouse để dễ quản lý.

---

## 📚 Common Schema

| Field             | Type        | Mô tả |
|-------------------|-------------|-------|
| `event_id`        | STRING      | ID duy nhất cho sự kiện |
| `event_timestamp` | TIMESTAMP   | Thời điểm sự kiện |
| `ingest_timestamp`| TIMESTAMP   | Thời điểm ingestion |
| `source_system`   | STRING      | Hệ thống nguồn (CoreBanking, ATM, EBANK, SWIFT, CITAD, PC, ACCESS_LOG) |
| `raw_payload`     | JSON/BLOB   | Dữ liệu gốc |

---

## 📚 Schema chi tiết theo nguồn

### CoreBanking
- `transaction_id` (STRING)  
- `account_id` (STRING)  
- `amount` (DECIMAL)  
- `currency` (STRING)  
- `transaction_type` (STRING)  
- `branch_code` (STRING)  

### ATM
- `atm_id` (STRING)  
- `card_number` (STRING)  
- `txn_code` (STRING)  
- `status_code` (STRING)  

### E-Banking
- `user_id` (STRING)  
- `session_id` (STRING)  
- `device_id` (STRING)  
- `ip_address` (STRING)  
- `action_type` (STRING)  

### SWIFT
- `message_id` (STRING)  
- `bic_sender` (STRING)  
- `bic_receiver` (STRING)  
- `amount` (DECIMAL)  
- `currency` (STRING)  
- `country_code` (STRING)  

### CITAD (VN Domestic Clearing)
- `txn_id` (STRING)  
- `bank_sender` (STRING)  
- `bank_receiver` (STRING)  
- `amount` (DECIMAL)  
- `currency` (STRING)  

### PC / Workstation Logs
- `device_id` (STRING)  
- `user_id` (STRING)  
- `ip_address` (STRING)  
- `domain_accessed` (STRING)  
- `process_executed` (STRING)  

### CoreBanking Access Logs
- `access_id` (STRING)  
- `user_id` (STRING)  
- `user_role` (STRING)  
- `function_code` (STRING)  
- `function_name` (STRING)  
- `customer_id` (STRING)  
- `access_timestamp` (TIMESTAMP)  
- `ip_address` (STRING)  
- `device_id` (STRING)  
- `action_type` (STRING)  
- `session_id` (STRING)  
- `is_privileged_user` (BOOLEAN)  
- `remark` (STRING)  

---

## 🖼️ ERD (Entity Relationship Diagram)

```mermaid
erDiagram
    COMMON {
        string event_id
        datetime event_timestamp
        datetime ingest_timestamp
        string source_system
        json raw_payload
    }

    COREBANKING {
        string transaction_id
        string account_id
        decimal amount
        string currency
        string transaction_type
        string branch_code
    }

    ATM {
        string atm_id
        string card_number
        string txn_code
        string status_code
    }

    EBANKING {
        string user_id
        string session_id
        string device_id
        string ip_address
        string action_type
    }

    SWIFT {
        string message_id
        string bic_sender
        string bic_receiver
        decimal amount
        string currency
        string country_code
    }

    CITAD {
        string txn_id
        string bank_sender
        string bank_receiver
        decimal amount
        string currency
    }

    PCLOGS {
        string device_id
        string user_id
        string ip_address
        string domain_accessed
        string process_executed
    }

    CORE_ACCESS_LOGS {
        string access_id
        string user_id
        string user_role
        string function_code
        string function_name
        string customer_id
        datetime access_timestamp
        string ip_address
        string device_id
        string action_type
        string session_id
        boolean is_privileged_user
        string remark
    }

    COMMON ||--o{ COREBANKING : contains
    COMMON ||--o{ ATM : contains
    COMMON ||--o{ EBANKING : contains
    COMMON ||--o{ SWIFT : contains
    COMMON ||--o{ CITAD : contains
    COMMON ||--o{ PCLOGS : contains
    COMMON ||--o{ CORE_ACCESS_LOGS : contains



