# 2. Mô tả tập dữ liệu
## 2.1 Nguồn dữ liệu
Trong dự án này, nhóm sử dụng bộ dữ liệu **PaySim – Mobile Money Transactions**, được công bố trên nền tảng Kaggle. Đây là một dataset mô phỏng các giao dịch tài chính di động, được xây dựng dựa trên dữ liệu thực tế nhưng đã được xử lý và tái tạo nhằm phục vụ cho nghiên cứu.

PaySim được phát triển dựa trên hành vi giao dịch trong hệ thống Mobile Money tại châu Phi, với quy mô lên đến hàng triệu bản ghi. Nhờ kích thước lớn và cấu trúc rõ ràng, dataset này rất phù hợp để xây dựng và kiểm thử các mô hình Machine Learning, đặc biệt trong bối cảnh phát hiện gian lận – nơi mà dữ liệu thực thường rất khó tiếp cận do yếu tố bảo mật.

## 2.2 Cấu trúc dữ liệu
Mỗi dòng trong dataset đại diện cho một giao dịch tài chính cụ thể. Dữ liệu bao gồm nhiều thông tin quan trọng như thời gian thực hiện giao dịch, loại giao dịch, số tiền cũng như trạng thái tài khoản của cả người gửi và người nhận trước và sau giao dịch.

Ví dụ, một vài dòng dữ liệu đầu tiên có thể được biểu diễn như sau:

#### Ví dụ dữ liệu (Sample Transactions)

Dưới đây là một số dòng dữ liệu tiêu biểu được trích từ dataset PaySim nhằm minh hoạ cấu trúc của một giao dịch:

| step | type     | amount   | nameOrig     | oldbalanceOrg | newbalanceOrig | nameDest     | oldbalanceDest | newbalanceDest | isFraud | isFlaggedFraud |
|------|----------|----------|--------------|----------------|----------------|--------------|----------------|----------------|--------|----------------|
| 1    | PAYMENT  | 9839.64  | C1231006815  | 170136.0       | 160296.36      | M1979787155  | 0.0            | 0.0            | 0      | 0              |
| 1    | PAYMENT  | 1864.28  | C1666544295  | 21249.0        | 19384.72       | M2044282225  | 0.0            | 0.0            | 0      | 0              |
| 1    | TRANSFER | 181.00   | C1305486145  | 181.0          | 0.0            | C553264065   | 0.0            | 0.0            | 1      | 0              |
| 1    | CASH_OUT | 181.00   | C840083671   | 181.0          | 0.0            | C38997010    | 21182.0        | 0.0            | 1      | 0              |
| 1    | PAYMENT  | 11668.14 | C2048537720  | 41554.0        | 29885.86       | M1230701703  | 0.0            | 0.0            | 0      | 0              |

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

# 3. Exploratory Data Analysis (EDA)

EDA - Phân tích dữ liệu thăm dò là quy trình kiểm tra, trực quan hóa và tóm tắt các đặc điểm chính của bộ dữ liệu thô. EDA giúp hiểu rõ cấu trúc, phát hiện lỗi, outlier (điểm ngoại lai) và các mối quan hệ giữa các biến trước khi xây dựng mô hình.

Tiến hành khảo sát dữ liệu cơ bản, ta nhận thấy dữ liệu có các đặc điểm:
- Tổng kích thước của dataset là (6362620, 11)
- Dữ liệu không chứa giá trị NaN
- Có hai cột chứa dữ liệu dạng object là type, nameOrig, nameDest

```python
df = pd.read_csv(file_path)
df.shape
df.isnull().sum()
df.info()
```
```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6362620 entries, 0 to 6362619
Data columns (total 11 columns):
 #   Column          Dtype  
---  ------          -----  
 0   step            int64  
 1   type            object 
 2   amount          float64
 3   nameOrig        object 
 4   oldbalanceOrg   float64
 5   newbalanceOrig  float64
 6   nameDest        object 
 7   oldbalanceDest  float64
 8   newbalanceDest  float64
 9   isFraud         int64  
 10  isFlaggedFraud  int64  
dtypes: float64(5), int64(3), object(3)
```

Với kích thước (6362620, 11), ta có thể nhận thấy đây là bộ dữ liệu không quá lớn nhưng cũng chứa nhiều thông tin và đặc trưng, vì vậy ta cần thực hiện phân tích đối với từng đặc trưng để tìm hiểu mối quan hệ giữa các đặc trưng.

## 3.1. Phân tích cách thức giao dịch - transaction type

Tiến hành khảo sát với các giao dịch được đánh dấu là Fraud và Valid, ta kiểm tra những hình thức giao dịch nào được xem là valid và giao dịch nào được xem là fraud.

```python
# Split data into Fraud and Valid
data_new = df.copy()

fraud = data_new[data_new["isFraud"] == 1]
valid = data_new[data_new["isFraud"] == 0]

print("Fraud transactions by type: \n",fraud.type.value_counts())
print("\n Valid transactions by type: \n",valid.type.value_counts())
```

Ta thu được kết quả:
```python
Fraud transactions by type: 
 type
CASH_OUT    4116
TRANSFER    4097
Name: count, dtype: int64

 Valid transactions by type: 
 type
CASH_OUT    2233384
PAYMENT     2151495
CASH_IN     1399284
TRANSFER     528812
DEBIT         41432
Name: count, dtype: int64
```

Qua khảo sát, ta nhận thấy trong bộ dữ liệu có những loại hình giao dịch: CASH_IN, CASH_OUT, PAYMENT, TRANSFER và DEBIT. Trong đó hai hình thức giao dịch được đánh giá là fraud gồm CASH_OUT và TRANSFER. Do đó, ta sẽ tập trung phân tích fraud và valid data ở hai hình thức giao dịch này.

```python
# Focus on TRANSFER and CASH_OUT
valid = valid[(valid["type"] == "CASH_OUT")| (valid["type"] == "TRANSFER")]
data_new = data_new[(data_new["type"] == "CASH_OUT") | (data_new["type"] == "TRANSFER")]
```

## 3.2. Phân tích số dư tài khoản - balance

Một trong những thông tin quan trọng phản ánh một giao dịch có là hợp pháp hay đáng ngờ đó là giá trị số dư tài khoản sau giao dịch. Ví dụ: Một giao dịch bình thường sẽ có số dư bằng hiệu của số dư trước đó và giá trị giao dịch. Ta tiến hành tính toán sai số giao dịch của tài khoản gửi (được mô tả bằng tên Orig trong biến) và sai số giao dịch của tài khoản nhận (được mô tả bằng tên Dest trong biến):

```python
data_new['errorOrig'] = data_new['oldbalanceOrg'] - data_new['amount'] - data_new['newbalanceOrig']
data_new['errorDest'] = data_new['oldbalanceDest'] + data_new['amount'] - data_new['newbalanceDest']

# Analyze these values to see the distribution of non 0 and 0 cases
# Define threshold for "zero"
threshold = 1e-6

# Origin
data_new['isErrorOrig'] = (~np.isclose(data_new['errorOrig'], 0, atol=threshold)).astype(int)

# Destination
data_new['isErrorDest'] = (~np.isclose(data_new['errorDest'], 0, atol=threshold)).astype(int)

print("Origin Error Counts:")
print(data_new['isErrorOrig'].value_counts(normalize=True) * 100)

print("\nDestination Error Counts:")
print(data_new['isErrorDest'].value_counts(normalize=True) * 100)
```
Kết quả nhận được:
```python
Origin Error Counts:
isErrorOrig
1    90.518079
0     9.481921
Name: proportion, dtype: float64

Destination Error Counts:
isErrorDest
0    71.075859
1    28.924141
Name: proportion, dtype: float64
```
Qua khảo sát ta nhận thấy error xảy ra thường xuyên với Origin, cả fraud và non-fraud đều có sai số về balance, vì vậy có thể nói hầu hết các trường hợp đều đáng ngờ, do đó nếu sử dụng Origin có thể sẽ không phản ánh đúng thực tế về tương quan giữa giao dịch fraud và non-fraud.

Trong khi đó, với Destination, chỉ có 29% trường hợp có sai số về balance. Ta nhận định khi sử dụng Destination để khảo sát có thể dễ nhận biết các giao dịch đáng ngờ hơn.

Tiếp đến ta xét các trường hợp có error và non-error với các nhãn fraud và non-fraud:

```python
pd.crosstab(data_new['isErrorDest'], data_new['isFraud'], normalize='columns') * 100
pd.crosstab(data_new['isErrorOrig'], data_new['isFraud'], normalize='columns') * 100
```

Kết quả được trả về hai bảng sau:

| isErrorDest | isFraud=0 | isFraud=1 |
| :---------: | :-------: | :--------: |
| 0 | 71.166528 | 40.582004 |
| 1 | 28.833472 | 59.417996 |

| isErrorOrig | isFraud=0 | isFraud=1 |
| :---------: | :-------: | :--------: |
| 0 | 9.214408 | 99.452088 |
| 1 | 90.785592 | 0.547912 |

**Xét biến isErrorDest:**
- Với error = 1:
  * Fraud: 59% -> Giao dịch fraud có tỉ lệ tồn tại error cao hơn
  * Non-fraud: 29% 

- Với error = 0:
  * Fraud: 40%
  * Non-fraud: 71% -> Giao dịch normal có tỉ lệ ít xảy ra error hơn

*Qua phân tích cho thấy, các giao dịch được đánh giá là fraud thường đi kèm với hiện tượng tồn tại sai số giao dịch ở Destination. Tỉ lệ xảy ra sai số trong trường hợp giao dịch là fraud cũng cao gấp đôi tỉ lệ sai số trong trường hợp là một giao dịch bình thường.*

-> Điều đó cho thấy isErrorDest là một feature tốt có thể dùng để phân biệt các giao dịch bình thường và bất thường. isErrorOrig=1 tương ứng với giao dịch fraud, isErrorOrig=0 tương ứng với giao dịch non-fraud.

**Xét biến isErrorOrig:**
- Với error = 1:
  * Fraud: 99% -> Giao dịch fraud hầu như không có error
  * Non-fraud: 0.5%

- Với error = 0:
  * Fraud: 90% -> Giao dịch bình thường tồn tại error cao hơn
  * Non-fraud: 9% 

*Qua phân tích cho thấy, với giao dịch fraud, sai số giao dịch gần như không có, số dư giao dịch luôn đúng. Trong khi đó với giao dịch non-fraud, sai số giao dịch có tỉ lệ cao. Từ đó ta có thể giả định rằng, với giao dịch fraud, sai số tài khoản được đã được điều chỉnh để tránh bị phát hiện (trong trường hợp ta dựa vào sai số tài khoản sau giao dịch). Với giao dịch non-fraud, sai số tài khoản cao.*

-> Điều này cho thấy isErrorOrig là một feature tiềm năng có thể sử dụng để phân biệt các giao dịch bình thường và bất thường. isErrorOrig=1 tương ứng giao dịch non-fraud, isErrorOrig=0 tương ứng giao dịch fraud.

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Fraud_Rate_by_Destination_Error.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Hình 3.1. Xác suất giao dịch là fraud khi có Destination error</em>
</p>

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Fraud_Rate_by_Origin_Error.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Hình 3.2. Xác suất giao dịch là fraud khi có Origin error</em>
</p>

**Kết luận:** isErrorOrig và isErrorDest sẽ được thêm vào dataset để làm đặc trưng.

## 3.3. Phân tích biến isFlaggedFraud

isFlaggedFraud mô tả các giao dịch vượt 200,000 đơn vị sẽ bị đánh giá là fraud, đây là đặc trưng dựa trên quy tắc của hệ thống (một mô phỏng của bộ dữ liệu này). Ta xét trong bộ dữ liệu có bao nhiêu giao dịch được đánh giá là fraud dựa trên quy tắc này:

```python
# Check how many observations are flagged as Fraud
flagged = data_new[data_new["isFlaggedFraud"] == 1]
flagged_correctly = sum(flagged["isFraud"] == 1)
flagged_wrongly = len(flagged) - flagged_correctly
total = flagged_correctly + flagged_wrongly

print(flagged_correctly," observations were flagged correctly and ", flagged_wrongly, \
      " observations were flagged wrongly for a total of ", total, " flagged observations.")

# Check how many observations where the transaction is fraudulent, the transaction is a transfer and the amount is greater than 200, 000 are in the dataset
should_be_flagged = fraud[(fraud["amount"] > 200000) & (fraud["type"] == "TRANSFER")]
print("Number of observations that should be flagged: ", len(should_be_flagged))
```

Kết quả của đoạn code trên:
```python
16 observations were flagged correctly and 0 observations were flagged wrongly for a total of  16  flagged observations.
Number of observations that should be flagged: 2740
```

Sau khảo sát ta thấy rằng chỉ có 16 trường hợp được đánh giá đúng, trong khi đó có 2740 giao dịch có giá trị chuyển khoản vượt 200,000 đơn vị nhưng chưa được gán nhãn bởi hệ thống.

**Kết luận:** isFlaggedFraud không phản ánh đúng tính chất của các giao dịch. Do đó đây không phải một đặc trưng quan trọng trong bộ dữ liệu đang xét, ta sẽ drop cột này.

## 3.4. Phân tích biến nameOrig và nameDest

nameOrig và nameDest biểu thị cho tên tài khoản gửi tiền và tài khoản nhận tiền. Qua khảo sát, ta thấy cột nameOrig và nameDest không cung cấp thông tin quan trọng cho bộ dữ liệu, do đó ta sẽ loại bỏ hai cột này.

## 3.5. Phân tích biến amount và step

Với amount, ta xét phân phối của amount với trường hợp fraud và non-fraud:

```python
sns.boxplot(x='isFraud', y='amount', data=data_new)
plt.yscale('log')
plt.title('Amount vs Fraud')
plt.show()
```
<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Amount_vs_fraud.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Hình 3.3. Phân phối của amount đối với fraud và non-fraud</em>
</p>

Qua biểu đồ ta thấy amount giao dịch dao động trong khoảng từ 10<sup>5</sup> đến 10<sup>7</sup>. Trong trường họp fraud, phân phối giá trị giao dịch có xu hướng lớn hơn so với non-fraud, tuy nhiên vẫn có sự giao giữa hai tập giá trị, trong đó vẫn tồn tại các giao dịch fraud ở mức amount tương đương với giao dịch non-fraud. Do đó không có sự khác biệt lớn giữa fraud và non-fraud về giá trị giao dịch.

Tiến hành khảo sát giao dịch fraud và valid theo thời gian, ta thấy hiện tượng fraud diễn ra đều theo thời gian, trong đó vào khoảng step từ 0 đến 100, khoảng 400 có sự nhảy vọt nhẹ về số lượng giao dịch fraud.

Đối với giao dịch valid, ta thấy có tỉ lệ lớn các giao dịch valid được thực hiện ở khoảng 0 đến 60 và khoảng 100 đến 400.

```python
bins = 50

valid.hist(column="step",color="green",bins=bins)
plt.xlabel("1 hour time step")
plt.ylabel("# of transactions")
plt.title("# of valid transactions over time")

fraud.hist(column ="step",color="red",bins=bins)
plt.xlabel("1 hour time step")
plt.ylabel("# of transactions")
plt.title("# of fraud transactions over time")

plt.tight_layout()
plt.show()
```

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/%23_of_valid_transactions_over_time.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Hình 3.4. Phân phối giao dịch hợp pháp theo thời gian</em>
</p>

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/%23_of_fraud_transactions_over_time.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Hình 3.5. Phân phối giao dịch fraud theo thời gian</em>
</p>

Để khảo sát rõ hơn về thời gian xảy ra các giao dịch fraud, ta sẽ chuyển đổi đơn vị về giờ (hour) và tìm các biểu hiện trong các khung giờ trong một ngày:

```python
data_new['hour'] = data_new['step'] % 24
sns.barplot(x='hour', y='isFraud', data=data_new)
plt.title('Fraud Rate by Hour')
plt.show()
```

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Fraud_rate_by_Hour.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Hình 3.6. Phân phối giao dịch fraud theo thời gian (24 giờ)</em>
</p>

Biểu đồ cho thấy các giao dịch fraud diễn ra chủ yếu vào khoảng từ 2 giờ sáng đến 8 giờ sáng. Do đó đây là một feature hiệu quả để đánh giá một giao dịch là fraud hay valid.

Qua khảo sát cho thấy amount vẫn là feature quan trọng để xác định một giao dịch là fraud hay non-fraud khi phần lớn các giao dịch fraud thường diễn ra với giá trị amount lớn. Với step, khi khảo sát ở mức độ tổng quan ta không thấy được nhiều insight, tuy nhiên khi quy đổi về đại lượng 24 giờ, ta có thể thấy rõ xu hướng của các giao dịch fraud, do đó ta sẽ bổ sung thêm cột hour để làm feature và bỏ cột step.

|         |     type |     amount | oldbalanceOrg | newbalanceOrig | oldbalanceDest | newbalanceDest | isFraud |  errorOrig |     errorDest | isErrorOrig | isErrorDest | hour |
|---------|---------:|-----------:|--------------:|---------------:|---------------:|---------------:|--------:|-----------:|--------------:|------------:|------------:|-----:|
|    2    | TRANSFER |     181.00 |        181.00 |            0.0 |           0.00 |           0.00 |       1 |       0.00 |  1.810000e+02 |           0 |           1 |    1 |
|    3    | CASH_OUT |     181.00 |        181.00 |            0.0 |       21182.00 |           0.00 |       1 |       0.00 |  2.136300e+04 |           0 |           1 |    1 |
|    15   | CASH_OUT |  229133.94 |      15325.00 |            0.0 |        5083.00 |       51513.44 |       0 | -213808.94 |  1.827035e+05 |           1 |           1 |    1 |
|    19   | TRANSFER |  215310.30 |        705.00 |            0.0 |       22425.00 |           0.00 |       0 | -214605.30 |  2.377353e+05 |           1 |           1 |    1 |
|    24   | TRANSFER |  311685.89 |      10835.00 |            0.0 |        6267.00 |     2719172.89 |       0 | -300850.89 | -2.401220e+06 |           1 |           1 |    1 |
|   ...   |      ... |        ... |           ... |            ... |            ... |            ... |     ... |        ... |           ... |         ... |         ... |  ... |
| 6362615 | CASH_OUT |  339682.13 |     339682.13 |            0.0 |           0.00 |      339682.13 |       1 |       0.00 |  0.000000e+00 |           0 |           0 |   23 |
| 6362616 | TRANSFER | 6311409.28 |    6311409.28 |            0.0 |           0.00 |           0.00 |       1 |       0.00 |  6.311409e+06 |           0 |           1 |   23 |
| 6362617 | CASH_OUT | 6311409.28 |    6311409.28 |            0.0 |       68488.84 |     6379898.11 |       1 |       0.00 |  1.000000e-02 |           0 |           1 |   23 |
| 6362618 | TRANSFER |  850002.52 |     850002.52 |            0.0 |           0.00 |           0.00 |       1 |       0.00 |  8.500025e+05 |           0 |           1 |   23 |
| 6362619 | CASH_OUT |  850002.52 |     850002.52 |            0.0 |     6510099.11 |     7360101.63 |       1 |       0.00 |  9.313226e-10 |           0 |           0 |   23 |

<p align="center">
  <em>Bảng 3.1 Dataset sau khi xử lí</em>
</p>

# 4. Data Preprocessing
## 4.1. Data Encoding

Trước khi đưa data vào train model, các giá trị feature cần đổi về giá trị số. Trong dataset hiện tại, cột type là cột duy nhất chứa kí tự chuỗi (CASH_OUT, TRANSFER). Ở đây ta sẽ sử dụng kĩ thuật one hot encoding để mã hóa dữ liệu, biến dữ liệu cột type thành dữ liệu số.

```python
# Seperate feature (X) and target (y)
X = data_new.drop(columns=['isFraud'])
y = data_new['isFraud']

# Apply one hot encoding to type feature
X = pd.get_dummies(X, columns=['type'], drop_first=True)
```

## 4.2. Train-test split

Trước khi tiến hành training, ta chia dataset thành 2 bộ: train và test. Bước này đảm bảo khả năng khái quát hóa của mô hình trên dữ liệu mới, đồng thời ngăn chặn hiện tượng quá khớp (overfitting). Điều này đảm bảo mô hình học được bản chất quy luật, thay vì chỉ ghi nhớ dữ liệu.

Ta sẽ chia 80% dữ liệu cho tập train và 20% còn lại cho tập test. Ta cũng thiết lập stratify=y để phân phối đều các giá trị của y trong cả 2 tập. Điều này là cần thiết vì bộ dữ liệu hiện tại mang đặc tính imbalance (tỉ lệ fraud 0.1%), khi đó các giá trị y tồn tại trong tập test có thể không chưa trường hợp fraud nào.

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y,
    test_size=0.2,       # 80% train, 20% test
    random_state=42,
    stratify=y           # VERY IMPORTANT for imbalance
)
```

## 4.3. Xử lí imbalance

Vì bộ dữ liệu hiện tại mang tính imbalance lớn, nếu không xử lí thì mô hình sẽ học sai và không nhận biết được fraud. Vì vậy ta cần một kĩ thuật oversampling để tăng cường dữ liệu.

SMOTE (Synthetic Minority Over-sampling Technique) là một kỹ thuật trong học máy dùng để xử lý dữ liệu bị mất cân bằng bằng cách tạo ra các mẫu nhân tạo (tổng hợp) cho lớp thiểu số. Thay vì sao chép dữ liệu, SMOTE nội suy giữa các điểm dữ liệu lân cận, giúp mô hình học tốt hơn và giảm quá khớp (overfitting).

Ta sẽ sử dụng thuật toán SMOTE cho tập train:

```python
from imblearn.over_sampling import SMOTE

smote = SMOTE(random_state=42)
X_train_res, y_train_res = smote.fit_resample(X_train, y_train)
```



