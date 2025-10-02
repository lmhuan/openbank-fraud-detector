# OpenBank Fraud Detector (VN)

## 🎯 Tiếng Việt

### Mục tiêu
Dự án mã nguồn mở giúp phát hiện sớm gian lận trong cơ chế **Maker–Checker** tại ngân hàng Việt Nam.  
Hệ thống tính toán **Risk Score** cho cặp Maker–Checker và gửi cảnh báo đến bộ phận QLRR.

### Kiến trúc
- **Data pipeline**: ingestion (login, session, transaction, approval), batch + realtime.
- **Feature store**: hành vi cá nhân, quan hệ Maker–Checker, đặc trưng giao dịch.
- **Models**:
  - Baseline: Logistic Regression, XGBoost
  - Advanced: Autoencoder, Isolation Forest
  - Future: Graph Neural Networks (GNN), LSTM/GRU
- **Output**: Risk Score + giải thích (SHAP/LIME) + dashboard cảnh báo.

---

## 🌍 English

### Objective
Open-source project to detect early **collusion/fraud** in the **Maker–Checker** mechanism in Vietnamese banks.  
The system computes a **Risk Score** for Maker–Checker pairs and sends alerts to risk managers.

### Architecture
- **Data pipeline**: ingest login/session/transaction/approval events.
- **Feature store**: user behavior, Maker–Checker relationships, transaction features.
- **Models**:
  - Baseline: Logistic Regression, XGBoost
  - Advanced: Autoencoder, Isolation Forest
  - Future: Graph Neural Networks (GNN), LSTM/GRU
- **Output**: Risk Score + explainability (SHAP/LIME) + dashboard.

## 📄 Master Plan
  
👉 [docs/master_plan.md](docs/master_plan.md)


## 📊 Architecture

  
👉 [docs/system_architecture.docx](docs/system_architecture.docx)

 [docs/architecture.md](docs/architecture.md).
