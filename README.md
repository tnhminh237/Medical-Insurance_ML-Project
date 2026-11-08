# Medical Insurance ML Project

Đồ án Machine Learning: dự đoán chi phí y tế cá nhân tại Mỹ và phân mức chi phí dự đoán thành **Thấp / Trung bình / Cao**.

Bài toán chính là **Regression**. Phân mức là quy tắc áp dụng lên số tiền dự đoán, không huấn luyện mô hình Classification riêng. Các mức mô tả chi phí tương đối trong bộ dữ liệu, không phải chuẩn y tế hoặc mức rủi ro lâm sàng.

## Quy trình

Dữ liệu gốc → kiểm tra và cleaning → chia Train/Test → EDA trên Train → thiết lập tiền xử lý → so sánh 4 mô hình Regression bằng CV → tuning → chốt mô hình → fit toàn bộ Train → đánh giá Test → XAI → ứng dụng web.

- Đầu vào: `age`, `sex`, `bmi`, `children`, `smoker`, `region`.
- Target: `charges` (USD).
- Encoding/scaling được fit trong từng fold CV bằng Pipeline; không xuất CSV đã scaling toàn bộ Train để chạy CV.
- Dùng CV/tuning trên Train để chọn mô hình. Test dành cho đánh giá cuối cùng.
- Mức chi phí suy ra bằng cùng ngưỡng cố định cho chi phí thực tế và dự đoán; có thể đánh giá bằng confusion matrix và F1-macro.

## Cấu trúc hiện tại

```text
data/
  medical_insurance.csv
  medical_insurance_cleaned.csv
  medical_insurance_train.csv
  medical_insurance_test.csv
src/
  data_cleaning.ipynb
  train_test_split.ipynb
  eda.ipynb
  preprocessing.ipynb
  regression_experiments.ipynb
  train_final.ipynb
  xai.ipynb
models/
  preprocessing_setup.joblib
  README.md
results/
  eda/figures/
app/
  app.py
requirements.txt
```

Khi có kết quả thực nghiệm, dùng `results/regression/figures/`, `results/regression/metrics/`, `results/xai/figures/` và `results/xai/metrics/`. Không tạo file kết quả giả hoặc xem các notebook khung là phần đã hoàn thành.

## Cài đặt và chạy

Yêu cầu Python 3.12; môi trường hiện tại đã được kiểm tra với phiên bản này. Chạy từ thư mục gốc dự án:

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/macOS: source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter lab
```

Chạy các notebook theo thứ tự trong cây `src/` ở trên. Notebook hỗ trợ thư mục làm việc là gốc dự án hoặc `src/`.

## Trạng thái và đầu ra

- Đã có dữ liệu thật: 2.772 dòng gốc; sau loại 1.435 dòng trùng hoàn toàn còn 1.337 dòng. Vì không có ID cá nhân, việc loại trùng dựa trên giả định các hàng giống hệt là bản ghi lặp.
- Đã có Train/Test 80/20, `random_state=42`: 1.069/268 dòng; có EDA và cấu hình tiền xử lý.
- `preprocessing.ipynb` tạo hai bộ tiền xử lý **chưa fit** và lưu vào `models/preprocessing_setup.joblib`.
- Ngưỡng hiện giữ phân vị **1/3 và 2/3 của charges thực tế trên Train**; chưa đổi sang Q1/Q3. Tại ngưỡng dưới thuộc mức Thấp, tại ngưỡng trên thuộc mức Trung bình. Không tính lại ngưỡng từ Test hoặc mỗi yêu cầu dự đoán.
- Các notebook Regression, huấn luyện cuối và XAI hiện là khung hướng dẫn. Chưa có mô hình cuối cùng hoặc kết quả đánh giá mô hình.
- Đầu ra dự kiến sau huấn luyện cuối: `models/regression_pipeline.joblib` và `models/cost_thresholds.json`.
- Web sẽ tải Pipeline và ngưỡng đã lưu để dự đoán; không huấn luyện lại khi phục vụ. `app/app.py` hiện là khung; framework và nơi deploy chưa được chọn.

Trong báo cáo, gộp kiểm tra, làm sạch và chia tập vào mục **3.3. Kiểm tra, làm sạch và phân chia dữ liệu**. Phân mức không được trình bày như một classifier được huấn luyện độc lập.
