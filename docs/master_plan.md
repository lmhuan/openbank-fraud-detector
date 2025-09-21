# 🏗️ Master Plan – OpenBank Fraud Detector (VN)

## 1. 🎯 Mục tiêu dự án
- Xây dựng hệ thống AI/ML giúp phát hiện sớm gian lận trong cơ chế **Maker–Checker** của ngân hàng.  
- Hỗ trợ **bộ phận quản lý rủi ro (QLRR)** bằng cách tính toán **Risk Score** và phát cảnh báo kịp thời.  
- Tạo một dự án **mã nguồn mở tại Việt Nam**, minh bạch, cộng đồng cùng phát triển.  

## 2. 📌 Phạm vi
- **Đầu vào**: sự kiện đăng nhập, session Core Banking, tạo giao dịch, phê duyệt, thông tin chi nhánh.  
- **Đầu ra**:  
  - Risk Score cho từng giao dịch hoặc cặp Maker–Checker.  
  - Dashboard trực quan + hệ thống cảnh báo.  
- **Không nhằm mục tiêu xác định gian lận 100%**, mà là **hệ thống cảnh báo sớm** để con người điều tra.  

## 3. 🛠️ Kiến trúc giải pháp
1. **Data Pipeline**: thu thập batch + realtime (Kafka/Flink).  
2. **Feature Engineering**:  
   - Hành vi cá nhân (login bất thường, ngoài giờ, cùng thiết bị, …).  
   - Hành vi Maker–Checker (cặp cố định, duyệt quá nhanh, …).  
   - Nội dung giao dịch (chia nhỏ tiền, beneficiary mới, …).  
   - Quan hệ mạng lưới (graph).  
3. **Model Layer**:  
   - Baseline: Logistic Regression, XGBoost.  
   - Advanced: Autoencoder, Isolation Forest.  
   - Future: Graph Neural Networks (GNN), LSTM/GRU.  
4. **Scoring Engine**: Risk Score (0–1), explainability (SHAP/LIME).  
5. **Dashboard**: Web app (Streamlit/Dash) để QLRR theo dõi và xử lý cảnh báo.  

## 4. 📅 Roadmap (12 tháng)

| Thời gian | Milestone | Deliverables |
|-----------|-----------|--------------|
| **Tháng 1–2** | **Milestone 0 – Khởi tạo** | Repo skeleton (đã có), synthetic dataset, baseline Logistic demo. |
| **Tháng 3–4** | **Milestone 1 – Proof of Concept (POC)** | Feature engineering cơ bản, huấn luyện XGBoost/LightGBM, dashboard Streamlit. |
| **Tháng 5–6** | **Milestone 2 – Anomaly Detection** | Autoencoder/Isolation Forest, phát hiện outlier, cảnh báo realtime giả lập bằng Kafka. |
| **Tháng 7–9** | **Milestone 3 – Advanced Modeling** | GNN cho quan hệ Maker–Checker, LSTM cho hành vi theo chuỗi thời gian. |
| **Tháng 10–12** | **Milestone 4 – Pilot & Community** | Pilot với dữ liệu ngân hàng (anonymized), mở rộng contributor, viết tài liệu song ngữ, chuẩn bị bài báo khoa học/blog. |

## 5. 👨‍👩‍👧‍👦 Quản lý cộng đồng & đóng góp
- **License**: Apache 2.0 → mở & dễ đóng góp.  
- **CONTRIBUTING.md**: hướng dẫn quy trình PR.  
- **Issue tracker**:  
  - `good first issue` → cho contributor mới.  
  - `help wanted` → kêu gọi cộng đồng.  
- **Roadmap public**: Trello/GitHub Projects.  
- **Meetup online**: tổ chức định kỳ 1–2 tháng/lần để cập nhật tiến độ.  

## 6. 📊 Chỉ số thành công (Success Metrics)
- **Kỹ thuật**:  
  - Risk Score AUC > 0.8 (baseline).  
  - Giảm 50% false negative so với rule-based truyền thống.  
- **Cộng đồng**:  
  - 50+ stars GitHub sau 6 tháng.  
  - 10+ contributors active.  
  - Blog/báo chí fintech VN đề cập.  

## 7. 🔐 Tuân thủ & bảo mật
- Dữ liệu demo: dùng synthetic dataset (không tiết lộ dữ liệu thật).  
- Khi pilot: áp dụng anonymization, encryption, logging & audit.  
- Tuân thủ pháp luật Việt Nam (Luật An toàn thông tin mạng, Nghị định về bảo mật dữ liệu tài chính).  

## 8. 🚀 Hướng phát triển dài hạn
- Kết hợp **federated learning** để huấn luyện mô hình liên ngân hàng mà không chia sẻ dữ liệu raw.  
- Mở rộng sang fraud detection cho **thẻ, internet banking, mobile banking, SWIFT**.  
- Đóng góp paper/bài nghiên cứu tại hội nghị AI/Fintech.  
