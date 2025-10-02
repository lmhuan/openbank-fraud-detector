# 🏦 OpenBank Fraud Detector

An open-source project for **bank transaction fraud detection** in Vietnam.  
The goal is to build a unified **batch + realtime ML pipeline** for monitoring transactions across multiple channels (CoreBanking, ATM, E-Banking, SWIFT, CITAD, PC logs...).

---

## 🚀 Features

- 📥 **Data Ingestion** from multiple sources: CoreBanking, ATM, Internet Banking, SWIFT, CITAD, workstation logs.  
- 🗄️ **Lakehouse Architecture**: combining batch ETL (Airflow/Spark) and streaming (Kafka/Flink).  
- 📊 **Feature Store** (Feast/Redis) for both training and realtime scoring.  
- 🤖 **Machine Learning Models**: batch training and realtime scoring APIs.  
- 🛡️ **Risk Scoring Engine**: hybrid rules + explainable AI (SHAP/LIME).  
- 📈 **Dashboard & APIs**: visualization and integration with existing banking monitoring systems.  

---

## 📚 Documentation

- 📄 [Master Plan](docs/master_plan.md)  
- 📊 [Architecture](docs/architecture.md)  

---

## 📂 Project Structure


---

## 🛠️ Tech Stack

- **Data Processing**: Apache Spark, Apache Flink  
- **Streaming**: Apache Kafka  
- **Workflow Orchestration**: Apache Airflow  
- **Storage**: Data Lakehouse (Delta Lake / Iceberg)  
- **ML/AI**: Python (scikit-learn, PyTorch, TensorFlow), Java (optional)  
- **Dashboard**: Streamlit, Plotly Dash  
- **APIs**: REST/gRPC  

---

## 🤝 Contributing

We welcome contributions from the community:  
1. Fork the repository  
2. Create a new branch (`git checkout -b feature/my-feature`)  
3. Commit your changes (`git commit -m "Add my feature"`)  
4. Push the branch (`git push origin feature/my-feature`)  
5. Open a Pull Request  

---

## 📢 Announcement

The **OpenBank Fraud Detector** project was created to build a collaborative community of data engineers, ML practitioners, and banking experts in Vietnam to fight financial fraud.  

👉 If you’re interested, please **star this repo** ⭐, open an **issue**, or join development with us!  

---
