# Giải thích mô hình transformer thông qua bài báo [Attention is All you need](https://arxiv.org/pdf/1706.03762.pdf)

## 1. Giới thiệu : 
Mô hình Transformer được giới thiệu như một trong những kiến trúc Seq2Seq (Sequence2Sequence). Và Seq2Seq là kiến trúc nhận vào một chuỗi (1 sequence) và đưa ra kết quả là 1 chuỗi tương ứng. Có vẻ giống như cách thức hoạt động của kiến trúc này đúng như cái tên nó :blush:

Một số mô hình Seq2Seq phổ biến : LSTM,GRU,LSTM,Transformer,...

Quay lại Transformer, bài báo [Attention is All you need](https://arxiv.org/pdf/1706.03762.pdf) đã đề cập đến và đã có code mẫu ở link sau (do Harvard's NLP group) : [**Link**](http://nlp.seas.harvard.edu/2018/04/03/attention.html) hoặc code Transformer áp dụng vào bài toán dịch ngôn ngữ (trang chủ tensorflow) [**Link**](https://www.tensorflow.org/tutorials/text/transformer).

Đây là mô hình dựa hoàn toàn vào self-attention để tính toán ra được biểu diễn của chuỗi đầu vào và đầu ra mà không sử dụng đến tuần tự (như RNN,LSTM hay CNN).

Tuy nhiên mình sẽ mô tả và giải thích đơn giản hơn về mô hình trong bài báo để mọi người có một góc nhìn trực quan và cho những người chưa có kiến thức trong mảng này có thể hiểu dễ dàng hơn.


## 2. Tổng quan

Hãy nhìn mô hình Transformer như một hộp đen. Ở trong ứng dụng dịch máy thì đầu vào sẽ là 1 câu hoặc 1 đoạn của ngôn ngữ X đi qua hộp đen Transformer và đầu ra sẽ là một câu hoặc 1 đoạn của ngôn ngữ Y.

<div class="center" markdown="0">
  <img src="https://i.imgur.com/DGOky96.png" />
</div>
<p align="center"><b>Tổng quan mô hình Transformer cho bài toán dịch máy</b></p>

Đi sâu hơn ta sẽ có mô hình Seq2Seq thường sẽ có 2 lớp trong đó lớp đầu tiên là lớp mã hóa (Encoder) để mã hóa chuỗi đầu vào sang chiều không gian khác và lớp thứ 2 là lớp giải mã (Decoder) nhận kết quả của lớp mã hóa để giải mã trở lại hoặc ra một chuỗi đầu ra.

![](https://i.imgur.com/HlSRR2A.png)
<p align="center"><b>Encoder và Decoder trong Transformer</b></p>

### 2.1 Mô hình Transformer
Mô hình cụ thể được miêu tả như hình vẽ dưới : 

<div class="center" markdown="0">
  <img src="https://i.imgur.com/5tGoAJ1.png" />
</div>
<p align="center"><b>Kiến trúc mô hình Transformer</b></p>

Ta có thể thấy lớp Encoder gồm N khối (ta tạm gọi là EncoderLayer). Và đầu ra của N khối này sẽ được nối sang bên Decoder. Tương tự với đó là bên Decoder cũng chứa N khối DecoderLayer. Trong bài báo cũng có đề cập đến việc lấy N = 6 (Đương nhiên là bạn có thể thử với N khác tùy).

Đây là hình minh họa đơn giản với N=6 khối.

<div class="center" markdown="0">
  <img src="https://i.imgur.com/A0jwW9r.png" />
</div>
<p align="center"><b>N=6 khối Encoder và Decoder</b></p>

Tất cả các khối Encoder đều là khác nhau,không chia sẻ trọng số. Và được chia ra thành như sau : 
<div class="center" markdown="0">
  <img src="https://i.imgur.com/BtpSaGU.png" />
</div>
<p align="center"><b>Khối Encoder</b></p>

Còn đối với Decoder thì mỗi khối sẽ sử dụng 2 lớp chú ý (Attention Layer) đó là 1 lớp Self-Attention như bên Encoder và 1 lớp Attention mà trong đó lấy thông tin từ lớp Encoder. Hình dưới đây mô tả khối Decoder.
<div class="center" markdown="0">
  <img src="https://i.imgur.com/iiBgd9p.png" />
</div>
<p align="center"><b>Khối Decoder</b></p>

## 2.2 Embedding và Position Encoding
### 2.2.1 Embedding
Ở mô hình trên thì Input Embeddings và Output Embeddings đều là lớp nhúng để chuyển chuỗi đầu vào thành vector với số chiều là $d$ (Ở trong bài báo còn gọi là $d_{model} = 512$).
### 2.2.2 Position Encoding
Tại vì mô hình sử dụng Self-Attention và không có mối tương quan giữa các vị trí trong chuỗi,do đó nó có thể mất đi tính tuần tự trong câu (ví dụ : đi không và không đi). Do đó người ta đã đưa ra một phương pháp đó là Position Encoding. Position Encoding sẽ thêm vào vector đầu ra của Embedding để tạo ra tính tuần tự cho câu nhưng vẫn sử dụng Self-Attention được.
<div class="center" markdown="0">
  <img src="https://i.imgur.com/FdPJ7vr.png" />
</div>
<p align="center"><b>Position Encoding (Time signal encoding) </b></p>

Công thức của Position Encoding (PE) là : 
$$PE_{pos,2i}=sin(pos/10000^{2i/d_{model}})$$
$$PE_{pos,2i+1}=cos(pos/10000^{2i/d_{model}})$$

Trong đó thì $pos$ là vị trí của từ đó trong chuỗi còn $i$ là vị trí chiều.

Ví dụ :
<div class="center" markdown="0">
  <img src="https://i.imgur.com/opchnh1.png" />
</div>

Giá trị PE của $x_{2}$ ($pos=1$) tại 4 chiều đầu là $0.84,0.0001,0.54,1.$
Giá trị 0.84 được lấy bằng $PE_{1,0}=sin(1/10000^{0/d_{model}})=0.84$ do $2i=0$ vì $i=0$.

Và biểu đồ của PE có giá trị như sau (mỗi hàng tương ứng với PE của 1 vector). Do đó hàng đầu tiên sẽ là giá trị chúng ta sẽ thêm vào từ đầu tiên trong chuỗi. Mỗi hàng sẽ chứa 512 giá trị tương đương với $d=512$ và có giá trị nằm trong khoảng -1 và 1 : 

![](https://i.imgur.com/6xXjg35.png)

Từ đây ta có thể thấy là $PE_{pos}$ có thể biểu diễn được bởi $PE_{pos+k}$.


## 2.3 Encoder
Ở phần này mình sẽ đi sâu vào lớp Encoder gồm Self-Attention và Feed Forward Network.
<div class="center" markdown="0">
  <img src="https://i.imgur.com/oUyq0vu.png" />
</div>
<p align="center"><b>Ví dụ 1 khối EncoderLayer</b></p>

Ta có thể thấy là từng từ $x_{i}$ sẽ đưa vào Self-Attention một cách riêng biệt và đưa ra vector $z_{i}$ là vector self-attention.Vector $z_{i}$ đi qua mạng Feed Forward Network (gồm 2 lớp) 1 lớp chuyển số chiều lên $d_{1}=2048$ sau đó qua lớp 2 lại chuyển số chiều về $d_{model}=512$ (theo bài báo). Đầu ra lại được đưa vào khối EncoderLayer tiếp theo.

Vậy Self-Attention là gì? Làm sao để máy có thể hiểu được Self-Attention?

### 2.3.1 Self-Attention
Cho câu sau : **"The animal didn't cross the street because it was too tired."**

Với câu trên thì câu hỏi đặt ra là từ **"it"** biểu diễn cho từ gì? Nếu như là người đọc thì có thể nhận ra ngay đó là **"it"** biểu diễn cho từ **"The animal"**. Tuy nhiên nếu cho một thằng bé thì có thể nó sẽ ko biết **"it"** biểu diễn cho từ **"the animal"** hay là **"the street"** hay bất cứ từ nào gần nó khác. Do đó Self-Attention sẽ đưa ra mức độ chú ý của từ đó với các từ khác như hình dưới.

![](https://i.imgur.com/aXvz8cr.png)

Chúng ta có thể thấy ở đây thì "it_" có liên kết khá mạnh với "The animal" và đương nhiên là có liên kết với tất cả từ khác tuy nhiên khá yếu.

Self-Attention một cách trực quan : 

Với mỗi đầu vào là vector $x_{i}$ ta sẽ có 3 ma trận $W^{Q},W^{K},W^{V}$ tương ứng với $(Q,K,V)$. Lấy  $x_{i}$ nhân với từng ma trận trên sẽ ra 3 vector $q_{i},k_{i},v_{i}$ tương ứng.

![](https://i.imgur.com/3OJSaJg.png)

Trong bài báo có giải thích cơ chế $(Q,K,V)$ là map đầu vào $Queries$ với các cặp $Keys$ và $Values$. Giá trị chú ý sẽ bằng tổng các cặp $(K,V)$ mà liên quan đến $Q$.

Sau khi đã có $q_{i},k_{i},v_{i}$ thì ta sẽ tính điểm cho từng vị trí một bằng cách lấy $q_{i}$ nhân với từng $k_{x}^{T}$.

![](https://i.imgur.com/u0m1NOY.png)

Trong bài báo có đề cập đến kết quả sau khi nhân $q_{i}*k_{x}^{T}$ đem chia cho $\sqrt(d_{model})$ khi $d_{model}$ nhỏ sẽ như nhau tuy nhiên khi $d_{model}$ lớn thì dot-product $q$ và $k$ sẽ đưa ra kết quả có giá trị lớn hơn và sẽ chia kết quả softmax sang 2 vùng ở 2 cực.

Kết quả sau khi chia sẽ qua lớp softmax để normalize kết quả và có tổng xác suất bằng 1 (tính chất của softmax).
![](https://i.imgur.com/JQnewed.png)
