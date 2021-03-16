Link paper : [https://arxiv.org/pdf/1905.11946.pdf](https://arxiv.org/pdf/1905.11946.pdf)

## 1. Giới thiệu
Mạng tích chập (ConvNet) đã thực sự trở thành một trong những mô hình được sử dụng nhiều nhất trong Computer Vision. Đa số các mô hình ConvNet đều đạt được một kết quả khả quan và nhất định với lượng resources (cơ sở vật chất,FLOPS,...) cố định. Do đó nhằm tăng độ chính xác mô hình thì chúng ta thường có 3 hướng sau : 

- Tăng độ sâu của mô hình
- Tăng độ rộng của từng layer trong mô hình
- Cải thiện chất lượng của đầu vào (tăng chất lượng,kích thước ảnh).

Tuy nhiên các cách để scale mô hình bây giờ đa số là chọn một trong 3 hướng trên để tinh chỉnh hoặc thử nghiệm với các số random hay thậm chí là tìm kiếm gridsearch trong một không gian lớn và việc này có thể sẽ khó khăn khi ta cần tối ưu đối với một mạng ConvNet lớn.

=> Bài báo đưa ra một cách tiếp cận mới để nhằm scale mô hình ConvNet đạt kết quả tốt hơn (accuracy) với số lượng params ít hơn, số FLOPS tăng lên (số lượng floating points cần tính toán per seconds).

Ngoài ra tác giả còn đề cập thêm mô hình EfficientNet B0 đến B7 (B0 là base còn B1->B7 là tuning của B0) đã hoàn toàn đánh bại các mô hình ConvNet về performance như hình sau.
<br>
![](https://i.imgur.com/4DMjjPe.png)


Như ta để ý thì với số lượng params ít hơn hẳn so với các mô hình ConvNet tuy nhiên kết quả  của các EfficientNet lại tỏ ra hiệu quả và đạt kết quả tốt trên tập ImageNet.
Ví dụ : EfficientNet-B1 tuy kết quả đạt tốt hơn ResNet-152 tuy nhiên số lượng params lại bé hơn tầm gần 8 lần. Hay EfficientNet-B7 chỉ cần số params bé hơn hẳn nhưng cũng đạt kết quả tương tự với GPipe.

## 2. Cách scale mô hình ConvNet
![](https://i.imgur.com/D9VIxbx.png)

Hình ảnh trên mô tả 3 cách để scale mô hình ConvNet trong đó gồm (b) tăng độ rộng trên từng layer của mô hình , (c) tăng độ sâu của mô hình hay (d) tăng chất lượng ảnh đầu vào nhằm mô hình học được những trích xuất rõ ràng nhất của hình ảnh (hình ảnh rõ nét sẽ dễ dàng đưa ra được đặc trưng hay tính chất của 1 vật). Cách cuối cùng là kết hợp của 3 cách trên với bộ tham số tùy chỉnh.

### Thu phóng theo chiều sâu (Depth Scaling):
Thu phóng theo chiều sâu là một cách thông dụng nhất được sử dụng để thu phóng một mô hình CNN. Với từng mô hình thì việc thu phóng sẽ chỉ đơn giản là thêm hoặc bớt $N$ block (định nghĩa của 1 khối chứa các thao tác trong xử lý ảnh) tương ứng. Ví dụ: ResNets có thể được scale từ Resnet 50 lên Resnet-200 hay Resnet-50 có thể thu nhỏ về Resnet-18 tùy theo nhu cầu. Tuy nhiên tại sao chúng ta lại thu phóng quy mô độ sâu? Trực giác cho chúng ta ý nghĩ rằng một mạng lưới sâu hơn có thể nắm bắt các tính năng phong phú và phức tạp hơn, đồng thời khái quát tốt các tác vụ mới.

Tuy nhiên việc lạm dụng thu phóng theo chiều sâu có thể không cải thiện hiệu quả của mô hình, thậm chí có thể làm mô hình kém hiệu quả hơn so với mô hình ban đầu. Việc tăng chiều sâu đúng là mang lại những kết quả tốt dần lên nhờ vào việc các lớp ẩn sau sẽ học được những pattern để phân loại hay phân biệt các sample với nhau chính xác hơn tuy nhiên là đến 1 thời điểm nào đó thì chính điều đó sẽ phản tác dụng. Mô hình sâu quá sẽ dễ bị rơi vào trạng thái Overfitting (học vẹt) hay thậm chí là bị mắc vấn đề vanishing gradients (một vấn đề dễ bị gặp phải đối với các mô hình nhiều lớp ẩn) khi đi xuống các lớp dưới khiến chúng không được cập nhật nhiều. Do đó không phải lúc nào, thu phóng theo chiều sâu cũng là sự lựa chọn thích hợp để cải thiện mô hình CNN.

### Thu phóng theo chiều rộng (Width Scaling):
Việc thu phóng theo chiều rộng của mạng sẽ khiến với mỗi lớp thì sẽ được biểu diễn bởi nhiều pattern hơn , tại mỗi lớp thì những đặc trưng trích xuất sẽ nhiều hơn => Mô hình cũng có thể đạt kết quả tốt hơn tuy nhiên cũng như thu phóng theo chiều sâu thì thu phóng theo chiều rộng cũng sẽ mang lại những vấn đề về Overfitting. Và việc cân bằng giữa độ sâu và số lượng neurons tại từng lớp ẩn cũng là 1 vấn đề đáng để nghiên cứu.

### Thu phóng theo độ phân giải (Resolution Scaling)
Theo một cách trực quan, chúng ta có thể nói rằng trong một hình ảnh có độ phân giải cao, các đặc trưng sẽ có độ chi tiết cao hơn và do đó hình ảnh có độ phân giải cao sẽ hoạt động tốt hơn. Độ phân giải đầu vào cao hơn cung cấp hình ảnh chi tiết hơn và do đó nâng cao khả năng suy luận của mô hình về các đối tượng nhỏ hơn và trích xuất các mẫu mịn hơn. Tuy nhiên cũng như các cách thu phóng trên, việc chỉ thu phóng theo độ phân giải không hề luôn luôn hiệu quả trong mọi trường hợp mà thậm chí nó còn có thể giảm độ chính xác của mô hình đi một cách nhanh chóng. (tùy vào từng task thì độ phân giải có thể mang lại kết quả tốt hơn đến một mức độ nào đó.)

> Kết luận : 
> - Việc scale mô hình nhằm đạt kết quả tốt hơn thực sự có hiệu quả nhưng đối với những mô hình lớn thì lại chưa có cách giải quyết (khi mô hình lớn thì 3 yếu tố trên đôi khi lại làm giảm độ chính xác của mô hình).
> - Khi mô hình lớn thì việc scale ko mang lại nhiều kết quả mà thời gian tìm kiếm những tham số phù hợp lại cực kì tốn kém về máy và thời gian.

Cắt ngang 1 tý : 
> Ta sẽ để ý rằng thay vì việc scale từng thứ một thì việc scale 3 thứ cùng 1 lúc sẽ đảm bảo được kết quả đầu ra sẽ được hưởng những tính chất sau : 
> - Chất lượng ảnh hay kích thước ảnh tăng lên => yêu cầu mô hình phải tăng số lượng layer để có thể học sâu hơn nữa để tăng lượng receptive field và độ rộng của mô hình cũng cần tăng để có thể nắm bắt được nhiều pattern hơn.
> - Mối quan hệ giữa 3 đại lượng width (w) , depth (d) và image resolution (r) là tương quan do đó tồn tại một cách biến đổi nào đó mà có thể sử dụng cả 3 và đạt kết quả tốt nhất.
> - Tuy nhiên đây vẫn chỉ là những suy tính và dự đoán dựa trên những kinh nghiệm của người làm và logic.

## 3. Đề xuất của tác giả

Ta thấy với mỗi layer ConvNet có thể biểu diễn dưới dạng $Y_{i}=F_{i}(X_{i})$ trong đó $F_{i}$ là toán tử và $Y_{i}$ là đầu ra và $X_{i}$ là đầu vào ở trạng thái $i$ , trong đó $X_{i}$ là tensor shape $(H_{i},W_{i},C_{i})$.

Do đó ta có thể định nghĩa mạng ConvNet thành chuỗi các layer nối liền nhau như sau : $N=F_{k} \odot ...\odot F_{2} \odot F_{1}(X_{1}) = \odot_{j=1...k}F_{j}(X_{1})$ . Thông thường thì mạng ConvNet sẽ chứa $N$ block giống nhau và chỉ khác nhau phần cuối tùy theo task.
Ở đây thì tác giả đề xuất đến bài toán cần tìm các số để Accuracy đạt tối đa với 2 tiêu chí gồm : 
- accuracy
- FLOPS / target_flops

Đây cũng là các tiêu chí để xây dựng mô hình EfficientNet B0.
<br>
![](https://i.imgur.com/fTZxpY1.png)

trong đó $d$ là depth , $w$ là width , $r$ là image resolution.

Công thức tác giả đưa ra : 
depth : $d = \alpha^{\phi}$
width : $w = \beta^{\phi}$
resolution : $r = \gamma^{\phi}$
sao cho $\alpha . \beta^{2} . \gamma^{2} \approx 2$
và $\alpha \geq 1,\beta\geq1,\gamma\geq1$

Các giá trị $\alpha,\beta,\gamma$ có thể tìm ra được thông qua 1 gridsearch tại không gian nhỏ. Hệ số $\phi$ là hệ số do người dùng cài đặt tùy thuộc vào mức độ scale mà người dùng muốn. Chú ý rằng FLOPS sẽ tăng tỉ lệ thuận với $d,w^{2},r^{2}$ .

Như đề cập ở trên thì việc xây dựng mạng EfficientNet sẽ dựa trên 2 yếu tố và hàm mục tiêu sẽ là $ACC(m)\times [FLOPS(m)/T]^{w}$ trong đó $ACC(m)$ là độ chính xác, $T$ là target FLOPS tối đa ta có thể handle, $w=-0.07$ là tham số để kiểm tra và đánh giá trade-off giữa acc và FLOPS.

![](https://i.imgur.com/N634sjb.png)

Bảng mô tả mô hình EfficientNet-B0. Ở đây sử dụng MBConv1 hay còn gọi là Inverse Residual Connection block.

Các mô hình EfficientNet sau dựa trên cách phát triển như sau : 
B1 : Fix giá trị $\phi=1$ sau đó tính toán các giá trị $\alpha,\beta,\gamma$ tương ứng. Sau đó bộ cặp số tốt nhất được đem ra.
B2 : Sử dụng chính các giá trị $\alpha,\beta,\gamma$ tương ứng để xây ra các mô hình mới với $\phi$ khác.

=> Ta có bộ số khá tối ưu cho mô hình lớn mà không cần phải tìm ở không gian lớn do đó giải quyết vấn đề scale mô hình.

## 4. Một số kết quả áp dụng đề xuất của tác giả vào các mạng ConvNet đã có sẵn.
![](https://i.imgur.com/hP4Zm6E.png)
![](https://i.imgur.com/FNYE5pI.png)



## 4. Code
Code trên tham khảo trên mạng


``` python
!pip3 install -U efficientnet


import os
import sys
import numpy as np
from skimage.io import imread
import matplotlib.pyplot as plt
sys.path.append('..')

from keras.applications.imagenet_utils import decode_predictions

from efficientnet.keras import EfficientNetB0
from efficientnet.keras import center_crop_and_resize, preprocess_input

image = imread('/content/812+SI3ZSQL.jpg')

# loading pretrained model
model = EfficientNetB0(weights='imagenet')

image_size = model.input_shape[1]
x = center_crop_and_resize(image, image_size=image_size)
x = preprocess_input(x)
x = np.expand_dims(x, 0)

# make prediction and decode
y = model.predict(x)
decode_predictions(y)
```

Trong đó hình ảnh predict là hình sau : 
![](https://i.imgur.com/2Eh0GwF.jpg)

Kết quả ko ra chó Shiba vì chắc ko có label đó tuy nhiên đều predict ra các giống chó và xếp đầu là loài corgi chắc vì màu lông giống và trông cũng ngáo y hệt ^^

![](https://i.imgur.com/SToWijX.png)

Mọi thắc mắc hay góp ý xin hãy comment để mình có thể trao đổi , thanks :heart: 


