# PAPER a Day : 

## Generative Adversarial Networks (https://arxiv.org/pdf/1406.2661.pdf)

GAN được coi như là một trong những phát kiến vĩ đại nhất trong thập kỉ qua của Deep Learning. Tác giả nói đây là một framework thông qua việc huấn luyện đồng thời 2 models : model sinh G sẽ cố gắng mô phỏng được phân bố của dữ liệu còn model $D$ là mô hình phân biệt sẽ cố gắng phân biệt data được sinh ra do model sinh G hay là từ data thật.

Các bạn có thể hiểu đơn giản như mô hình sau : 


![](https://i.imgur.com/UnC2Afq.jpg)

Ở hình trên thì có thể coi như model sinh G như tên trộm luôn kiếm cách tạo ra tiền giả để đem đi lừa còn model $D$ sẽ như cảnh sát sẽ luôn luôn tìm cách để phân biệt được tiền được tạo ra từ kẻ trộm hay là tiền thật. Mỗi khi tên cảnh sát cố gắng phân biệt được sự khác biệt giữa tiền thật và tiền giả do tên trộm tạo ra thì tên trộm lại nhìn vào cảnh sát để có thể cải tiến khả năng tạo ra tiền giả tinh vi hơn để cảnh sát khó có thể nhận ra hơn. Ý tưởng này là ý tưởng cho quá trình huấn luyện của GAN. Cho đến một thời điểm lý tưởng thì tiền do kẻ trộm tạo ra sẽ rất giống với tiền thật và lúc đó thì cảnh sát không thể phân biệt được giữa tiền giả và tiền thật (hay xác suất mà cảnh sát dự đoán sẽ tiến tới 0,5 .

Đó cũng là bắt đầu cho một định lý ở trong paper : 
>  In the space of arbitrary functions G and D, a unique solution exists, with G recovering the training data distribution and D equal to 1/2 everywhere. 

## Giới thiệu
2 mô hình trong GAN : 
- Generator : tạo ra G(z) sao cho D không thể phân biệt được so với real data.
- Discriminator : phân biệt giữa G(z) và real data.

Trong paper thì cả Generator (G) và Discriminator (D) đều sử dụng Multi Layer Perceptron. Đây như trò chơi 2 người với mỗi người mỗi vai trò khác nhau mà công thức dưới đây sẽ biểu diễn cho việc đó : 

![](https://i.imgur.com/OtlWf3Q.png)

Dễ thấy công thức ở trên có vẻ khó hiểu nhưng thực ra không khó hiểu. Đầu tiên ta xét nhiệm vụ của D là phải làm tối ưu giữa việc phân biệt dữ liệu thật và dữ liệu fake. Vậy nên công thức ở trên ta đặt $V(D,G)$ là giá trị $D(real)*log(D(real))+D(fake)*log(1-D(G(z)))$ trong đó $D(real)$ và $D(G(z))$ là giá trị mà model $D$ nhận biết được data fake hay real. Vì nhiệm vụ là tối ưu sự khác biệt giữa data real và fake do đó model $D$ được gán giá trị là làm tối ưu $V(D,G)$. Ngược lại thì ta nhận thấy model $G$ là mô hình sinh dữ liệu cần làm thế nào để fake data tốt nhất, nó sẽ làm mọi cách để khiến model $D$ giảm khả năng phân biệt => hay cũng như là tăng khả năng biểu diễn phân bố dữ liệu tốt hơn. Vậy nên ở đây vai trò của model $G$ như thể tối thiểu giá trị của model $D$ hay là $min(max(V(D,G)))$. Tuy nhiên dễ nhận thấy là minimize $G$ thì không liên quan đến phần đầu của phương trình trên do đó ta chỉ quan tâm phần còn lại của phương trình là phần chứa $log(1-D(G(z)))$.

Hình tiếp theo sẽ mô tả quá trình huấn luyện của GAN.

![](https://i.imgur.com/592F1nv.png)
Dễ dàng nhận thấy ban đầu ở hình (a) khi mà mô hình G chưa được huấn luyện thì việc nó biểu diễn lại phân bố của x từ phân bố ngẫu nhiên z ban đầu thì sẽ tạo ra giá trị như đường kẻ màu xanh lá cây trong khi phân bố dữ liệu thật là giá trị đường chấm màu đen. Dễ nhận thấy rằng khi mới training thì model sinh G rất kém do đó việc nhận ra dữ liệu giả và dữ liệu thật rất dễ do đó $D(G(z))$ rất dễ đạt giá trị 0 do đó $log(1-D(G(z)))$ sẽ dễ dàng đạt giá trị 0 khiến cho mô hình rất khó để có thể học. Từ đó người ta sử dụng một trick đó là thay vì việc ta tối thiểu(minimize) giá trị $log(1-D(G(z)))$ thì ta sẽ tìm maximize của $log(D(z))$. Hàm này có ý nghĩa tương tự nhưng lại đem lại gradients nhiều hơn trong quá trình bắt đầu training.

Trở lại với hình trên thì ta sẽ có đường kẻ màu xanh lá cây đại diện cho $G(z)$ (khi map giá trị ở $z$ sang $x$) , đường kẻ màu đen là x (real data) và đường kẻ màu xanh nước biển là đường phân biệt phân bố dữ liệu của $D(x)$. Sau đó ta huấn luyện model $D$ như ở hình (b) ta thấy đường màu xanh nước biển đã phân biệt tốt hơn. Tiếp đến ta huấn luyện model $G$ hình (c) ta sẽ thấy phân bố của $z$ đã tiến gần đến với phân bố của dữ liệu $x$ hơn(giá trj gradients sẽ dẫn $G(z)$ hay là $p_{z}$ tiến gần hơn đến $p_{x}$) . Quá trình cập nhật như thế này sẽ diễn ra liên tục để đến hình (d) thì khi mà gần như đường màu xanh lá cây trùng với đường chấm đen thì đường màu xanh sẽ thẳng và đạt giá trị 0,5 (khi $p_{x}=p_{g}$).

## Thuật toán training GAN sử dụng trong paper : 

![](https://i.imgur.com/uOu4Wbh.png)

Ở đây thì vòng for đầu tiên là theo iterations để finish 1 lần full dữ liệu. vòng for thứ 2 là for theo k trong đó k là số lần chạy update model $D$ rồi mới update 1 lần model $G$ (trong paper lấy là 1). Một tips là thường giá trị k sẽ không bằng một vì ta sẽ không muốn vấn đề training model $G$ liên tục song hành với model $D$ (sẽ giải thích sau). Tiếp theo chọn ra sample 1 minibatch chứa m phần tử noise từ tập $p_{g}(z)$ và từ tập real data $p_{data}(x)$ xong rồi update như công thức trên. Sau khi chạy xong k lần đó thì mới update model $G$ một lần theo cách tương tự và cứ như thế cho đến khi end vòng lặp.

Giải thích vấn đề tại sao khi global optimal $p_{g}=p_{data}$ thì giá trị của model $D$ sẽ ra 1/2.

Với hàm biểu diễn $G$ được fixed thì việc tối ưu $D$ như sau : 

![](https://i.imgur.com/LC14TOx.png)


Ta có $max V(G,D)$ sẽ là : 
![](https://i.imgur.com/4Bro5iY.png)
Ta cộng được như trên do ta đã giả sử là đạt global optimal là $G(z)$ có phân bố gần tương tự $x$. Và phương trình $a*logy+b*log(1-y)$ đạt max bằng $\frac{a}{a+b}$ hay bằng 1/2 (do $p_{g}=p_{data}$).

## Một số lưu ý : 
- Mô hình Generator được xây dựng sử dụng relu và sigmoid là activations.
- Mô hình Discriminator sử dụng max-out activations(Dropout trong quá trình training).
- Giá trị noise chỉ được thêm vào ở lớp đầu tiên chứ không được thêm vào các lớp sau.

## Advantage và Disadvantage : 
Disadvantages : 
- Không có biểu diễn cụ thể của $p_{g}(x)$.
- Training $D$ và $G$ cần phối hợp tốt với nhau tránh trường hợp training $G$ quá nhiều sẽ khiến mô hình giá trị của $G$ thay đổi liên tục và sẽ biểu diễn nhiều giá trị trên tập $z$ bằng 1 giá trị tập $x$ => không biểu diễn cụ thể được.

Advantages : 
- Không cần Markov Chain.
- Không cần inference.
- Có thể mang lại cách biểu diễn data tốt dưới cả những phân bố khó mà ML không thực hiện tốt.







