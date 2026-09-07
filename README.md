[README.md](https://github.com/user-attachments/files/31911560/README.md)
# 📚 Amazon Books Review Analysis

Bài tập cá nhân môn **Big Data** — Phân tích review khách hàng sách Amazon bằng PySpark, mô hình NLP và trực quan hoá qua Streamlit.

**Sinh viên:** Phan Hồ Ngọc Hân — MSSV: C25611249

🔗 **[Xem demo Streamlit](https://phanhongochan-btcn-bigdata.streamlit.app)**

---

## Tổng quan

Hệ thống xử lý toàn bộ pipeline từ dữ liệu thô đến dashboard tương tác:

1. **Thu thập & tiền xử lý** — load dataset Amazon Books Reviews từ Hugging Face, làm sạch với PySpark RDD
2. **Gán khía cạnh** — SentenceTransformer (`all-mpnet-base-v2`) + cosine similarity để phân loại câu review vào 10 khía cạnh
3. **Phân tích sentiment** — RoBERTa (`cardiffnlp/twitter-roberta-base-sentiment-latest`) gán nhãn Positive / Negative / Neutral cho từng câu
4. **Tóm tắt AI** — FLAN-T5 tổng hợp ý kiến tiêu cực theo từng khía cạnh
5. **Tính điểm ưu tiên** — công thức `Priority Score = coverage × neg_rate × intensity × confidence`
6. **Trực quan hoá** — Streamlit dashboard với 3 tab, 4 biểu đồ, KPI cards và Decision Cards

---

## Cấu trúc repo

```
btcn-bigdata/
├── btcn_app.py              # Streamlit dashboard
├── priority_ranking.csv     # Kết quả xếp hạng khía cạnh (output từ notebook)
├── evidence_table.csv       # Bảng câu evidence đã gán sentiment (output từ notebook)
├── decision_cards.json      # Decision cards P0/P1 với AI summary và hành động
└── requirements.txt         # Dependencies cho Streamlit Cloud
```

> Notebook phân tích (`btcn_bigdata.ipynb`) chạy trên **Google Colab** (yêu cầu GPU/TPU cho các mô hình NLP), không đưa vào repo này.

---

## Streamlit Dashboard

### Tab 1 — Phân tích ưu tiên

- **4 KPI cards:** Tổng câu phân tích, số khía cạnh phát hiện, tỷ lệ Negative, khía cạnh có Priority Score cao nhất
- **Biểu đồ Priority Score** theo từng khía cạnh (bar chart ngang)
- **Biểu đồ tròn** phân bố Positive / Negative / Neutral toàn bộ
- **Top 5 Neg Rate** theo khía cạnh (bar chart)
- **Bảng xếp hạng đầy đủ** với progress bar Priority Score
- **Biểu đồ phân phối Rating** (1★ → 5★)

### Tab 2 — Evidence

- **Bảng evidence** toàn bộ câu đã gán sentiment + độ tin cậy (có thể lọc theo khía cạnh & sentiment)
- **Stacked bar chart** phân bố sentiment theo từng khía cạnh
- **Top 15 Bigrams** trong câu Negative của khía cạnh P0/P1 (tính on-the-fly, không cần NLTK)

### Tab 3 — Decision Cards

- **Cards P0/P1** hiển thị: neg rate, avg rating, AI summary, hành động ưu tiên động theo ngưỡng neg_rate
- **Bảng tóm tắt** toàn bộ Decision Cards có scroll ngang

---

## Ngưỡng hành động 

| Neg Rate | Ký hiệu | Hành động |
|---|---|---|
| ≥ 30% | 🔴 | Xử lý khẩn trong tuần |
| ≥ 20% | 🟡 | Ưu tiên trong tháng |
| ≥ 10% | 🟢 | Lên kế hoạch quý |
| < 10% | ⚪ | Theo dõi định kỳ |

---

## Chạy local

```bash
pip install -r requirements.txt
streamlit run btcn_app.py
```

App sẽ chạy ở `http://localhost:8501`. Chế độ mặc định đọc trực tiếp `priority_ranking.csv`, `evidence_table.csv`, `decision_cards.json` từ cùng thư mục.

---

## Upload dữ liệu của bạn

Sidebar có chế độ **"⬆️ Upload file của bạn"** — upload 3 file đầu ra từ notebook của riêng bạn và nhập tên dataset để dashboard tự cập nhật toàn bộ tiêu đề, biểu đồ và KPI.

---

## Tech stack

| Thành phần | Công nghệ |
|---|---|
| Xử lý dữ liệu | PySpark RDD (Google Colab) |
| Gán khía cạnh | SentenceTransformer `all-mpnet-base-v2` |
| Sentiment | RoBERTa `cardiffnlp/twitter-roberta-base-sentiment-latest` |
| AI Summary | FLAN-T5 |
| Dashboard | Streamlit 1.63 |
| Biểu đồ | Plotly |
| Deploy | Streamlit Community Cloud |
