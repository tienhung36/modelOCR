# Chuẩn bị dữ liệu
Tạo một custom_word.txt viết ra các từ ngẫu nhiên, mỗi từ có độ dài ngẫu nhiên từ 5 đến 10, được tạo ra bằng cách chọn các ký tự có trong bảng chữ cái Tiếng Việt gồm số, chữ hoa, chữ thường, chữ có dấu.

![Ảnh chụp màn hình 2023-06-04 213653](https://github.com/tienhung36/modelOCR/assets/106159669/f3eb2253-d64b-4632-b450-c813656c7346)
 
 Dùng Text Recognition Data Generator  để tạo tập train từ các từ trong custom_word.txt được tạo từ trước.
 

 
 Đọc các nhãn (labels) đã tạo bởi công cụ tạo nhãn TRDG, có định dạng tương tự như tên các tệp hình ảnh trong thư mục.



Chuyển đổi định dạng các nhãn này từ .txt sang .csv để sử dụng cho quá trình huấn luyện.
Tương tự cho tập val để sử dụng cho đánh giá mô hình
# Huấn luyện mô hình

Sau khi có được dữ liệu và phân chia dữ liệu. Nhóm tiến hành tinh chỉnh các tham số để pre_trained model.


![Ảnh chụp màn hình 2023-06-04 220301](https://github.com/tienhung36/modelOCR/assets/106159669/b81ad2ba-3352-42a6-b824-940f80ccea01)

FT: Finetune, là mô hình pretrained sẽ được freeze toàn bộ các lớp detection layer, đảm bảo quá trình training không ảnh hưởng đến mô hình nhận diện vị trí chữ digital đã được pretrained.

FeatureExtraction: Chọn mô hình trích xuất đặc trưng VGG để áp dụng

freeze_FeatureExtraction: Lớp VGG này cũng sẽ được freeze để chỉ dùng kết quả trích xuất làm mục tiêu nhận diện thay vì sẽ bị đạo hàm weight ngược vào lại.

SequenceModeling: Chọn mô hình nhận diện chữ cái tuần tự, ở đây BiLSTM là 1 trong các dạng RNN

![Ảnh chụp màn hình 2023-06-04 220326](https://github.com/tienhung36/modelOCR/assets/106159669/cc3c6401-b884-4c77-8d0c-352ac612400f)


Prediction: Chọn CTC làm loss function để đánh giá mô hình mỗi khi train để phù hợp với việc tính loss cho từng chữ digital.
num_iter: Số iteration mà mô hình sẽ lặp lại để train qua số lượng ảnh cần train.
valInterval: Khi đã train được đúng số iteration cần lặp lại, sẽ có 1 iteration là mô hình sẽ trải qua giai đoạn đánh giá (validation) để tinh chỉnh lại weight trước khi tiếp tục train tiếp. (ở đây, cứ train được 400 iteration là sẽ có 1 validation iteration, rồi tiếp tục cứ lặp lại đến hết).
# Kết quả train

![Ảnh chụp màn hình 2023-06-04 220401](https://github.com/tienhung36/modelOCR/assets/106159669/debb9794-bcad-4e9a-bbdd-aad00149a51c)

![Ảnh chụp màn hình 2023-06-04 220434](https://github.com/tienhung36/modelOCR/assets/106159669/96e4a14a-37af-43e3-adf0-c6112591f03b)


Đánh giá mô hình 

![Ảnh chụp màn hình 2023-06-04 220505](https://github.com/tienhung36/modelOCR/assets/106159669/fe6a6a4c-1df4-418d-8bbd-3573a77dcce5)

![Ảnh chụp màn hình 2023-06-04 220530](https://github.com/tienhung36/modelOCR/assets/106159669/7ad53c83-1c0f-4b78-94d0-d7f9cb88bcef)

# Áp dụng kết quả train vào model
Sau khi pre_trained model, ta thu được file trọng số để áp dụng vào mô hình để giải quyết bài toán

Sử dụng mô hình để nhận dạng gộp các box cùng 1 hàng thành text

