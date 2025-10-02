# 🗄️ Ingestion Data Model (ERD)

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

    COMMON ||--o{ COREBANKING : contains
    COMMON ||--o{ ATM : contains
    COMMON ||--o{ EBANKING : contains
    COMMON ||--o{ SWIFT : contains
    COMMON ||--o{ CITAD : contains
    COMMON ||--o{ PCLOGS : contains
