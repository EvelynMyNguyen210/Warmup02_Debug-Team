# 2. Mô tả tập dữ liệu
## 2.1 Nguồn dữ liệu
Trong dự án này, nhóm sử dụng bộ dữ liệu **PaySim – Mobile Money Transactions**, được công bố trên nền tảng Kaggle. Đây là một dataset mô phỏng các giao dịch tài chính di động, được xây dựng dựa trên dữ liệu thực tế nhưng đã được xử lý và tái tạo nhằm phục vụ cho nghiên cứu.

PaySim được phát triển dựa trên hành vi giao dịch trong hệ thống Mobile Money tại châu Phi, với quy mô lên đến hàng triệu bản ghi. Nhờ kích thước lớn và cấu trúc rõ ràng, dataset này rất phù hợp để xây dựng và kiểm thử các mô hình Machine Learning, đặc biệt trong bối cảnh phát hiện gian lận – nơi mà dữ liệu thực thường rất khó tiếp cận do yếu tố bảo mật.

## 2.2 Cấu trúc dữ liệu
Mỗi dòng trong dataset đại diện cho một giao dịch tài chính cụ thể. Dữ liệu bao gồm nhiều thông tin quan trọng như thời gian thực hiện giao dịch, loại giao dịch, số tiền cũng như trạng thái tài khoản của cả người gửi và người nhận trước và sau giao dịch.

Ví dụ, một vài dòng dữ liệu đầu tiên có thể được biểu diễn như sau:

#### Ví dụ dữ liệu (Sample Transactions)

Dưới đây là một số dòng dữ liệu tiêu biểu được trích từ dataset PaySim nhằm minh hoạ cấu trúc của một giao dịch:

| step | type     | amount  | nameOrig     | oldbalanceOrg | newbalanceOrig | nameDest     | oldbalanceDest | newbalanceDest | isFraud |
|------|----------|---------|--------------|----------------|----------------|--------------|----------------|----------------|--------|
| 1    | PAYMENT  | 9839.64 | C1231006815  | 170136.0       | 160296.36      | M1979787155  | 0.0            | 0.0            | 0      |
| 1    | PAYMENT  | 1864.28 | C1666544295  | 21249.0        | 19384.72       | M2044282225  | 0.0            | 0.0            | 0      |
| 1    | TRANSFER | 181.00  | C1305486145  | 181.0          | 0.0            | C553264065   | 0.0            | 0.0            | 1      |
| 1    | CASH_OUT | 181.00  | C840083671   | 181.0          | 0.0            | C38997010    | 21182.0        | 0.0            | 1      |
| 1    | PAYMENT  | 11668.14| C2048537720  | 41554.0        | 29885.86       | M1230701703  | 0.0            | 0.0            | 0      |

**Nhận xét nhanh:**  
- Các giao dịch gian lận (`isFraud = 1`) thường xuất hiện trong các loại như `TRANSFER` hoặc `CASH_OUT`.  
- Có thể quan sát các trường hợp tài khoản bị rút về 0 ngay sau giao dịch – một dấu hiệu đáng nghi cần được mô hình học và phát hiện.

Từ cấu trúc này có thể thấy dataset không chỉ lưu thông tin giao dịch đơn lẻ mà còn phản ánh được dòng tiền và sự thay đổi số dư, tạo điều kiện thuận lợi cho việc phát hiện các hành vi bất thường.

## 2.3 Giải thích các đặc trưng 
Một trong những điểm mạnh của dataset PaySim nằm ở việc các biến được thiết kế khá trực quan và mang ý nghĩa gần với thực tế hệ thống tài chính. Điều này giúp người làm Machine Learning không chỉ “train model” mà còn hiểu được logic đằng sau dữ liệu.

Trước hết, biến amount đại diện cho số tiền của mỗi giao dịch. Đây là một trong những yếu tố quan trọng nhất, bởi trong thực tế, các giao dịch gian lận thường có xu hướng liên quan đến những khoản tiền bất thường – hoặc quá lớn so với hành vi thông thường của người dùng, hoặc được chia nhỏ một cách có chủ đích để tránh bị phát hiện.

Biến step thể hiện thời gian giao dịch theo đơn vị giờ, kéo dài trong khoảng gần 30 ngày. Nhờ đó, ta có thể phân tích hành vi theo chuỗi thời gian, ví dụ như việc một tài khoản thực hiện nhiều giao dịch liên tiếp trong thời gian ngắn – một dấu hiệu thường thấy trong các kịch bản gian lận.

Bên cạnh đó, các biến liên quan đến số dư như oldbalanceOrg, newbalanceOrig, oldbalanceDest và newbalanceDest đóng vai trò cực kỳ quan trọng trong việc mô tả dòng tiền. Thay vì chỉ nhìn vào một giao dịch đơn lẻ, các biến này cho phép ta quan sát sự thay đổi trạng thái tài chính trước và sau giao dịch. Ví dụ, một tài khoản bị rút sạch về 0 ngay sau một lần chuyển tiền, hoặc một tài khoản nhận tiền nhưng trước đó không có số dư rõ ràng, đều là những tín hiệu đáng nghi.

Ngoài ra, biến type giúp phân loại các giao dịch thành nhiều nhóm khác nhau như PAYMENT, TRANSFER, CASH_OUT,… Điều này đặc biệt hữu ích vì trong thực tế, không phải loại giao dịch nào cũng có mức độ rủi ro như nhau. Chẳng hạn, các giao dịch TRANSFER và CASH_OUT thường có liên quan nhiều hơn đến gian lận so với các giao dịch thanh toán thông thường.

Tổng thể, các đặc trưng trong dataset không chỉ cung cấp thông tin định lượng mà còn phản ánh được hành vi tài chính, tạo nền tảng tốt để xây dựng các feature nâng cao trong các bước tiếp theo.

## 2.4 Biến mục tiêu 
Trong bài toán này, biến isFraud đóng vai trò là nhãn mục tiêu mà mô hình cần dự đoán. Đây là một bài toán phân loại nhị phân (binary classification), trong đó mỗi giao dịch được gán một trong hai giá trị: 1 nếu là gian lận và 0 nếu là hợp lệ.

Nghe có vẻ đơn giản, nhưng trong thực tế, việc dự đoán biến này lại không hề dễ. Một mô hình tốt không chỉ cần phát hiện được càng nhiều giao dịch gian lận càng tốt, mà còn phải hạn chế tối đa việc “báo động nhầm” đối với các giao dịch hợp lệ. Điều này đặc biệt quan trọng trong hệ thống tài chính, vì mỗi lần cảnh báo sai có thể gây phiền toái cho người dùng, thậm chí làm giảm niềm tin vào hệ thống.

Do đó, bài toán ở đây thực chất là một bài toán cân bằng giữa hai mục tiêu: phát hiện đúng gian lận (high recall) và giữ tỷ lệ cảnh báo sai ở mức chấp nhận được (precision). Đây cũng là lý do tại sao các chỉ số đánh giá mô hình trong phần sau sẽ không chỉ dừng lại ở accuracy mà còn bao gồm precision, recall và F1-score.

## 2.5 Thách thức chính: Class Imbalance
Một trong những thách thức lớn nhất khi làm việc với dataset này là hiện tượng mất cân bằng dữ liệu (class imbalance). Cụ thể, số lượng giao dịch gian lận chỉ chiếm một tỷ lệ rất nhỏ trong toàn bộ dataset, thường dưới 1%, trong khi phần lớn còn lại là các giao dịch hợp lệ.

Vấn đề này gây ra một hệ quả khá “nguy hiểm”: mô hình có thể đạt độ chính xác rất cao chỉ bằng cách dự đoán tất cả các giao dịch là không gian lận. Ví dụ, nếu 99% dữ liệu là hợp lệ, một mô hình luôn dự đoán 0 vẫn đạt accuracy 99%, nhưng lại hoàn toàn thất bại trong việc phát hiện gian lận – tức là mục tiêu chính của bài toán.

Chính vì vậy, trong bối cảnh này, accuracy không còn là thước đo đáng tin cậy. Thay vào đó, các chỉ số như recall (khả năng phát hiện gian lận) và F1-score (cân bằng giữa precision và recall) trở nên quan trọng hơn rất nhiều. Một mô hình tốt cần đảm bảo không bỏ sót quá nhiều giao dịch gian lận, ngay cả khi phải chấp nhận một mức độ cảnh báo sai nhất định.

Để giải quyết vấn đề này, các kỹ thuật xử lý mất cân bằng sẽ được áp dụng trong giai đoạn tiền xử lý dữ liệu. Cụ thể, phương pháp oversampling (như SMOTE) có thể được sử dụng để tăng số lượng mẫu gian lận, trong khi undersampling giúp giảm bớt số lượng mẫu không gian lận. Việc kết hợp các phương pháp này sẽ giúp mô hình học được pattern của lớp thiểu số tốt hơn và cải thiện khả năng phát hiện gian lận trong thực tế.

## 2.6 Nhận xét sơ bộ
Nhìn chung, PaySim là một dataset có cấu trúc rõ ràng, dễ tiếp cận và đủ thông tin để xây dựng các feature liên quan đến hành vi tài chính. Đây là một lựa chọn rất phù hợp để xây dựng mô hình nền tảng cho bài toán phát hiện gian lận.

Tuy nhiên, cần lưu ý rằng đây là dữ liệu mô phỏng (synthetic data), nên các pattern gian lận có thể “đơn giản hóa” so với thực tế. Ngoài ra, dataset còn thiếu các yếu tố ngữ cảnh quan trọng như vị trí, thiết bị hay lịch sử hành vi dài hạn, và cũng không phản ánh được tính chất thời gian thực của hệ thống tài chính. Những hạn chế này cần được cân nhắc khi đánh giá khả năng áp dụng mô hình vào môi trường thực tế.

# Tài liệu tham khảo
Lopez-Rojas, E., & Axelsson, S. (2014). PaySim: A financial mobile money simulator for fraud detection.

Kaggle. (n.d.). PaySim dataset. Truy cập từ: https://www.kaggle.com/datasets/ealaxi/paysim1/data





