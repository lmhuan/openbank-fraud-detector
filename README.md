# 💳 OpenBank Fraud & Risk Detection

> **An open-source project** for developing a modern, AI-powered fraud and insider behavior detection system for Vietnamese banks — built on a **Hybrid Azure Databricks Lakehouse architecture**, fully compliant with **State Bank of Vietnam (NHNN)** data regulations.

---

## 🚀 Overview

OpenBank Fraud & Risk Detection enables financial institutions to:

- Integrate data from multiple banking systems (CoreBanking, CITAD, SWIFT, ATM, eBanking, CRM, Logs, etc.)  
- Detect **fraudulent transactions** and **insider misuse behaviors** using AI/ML models.  
- Operate securely on a **hybrid Azure Databricks** platform where all data remains **within Vietnam’s borders**.  
- Build a unified **Lakehouse foundation** for ML, analytics, and regulatory compliance.

---

## 🏗️ System Architecture

The system is organized into **four logical layers**:

| Layer | Description |
|-------|--------------|
| **1️⃣ Data Ingestion** | Collects data from on-prem systems (CoreBanking, CITAD, SWIFT, ATM, eBanking, Logs...) |
| **2️⃣ Lakehouse Layer** | Stores, cleanses, and aggregates data using Delta Lake (ADLS Gen2) |
| **3️⃣ ML / Feature Layer** | Generates features, trains, and serves AI/ML models |
| **4️⃣ BI / Analytics Layer** | Provides dashboards, alerts, and real-time risk monitoring |

📘 **Detailed architecture:** [System Architecture](./docs/architecture.md)

---

## ☁️ Azure Deployment

The platform is deployed on **Azure Databricks (Private VNet)** with data storage in **ADLS Gen2 (Vietnam region)**.  
Integration uses **Azure Data Factory (Self-hosted IR)**, **Event Hubs / Kafka**, and **Unity Catalog** for secure data governance.

📘 **Deployment guide:** [Azure Deployment Guide](./docs/azure_deployment.md)

---

## 🧠 Machine Learning & AI

- Model training and tracking using **MLflow**  
- Feature management with **Databricks Feature Store**  
- Real-time inference via **Databricks Model Serving / Azure Function**  
- Key use cases:
  - 🕵️ Fraud Detection (transactions, ATM, eBanking)  
  - 👨‍💻 Insider Behavior Monitoring (employee access logs)  
  - 💰 Risk Scoring and Customer Segmentation  

---

## 🧩 Implementation Roadmap

The master plan outlines the end-to-end development journey:

1. Architecture & Infrastructure design  
2. Ingestion & Lakehouse pipeline implementation  
3. ML/Feature development  
4. BI & Alert integration  
5. Testing, validation, and NHNN compliance audit  

📘 **See the plan:** 

---

## 🗂️ Repository Structure

---

## 🧰 Contributing

We welcome community contributions to improve the system, add connectors, and enhance ML use cases.

### 🔹 How to Contribute
1. Fork this repository  
2. Create a new branch: `git checkout -b feature/your-feature`  
3. Commit your changes: `git commit -m "Add new feature"`  
4. Push the branch: `git push origin feature/your-feature`  
5. Open a Pull Request 🎉  

---

## 📜 License

This project is released under the **MIT License**, allowing free use and modification for research, testing, and production deployment under compliance constraints.

---

## 👥 Maintainers & Contact

**OpenBank Vietnam Initiative (Community Project)**  
📧 Contact: [mr.huan@gmail.com](mailto:mr.huan@gmail.com)

---

> 💡 “AI-driven fraud detection for secure, transparent, and compliant banking in Vietnam.”
