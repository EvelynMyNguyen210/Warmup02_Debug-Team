# 1. Anomaly Detection là gì? – Khi những "kẻ lạc loài" lên tiếng

Trong thế giới Data Science, chúng ta thường dành phần lớn thời gian để đi tìm những quy luật chung (patterns) – thứ giúp mô hình hiểu được số đông. Thế nhưng, đôi khi những giá trị "lệch chuẩn", những điểm dữ liệu "đi lạc" khỏi đám đông lại chính là nơi chứa đựng những thông tin đắt giá nhất. Đó chính là lãnh địa của **Anomaly Detection** (Phát hiện bất thường).

Nói một cách kỹ thuật, **Anomaly Detection** là quá trình xác định các điểm dữ liệu có đặc tính phân phối khác biệt đáng kể so với phần còn lại của tập dữ liệu. Trong thống kê, chúng ta gọi chúng là các **Outliers** (những mẫu ngoại lệ). 

Tuy nhiên, cần phân biệt rõ: **Anomaly không phải là Nhiễu (Noise)**. Nếu nhiễu là những sai số ngẫu nhiên cần được loại bỏ trong quá trình tiền xử lý (Data Cleaning), thì **Anomaly** lại là những tín hiệu thực sự, báo hiệu một sự thay đổi đột ngột hoặc một hành vi đặc biệt trong hệ thống.

Để thấy rõ tầm quan trọng của những "kẻ lạc loài" này, hãy nhìn vào các bài toán thực tế:

* **Giao dịch tài chính (Financial Transactions):** Giả sử lịch sử chi tiêu của bạn chỉ quẩn quanh các quán cà phê và siêu thị tại Việt Nam với hạn mức dưới 2 triệu đồng. Đột nhiên, một giao dịch 100 triệu đồng mua trang sức tại London xuất hiện. Thuật toán sẽ ngay lập tức gắn cờ (flag) điểm bất thường này để ngăn chặn hành vi chiếm đoạt tài khoản.
* **An ninh mạng (Cybersecurity):** Một server đang hoạt động ổn định bỗng dưng nhận được lượng request tăng vọt theo cấp số nhân trong vài giây (Spike). Đây không đơn giản là sự tăng trưởng người dùng, mà là dấu hiệu của một cuộc tấn công DDoS đang trực chờ đánh sập hệ thống.
* **Giám sát công nghiệp (Industrial IoT):** Trong một dây chuyền sản xuất, các cảm biến rung động (Vibration Sensors) gửi về tín hiệu đều đặn. Một sự thay đổi nhỏ trong biên độ sóng có thể là "lời cầu cứu" của vòng bi sắp hỏng trước khi máy móc thực sự đổ vỡ.

# 2. Sự quan trọng của Anomaly Detection và ứng dụng
## 2.1. Tại sao Anomaly Detection quan trọng?
Hiện nay với sự phát triển không ngừng của xã hội, dữ liệu đóng vai trò lớn, biểu thị sự phát triển hay vận hành của nhiều lĩnh vực, sự gia tăng về dữ liệu cũng dẫn theo nguy cơ về sai sót và bất thường ngày càng tăng cao. Việc phát hiện sớm các điểm bất thường này có thể giúp các tổ chức, doanh nghiệp xử lí kịp thời vấn đề về sai sót dữ liệu, từ đó đưa ra giải pháp nâng cấp, bảo trì phù hợp, tối ưu hóa chi phí vận hành. Thông qua anomaly detection, một số lợi ích ta có thể đạt được như:
- Phát hiện gian lận
- Phát hiện lỗi hệ thống
- Phát hiện tấn công mạng

## 2.2. Những ứng dụng của Anomaly Detection
### 2.2.1. Phát hiện gian lận tài chính
Trong lĩnh vực tài chính, việc phát hiện các điểm bất thường trong giao dịch có thể giúp doanh nghiệp phát hiện kịp thời các giao dịch mang tính gian lận, từ đó doanh nghiệp có thể bảo vệ khách hàng cũng như giảm thiểu rủi ro về tổn thất tài chính. Ứng dụng cụ thể có thể kể đến:
**Transaction fraud:**
* Hành vi cố tình sử dụng thông tin tài chính bị đánh cắp để thực hiện các giao dịch mua sắm, chuyển tiền trái phép. Thông qua việc phân tích các điểm bất thường trong dữ liệu giao dịch, ngân hàng có thể can thiệp kịp thời và ngăn chặn các giao dịch trái phép, khả nghi cho khách hàng.

**Credit card fraud:**
* Thông tin tín dụng cá nhân rất dễ bị đánh cắp, vì vậy anomaly detection có thể được ứng dụng để truy vết các giao dịch tín dụng từ IP lạ hay các giao dịch lớn bất thường so với thường lệ. Từ đó ngân hàng có thể ngăn chặn các giao dịch này, đảm bảo quyền lợi và thiệt hại tài chính của chủ thẻ.

### 2.2.2. An ninh mạng
Trong lĩnh vực an ninh mạng, việc phát hiện các truy cập khả nghi hay request bất thường có thể giúp tìm ra lỗ hổng bảo mật hiện có, từ đó có ngăn chặn kịp thời các cuộc tấn công mạng, nâng cao bảo mật của hệ thống mạng máy tính.

**Network intrusion:** 
* Thông qua giám sát lưu lượng truy cập mạng bất thường của một truy cập, hệ thống có thể đưa ra cảnh báo đến người quản trị, ngăn chặn việc xâm nhập mạng bất thường.

**Abnormal login activity:**
* Đề cập đến các truy cập không rõ nguồn gốc, không được cấp phép hay từ các thiết bị mới. Thông qua nhận diện các truy cập này, hệ thống có thể gửi cảnh báo để người dùng về một rủi ro về bảo mật tài khoản hiện tại.

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/part2_cybersecurity.png style="margin: 0 auto; display: block;"><br/>
  <em>Hình 2.1. Anomalies trong an ninh mạng (Nguồn: https://www.wiz.io/academy/detection-and-response/anomaly-detection)</em>
</p>

### 2.2.3. Industrial monitoring:
Trong hệ thống máy công nghiệp, các thiết bị có thể đọc ghi các kết quả sai theo thời gian. Việc phát hiện sớm các sai sót về dữ liệu như sensor có thể giúp doanh nghiệp tìm nguyên nhân và sửa chữa kịp thời, đảm bảo tính ổn định của hệ thống.

**Lỗi máy móc** 
* Một hư hỏng về bộ phận bất kì trong máy sản xuất cũng có thể ảnh hưởng đến các thiết bị khác. Ví dụ như hệ thống laser của một máy cắt laser khi bị lỗi có thể cấp nhiệt quá mức cho phép, từ đó gây ra sai số dữ liệu cho sensor nhiệt. Việc phát hiện bất thường trong dữ liệu đọc ghi từ các thiết bị này có thể giúp doanh nghiệp truy vết được các vấn đề khác, từ đó bảo trì máy được tốt hơn.

**Sai số điều khiển:**
* Trong lĩnh vực điều khiển, các dữ liệu về motor có thể phản ánh tính chính xác của bộ điều khiển, việc phân tích các dữ liệu có thể giúp tìm ra hạn chế về thiết kế hiện tại và tối ưu hệ thống.

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/part2_industrial.png style="margin: 0 auto; display: block;"><br/>
  <em>Hình 2.2. Anomaly trong dữ liệu sensor (Nguồn: https://www.themoonlight.io/de/review/an-attention-based-deep-generative-model-for-anomaly-detection-in-industrial-control-systems)</em>
</p>

### 2.2.4. Healthcare:
Trong lĩnh vực y tế, các dữ liệu về bệnh nhân rất quan trọng cho việc theo dõi sức khỏe bệnh nhân. Thông qua các dữ liệu này, bác sĩ có thể theo dõi các chuyển biến về sức khỏe và cung cấp các giải pháp y tế kịp thời.

**Dữ liệu y tế:**
* Các dữ liệu như mỡ máu, huyết áp bất thường giúp bác sĩ tầm soát các bệnh lí có thể xảy ra ở bệnh nhân, qua đó hỗ trợ chuẩn đoán bệnh và phòng ngừa.

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/part2_healthcare.png style="margin: 0 auto; display: block;"><br/>
  <em>Hình 2.3. Anomaly trong dữ liệu y tế (Nguồn: https://unit8.com/resources/anomaly-detection-in-healthcare-data-with-darts/) </em>
</p>

# 3. Phân loại Anomaly

*Trong Data Science, để chọn đúng phương pháp xử lý, chúng ta cần nhận biết các điểm bất thường (anomalies) dưới hai góc độ: Nguyên nhân sinh ra chúng và cách biểu hiện trong tập dữ liệu.*

## 3.1. Phân loại theo nguyên nhân

Hệ thống phát hiện bất thường có thể phát hiện hai loại bất thường chính: bất thường không chủ đích và bất thường có chủ đích.

### 1. Bất thường ngoài ý muốn (Unintentional anomalies)

Những bất thường không cố ý là các điểm dữ liệu lệch khỏi mức bình thường do lỗi hoặc nhiễu trong quá trình thu thập dữ liệu. Những lỗi này có thể là có hệ thống hoặc ngẫu nhiên, thường xuất phát từ các vấn đề như cảm biến bị lỗi hoặc sai sót của con người khi nhập dữ liệu. Các bất thường không cố ý này có thể làm méo mó bộ dữ liệu, khiến việc rút ra kết luận chính xác từ dữ liệu trở nên khó khăn hơn.

Ví dụ:

 - **Lỗi thu thập dữ liệu**: Một nhân viên nhập liệu vô tình gõ dư một số "0", biến giao dịch 1.000.000 VNĐ thành 10.000.000 VNĐ.

 - **Lỗi phần cứng**: Một cảm biến nhiệt độ bị chập mạch do trời mưa, gửi về hệ thống mức nhiệt 100°C.

 - **Sự kiện ngẫu nhiên**: Một bài đăng trên mạng xã hội đột nhiên "viral" khiến lưu lượng truy cập tăng gấp nhiều lần bình thường.


 <p align="center">
   <img src="https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/3_1_social_viral.png" style="margin: 0 auto; display: block;"><br/>
   <em>Hình 3.1. Bài hát đột nhiên lan truyền mạnh khiến lượt nhắc đến tăng vọt; nguồn:  <a href="https://www.talkwalker.com/blog/trend-analysis-life-of-a-showgirl">Talkwalker Social Listening</a> </em>
 </p>

### 2. Bất thường có chủ đích (Intentional anomalies)
 Bất thường có chủ đích là những điểm dữ liệu khác biệt do một sự kiện hoặc hành động cụ thể, thường mang ý nghĩa quan trọng. Những bất thường này đôi khi lại rất có giá trị, vì chúng có thể cho thấy những hiện tượng đặc biệt hoặc xu hướng đáng chú ý trong bộ dữ liệu.

 Ví dụ doanh số bán hàng tăng vọt vào các đợt sale có thể được xem là một intentional anomaly. Mặc dù nó khác với dữ liệu doanh số thông thường, nhưng sự tăng đột biến này là điều có thể dự đoán được vì nó gắn với một sự kiện thực tế và thường được chuẩn bị trước.

  <p align="center">
   <img src="https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/part2_healthcare.png/3_2_Intentional_anomalies.png" style="margin: 0 auto; display: block;"><br/>
   <em>Hình 3.2. Doanh số tăng vọt trong đợt sale, một dạng bất thường có chủ đích trong dữ liệu. </em>
 </p>

# 5. Những hạn chế thực tế trong việc phát hiện bất thường (Anomaly Detection)
Mặc dù anomaly detection là một kỹ thuật rất quan trọng trong data science, việc phát hiện các mẫu dữ liệu bất thường trong dữ liệu thực tế thường gặp nhiều khó khăn. Trong nhiều hệ thống, anomaly xảy ra rất hiếm, dữ liệu có thể thay đổi theo thời gian, và tập dữ liệu có thể chứa rất nhiều đặc trưng khác nhau. Những yếu tố này khiến các thuật toán khó phân biệt chính xác giữa hành vi bình thường và hành vi bất thường.

## 5.1. Dữ liệu mất cân bằng (Imbalanced Data)
Một trong những thách thức phổ biến nhất của anomaly detection là **dữ liệu bị mất cân bằng.**

Trong nhiều bài toán thực tế, dữ liệu bất thường chiếm tỷ lệ rất nhỏ so với dữ liệu bình thường. Ví dụ, trong dữ liệu giao dịch tài chính, các giao dịch gian lận thường chỉ chiếm một phần rất nhỏ trong tổng số giao dịch.

Ví dụ:
Normal transactions: 99.9%

Fraudulent transactions: 0.1%

Sự mất cân bằng này có thể khiến các mô hình machine learning truyền thống tập trung quá nhiều vào lớp dữ liệu chiếm đa số (dữ liệu bình thường). Kết quả là mô hình có thể **đạt độ chính xác rất cao** nhưng lại **không phát hiện được anomaly.**

Ví dụ, nếu một mô hình dự đoán tất cả giao dịch đều là normal, nó vẫn có thể đạt độ chính xác 99.9% trong ví dụ trên, mặc dù hoàn toàn không phát hiện được bất kỳ giao dịch gian lận nào.

Để giải quyết vấn đề này, các nhà khoa học dữ liệu thường áp dụng những kỹ thuật như **sampling methods** (ví dụ: oversampling dữ liệu hiếm) hoặc sử dụng các thuật toán chuyên biệt cho anomaly detection nhằm cải thiện khả năng phát hiện các sự kiện hiếm.

 <p align="center">
   <img src="https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/Imbalanced Data.png" style="margin: 0 auto; display: block;"><br/>
     <em>Hình 5.1. Dữ liệu mất cân bằng; nguồn: <a href="https://deepgram.com/ai-glossary/imbalanced-data?utm_source=chatgpt.com
">DeepGram</a> </em>
 </p></em>
 </p>
 
## 5.2. Sự thay đổi của dữ liệu theo thời gian (Evolving Patterns)
Một thách thức khác là các hành vi bất thường **không cố định mà có thể thay đổi theo thời gian.**

Trong machine learning, hiện tượng này thường được gọi là **concept drift**, khi đặc tính thống kê của dữ liệu thay đổi theo thời gian. Khi điều này xảy ra, các mô hình được huấn luyện trên dữ liệu cũ có thể trở nên kém hiệu quả khi áp dụng cho dữ liệu mới.

Ví dụ điển hình là trong **hệ thống phát hiện gian lận tài chính**. Những kẻ gian lận liên tục thay đổi chiến thuật để vượt qua các hệ thống bảo mật. Khi một kiểu gian lận đã bị phát hiện và ngăn chặn, họ thường nhanh chóng tìm ra phương thức mới để thực hiện hành vi đó.

Vì vậy, các hệ thống anomaly detection trong thực tế thường cần:

- theo dõi hiệu suất mô hình liên tục
- cập nhật dữ liệu mới
- huấn luyện lại mô hình định kỳ

để đảm bảo hệ thống vẫn có thể phát hiện anomaly trong môi trường dữ liệu luôn thay đổi.

 <p align="center">
   <img src="https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/Evolving Patternts.png" style="margin: 0 auto; display: block;"><br/>
     <em>Hình 5.2. Sự thay đổi của dữ liệu theo thời gian; nguồn: <a href="https://www.evidentlyai.com/ml-in-production/data-drift?utm_source=chatgpt.com
">EVIDENTLY AI</a> </em>
 </p></em>
 </p>
 
## 5.3. Dữ liệu có nhiều chiều (High-Dimensional Data)

Một khó khăn khác xuất hiện khi dữ liệu có **rất nhiều đặc trưng.**

Ví dụ, một bản ghi giao dịch có thể bao gồm nhiều thuộc tính như:

- số tiền giao dịch
- thời gian giao dịch
- vị trí địa lý
- loại thiết bị sử dụng
- lịch sử hành vi của người dùng
- phương thức thanh toán
  
Khi số lượng đặc trưng tăng lên, việc phát hiện anomaly trở nên khó khăn hơn do hiện tượng được gọi là **“curse of dimensionality”**.

Trong không gian dữ liệu nhiều chiều, khoảng cách giữa các điểm dữ liệu trở nên ít ý nghĩa hơn. Các điểm dữ liệu có thể trông gần như cách xa nhau tương tự nhau, khiến các thuật toán khó phân biệt đâu là dữ liệu bình thường và đâu là anomaly.

Ngoài ra, dữ liệu nhiều chiều cũng làm tăng độ phức tạp tính toán và có thể chứa các đặc trưng không liên quan hoặc nhiễu, làm giảm hiệu quả của mô hình.

Để khắc phục vấn đề này, các nhà khoa học dữ liệu thường sử dụng các kỹ thuật như **feature selection** (chọn đặc trưng quan trọng) hoặc **dimensionality reduction** như Principal Component Analysis (PCA).

 <p align="center">
   <img src="https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/High-Dimensional Data.png" style="margin: 0 auto; display: block;"><br/>
     <em>Hình 5.3. Dữ liệu có nhiều chiều</em>; nguồn: <a href="https://builtin.com/data-science/curse-dimensionality?utm_source=chatgpt.com
">BuiltIn</a> </em>
 </p></em>
 </p>

## 5.4. Yêu cầu phát hiện theo thời gian thực (Real-Time Detection)
Trong nhiều ứng dụng thực tế, hệ thống anomaly detection cần phải hoạt động **theo thời gian thực.**

Ví dụ, trong các hệ thống phát hiện gian lận thẻ tín dụng hoặc hệ thống an ninh mạng, anomaly cần được phát hiện ngay lập tức để tránh thiệt hại. Nếu việc phát hiện diễn ra quá muộn, hậu quả có thể rất lớn.

Hãy tưởng tượng một trường hợp thẻ tín dụng được sử dụng ở hai địa điểm cách xa nhau trong một khoảng thời gian rất ngắn. Hành vi bất thường này có thể là dấu hiệu của gian lận, và hệ thống cần nhanh chóng gắn cờ giao dịch đó để kiểm tra thêm.

Do đó, các hệ thống anomaly detection phải cân bằng giữa **độ chính xác, tốc độ xử lý và chi phí tính toán**, đặc biệt khi làm việc với dữ liệu lớn hoặc dữ liệu dạng streaming.

 <p align="center">
   <img src="https://github.com/EvelynMyNguyen210/Warmup02_Blog1/blob/main/Collection/Real-Time Detection.png" style="margin: 0 auto; display: block;"><br/>
     <em>Hình 5.4. Yêu cầu phát hiện theo thời gian thực</em>; nguồn: <a href="https://aws.amazon.com/blogs/machine-learning/simplify-and-automate-anomaly-detection-in-streaming-data-with-amazon-lookout-for-metrics/?utm_source=chatgpt.com">AWS</a> </em>
 </p></em>
 </p>

# 6. Kết luận

Hành trình giải mã dữ liệu không chỉ là đi tìm sự tương đồng, mà còn là học cách lắng nghe những tín hiệu khác biệt. **Anomaly Detection** chính là "đôi mắt tinh tường" giúp các hệ thống Data Science không bỏ lỡ những khoảnh khắc quan trọng, từ việc ngăn chặn một cú lừa đảo tài chính tinh vi đến việc bảo vệ hạ tầng mạng trước các cuộc tấn công ẩn mình. 

Dù bạn sử dụng các phương pháp thống kê truyền thống hay các kiến trúc máy học hiện đại, mục tiêu cuối cùng vẫn là biến những "điểm đen" bất thường thành những quyết định chính xác. Nhưng làm thế nào để mang những lý thuyết này vào thế giới thực đầy biến động của ngành tài chính? Ở bài viết tiếp theo (**Blog 2**), chúng ta sẽ cùng "thực chiến" với các kỹ thuật máy học chuyên biệt để xây dựng hệ thống phát hiện gian lận (Fraud Detection) cho ngân hàng. Đừng bỏ lỡ nhé!

# References
Aggarwal, C. C. (2017). Outlier analysis (2nd ed.). Springer.

Chandola, V., Banerjee, A., & Kumar, V. (2009). Anomaly detection: A survey. ACM Computing Surveys, 41(3), 1–58. https://doi.org/10.1145/1541880.1541882

Goodfellow, I., Bengio, Y., & Courville, A. (2016). Deep learning. MIT Press.



















