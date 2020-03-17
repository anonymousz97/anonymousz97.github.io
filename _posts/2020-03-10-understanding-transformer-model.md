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
<p align="center"><b>Ví dụ 1 khối Encoder</b></p>

Ta có thể thấy là từng từ $x_{i}$ sẽ đưa vào Self-Attention một cách riêng biệt và đưa ra vector $z_{i}$