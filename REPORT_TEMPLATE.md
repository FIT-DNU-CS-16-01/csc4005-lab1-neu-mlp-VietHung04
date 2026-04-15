# CSC4005 – Lab 1 Report Template

## 1. Mục tiêu

### 1.1 Mục tiêu bài thực hành
Mục tiêu chính của bài lab này là xây dựng một pipeline huấn luyện hoàn chỉnh cho bài toán phân loại hình ảnh bằng mạng nơ-ron truyền thẳng (MLP). Thay vì tập trung tối ưu hóa điểm số, bài thực hành chú trọng vào việc giúp sinh viên nắm vững quy trình bài bản trong học sâu, bao gồm:

- Xử lý dữ liệu: Chuẩn bị và chuẩn hóa dữ liệu hình ảnh đúng cách.

- Huấn luyện & Đánh giá: Hiểu rõ vai trò của các tập dữ liệu Train, Validation, Test và lựa chọn hàm mất mát (Loss function) phù hợp.

- Kiểm soát mô hình: Theo dõi và xử lý hiện tượng Overfitting và Underfitting thông qua các kỹ thuật Regularization.

- Tối ưu hóa: So sánh hiệu quả của các bộ tối ưu (Optimizer) khác nhau.

- Quản lý thí nghiệm: Sử dụng công cụ Weights & Biases (W&B) để ghi lại toàn bộ quá trình thực nghiệm, từ biểu đồ học tập (Learning curves) đến bảng so sánh cấu hình.

### 1.2 Bộ dữ liệu sử dụng

Bài thực hành sử dụng bộ dữ liệu NEU Surface Defect Database. Đây là bộ dữ liệu thực tế dùng để nhận diện các lỗi trên bề mặt thép, bao gồm 1.800 ảnh được chia thành 06 lớp đối tượng:

- Crazing (Vết nứt bề mặt)

- Inclusion (Tạp chất)

- Patches (Mảng bám)

- Pitted Surface (Bề mặt rỗ)

- Rolled-in Scale (Vảy cán thép)

- Scratches (Vết trầy xước)

## 2. Cấu hình thí nghiệm

Phần này trình bày các thiết lập thông số cho 3 kịch bản huấn luyện khác nhau nhằm so sánh hiệu năng giữa các bộ tối ưu (Optimizer) và mức độ tác động của các kỹ thuật điều chuẩn (Regularization).

### 2.1 Các tham số chung
Tất cả các thí nghiệm đều sử dụng chung các cấu hình nền tảng sau để đảm bảo tính khách quan khi so sánh:
- Dữ liệu: NEU-CLS dataset.
- Kích thước ảnh: 64 x 64 pixels.
- Batch size: 32.
- Số Epoch tối đa: 20.
- Early Stopping: Dừng sớm nếu không cải thiện sau 5 epoch (patience=5).
- Augmentation: Có sử dụng tăng cường dữ liệu.
- Công cụ theo dõi: Weights & Biases (wandb).
### 2.2 Chi tiết các cấu hình chạy (Runs)
Dưới đây là bảng tổng hợp thông số chi tiết cho 3 lần chạy:

Tham số |	Run 1: Baseline (AdamW) |	Run 2: SGD |	Run 3: Regularization
---------  | --------- | --------- | ---------
Run Name |	baseline_adamw |	run_sgd |	run_regularization
Optimizer |	AdamW |	SGD |	AdamW
Learning Rate |	0.001 |	0.01 |	0.001
Weight Decay |	0.0001 |	0.0001  |	0.001
Dropout |	0.3 |	0.3 |	0.5

### 2.3 Mô tả mục tiêu từng cấu hình
#### Cấu hình Baseline (baseline_adamw):

- Sử dụng bộ tối ưu AdamW với các tham số tiêu chuẩn.

- Mục tiêu: Thiết lập một mốc hiệu năng cơ bản để so sánh với các thay đổi sau này.

#### Cấu hình So sánh Optimizer (run_sgd):

- Thay đổi sang bộ tối ưu SGD với Learning Rate lớn hơn (0.01) và sử dụng weight decay nhỏ (0.0001)

- Mục tiêu: Đánh giá tốc độ hội tụ và độ ổn định của SGD so với AdamW trên bài toán phân loại ảnh lỗi thép.

#### Cấu hình Điều chuẩn mạnh (run_regularization):

- Giảm Learning Rate, tăng Weight Decay lên gấp 10 lần và tăng Dropout lên 0.5.

- Mục tiêu: Quan sát khả năng kiểm soát hiện tượng Overfitting, giúp mô hình tổng quát hóa tốt hơn trên tập Validation và Test.
## 3. Kết quả
### 3.1 Bảng so sánh cấu hình (Metrics)
Chỉ số |	Run 1: Baseline (AdamW) |	Run 2: SGD |	Run 3: Regularization
---------  | --------- | --------- | ---------
Best Val Accuracy |	41.85% |	44.07% |	38.15%
Test Accuracy |	38.15% |	40.74% |	36.30%
Best Val Loss |	1.4993 |	1.4618 |	1.6226
Test Loss |	1.4957 |	1.4542 |	1.5997

### 3.2 Learning Curves (Nhận xét chung)
- Baseline:

<img width="1484" height="658" alt="image" src="https://github.com/user-attachments/assets/19f556b3-078e-4a79-a2d7-e321fac8380a" />

- Run 2 (SGD): Cho thấy xu hướng hội tụ tốt nhất và ổn định nhất về mặt độ chính xác trên cả tập Validation và Test.

<img width="1484" height="657" alt="image" src="https://github.com/user-attachments/assets/0b31a29a-83e6-4b5d-ad89-d1d93204db86" />

- Run 3 (Regularization): Do áp dụng Dropout (0.5) và Weight Decay (0.001) quá cao, mô hình gặp khó khăn trong việc học các đặc trưng quan trọng, dẫn đến kết quả thấp nhất trong 3 kịch bản.

<img width="1484" height="658" alt="image" src="https://github.com/user-attachments/assets/5e6816f8-966b-41d1-b0af-a38371178d40" />

### 3.3 Confusion Matrix

- Baseline:

<img width="765" height="734" alt="image" src="https://github.com/user-attachments/assets/80c83773-778b-43d5-9751-4e2bdf4b02fa" />

- Run 2 (SGD): 

<img width="765" height="734" alt="image" src="https://github.com/user-attachments/assets/4365c9f6-74b7-415e-9802-cacc3de78ce3" />

- Run 3 (Regularization): 

<img width="765" height="734" alt="image" src="https://github.com/user-attachments/assets/3b5a2f65-376c-4064-927f-ccd7934ccee6" />

## 4. Phân tích

### 4.1 Cấu hình tốt nhất
Cấu hình Run 2 (SGD) là cấu hình tốt nhất với độ chính xác trên tập Test đạt 40.74%. Mặc dù MLP không phải là kiến trúc mạnh nhất cho bài toán hình ảnh, nhưng SGD với learning rate 0.01 cho kết quả tốt hơn AdamW trong thí nghiệm này. Một nguyên nhân có thể là learning rate lớn giúp mô hình cập nhật mạnh hơn trong giai đoạn đầu, từ đó cải thiện khả năng học đặc trưng so với AdamW với learning rate nhỏ hơn.
### 4.2 Dấu hiệu Overfitting / Underfitting
- Underfitting: Cấu hình Strong Regularization (dropout = 0.5, weight_decay = 0.001) dẫn đến hiện tượng underfitting rõ rệt. Train accuracy chỉ đạt ~25% cho thấy mô hình không học được đặc trưng quan trọng của dữ liệu. So với baseline (~34%), train accuracy của mô hình này giảm đáng kể, củng cố nhận định về hiện tượng underfitting.
Nguyên nhân là do:
-- Dropout quá cao làm mất nhiều thông tin trong quá trình huấn luyện
-- Weight decay lớn làm giảm độ lớn của trọng số quá mức

Điều này chứng minh rằng regularization cần được điều chỉnh hợp lý, không phải càng mạnh thì càng tốt.

- Overfitting: Không có dấu hiệu overfitting rõ rệt trong các thí nghiệm. Khoảng cách giữa validation và test là nhỏ, cho thấy mô hình có khả năng tổng quát hóa tương đối ổn định.

### 4.3 So sánh AdamW và SGD
- AdamW (Run 1): Hội tụ nhanh nhưng dễ bị kẹt ở mức hiệu năng trung bình (~40%). Nó gặp khó khăn đặc biệt với lớp Inclusion.

- SGD (Run 2) cho kết quả tốt hơn AdamW, với độ chính xác cao hơn và phân bố dự đoán đồng đều hơn giữa các lớp (quan sát qua confusion matrix). Learning rate lớn (0.01) có thể giúp mô hình cập nhật mạnh hơn và học đặc trưng hiệu quả hơn trong giai đoạn đầu.

## 5. Kết luận
Mô hình được chọn là cấu hình Run 2 (SGD).

Lý do lựa chọn:

- Hiệu năng vượt trội: Đạt độ chính xác cao nhất trên tập Test (40.74%), cao hơn cấu hình Baseline 2-3%.

- Độ tin cậy: Khoảng cách giữa kết quả Validation và Test của Run 2 là rất nhỏ (~4%), chứng tỏ mô hình có khả năng tổng quát hóa tốt trên dữ liệu mới.

Tuy nhiên, do sử dụng MLP cho dữ liệu hình ảnh, mô hình không khai thác được cấu trúc không gian của ảnh. Điều này giới hạn hiệu năng ở mức khoảng 40–45%. Trong thực tế, các kiến trúc như CNN sẽ phù hợp hơn cho bài toán này.

