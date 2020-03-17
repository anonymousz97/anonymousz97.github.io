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
<p align="center">*Tổng quan mô hình Transformer cho bài toán dịch máy*</p>

Đi sâu hơn ta sẽ có mô hình Seq2Seq thường sẽ có 2 lớp trong đó lớp đầu tiên là lớp mã hóa (Encoder) để mã hóa chuỗi đầu vào sang chiều không gian khác và lớp thứ 2 là lớp giải mã (Decoder) nhận kết quả của lớp mã hóa để giải mã trở lại hoặc ra một chuỗi đầu ra.

![](https://i.imgur.com/HlSRR2A.png)
<p align="center">*Encoder và Decoder trong Transformer*</p>






