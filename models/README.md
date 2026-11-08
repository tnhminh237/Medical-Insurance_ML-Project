# Các tệp phục vụ thực nghiệm và triển khai

## Đã có: preprocessing_setup.joblib

Chạy `src/preprocessing.ipynb` để tạo hoặc cập nhật. File chứa:

- `numeric_features`, `categorical_features`, `target`: schema đầu vào và target hồi quy.
- `scaled`: ColumnTransformer có StandardScaler và One-Hot Encoding, **chưa fit**.
- `tree`: ColumnTransformer giữ nguyên cột số và One-Hot Encoding, **chưa fit**.
- `cost_quantiles`: hiện là `[1/3, 2/3]`.
- `cost_thresholds`: hai ngưỡng tính từ charges thực tế trên Train.
- `cost_labels`: `Thấp`, `Trung bình`, `Cao`, theo thứ tự tăng dần.

Thay schema cũ `classification_thresholds`/`classification_labels` bằng `cost_thresholds`/`cost_labels`. Không còn target Classification riêng. Dùng bản sao bộ tiền xử lý khi tạo Pipeline cho từng mô hình; fit trong từng fold CV.

## Chưa tạo: đầu ra sau khi thực nghiệm

- `regression_best_config.json`: thuật toán và tham số đã chọn bằng CV/tuning trên Train, do notebook thực nghiệm xuất.
- `regression_pipeline.joblib`: Pipeline Regression đã fit toàn bộ Train, do notebook huấn luyện cuối xuất.
- `cost_thresholds.json`: ngưỡng cố định và nhãn để web phân mức nhất quán với thực nghiệm. Lưu cả quy ước biên: Thấp <= ngưỡng dưới; Trung bình > ngưỡng dưới và <= ngưỡng trên; Cao > ngưỡng trên.

Web sử dụng Pipeline đã huấn luyện và ngưỡng. `preprocessing_setup.joblib` chưa phải mô hình dự đoán. Lưu phiên bản thư viện thực tế khi xuất mô hình để tái lập môi trường triển khai.
