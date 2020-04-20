Một trong những vấn đề cơ bản trong việc biểu diễn những phép tính và giá trị toán học trên máy tính đó là ta phải biểu diễn những số thập phân có vô hạn chữ số đằng sau dấu phẩy bởi hữu hạn số lượng bits. Điều này có nghĩa là ta phải đưa ra một mức xấp xỉ tương đối (approximation error) khi biểu diễn số trên máy tính. Một trong số cách biểu diễn đó là làm tròn số. Tuy nhiên nó nảy sinh ra vấn đề khá nghiêm trọng là các con số đi từ lớp này qua lớp khác, đi từ phép tính này qua phép tính khác sẽ khiến cho thuật toán có thể không thể học được nếu như nó không được thiết kế để làm tối ưu tích lũy của lỗi làm tròn.

Ở đây phải kể đến 2 trường hợp là : Overflow và Underflow.

Một dạng của lỗi làm tròn là Overflow. Đó là khi các con số có giá trị lớn xấp xỉ $-\infty$ hay là $\infty$. Điều này khiến việc thực hiện các phép tính đôi khi mang lại giá trị không phải là số. 

Một dạng nữa là Underflow. Đây là hiện tượng xảy ra khi các giá trị số tiến tới giá trị 0 và được coi xấp xỉ là 0. Điều này dẫn đến rất nhiều lỗi ví dụ như chia cho 0 hay là lấy log của 0 thường được coi là $-\infty$.

Một ví dụ về hàm softmax mà chúng ta thường biết đến để tính toán xác suất liên kết với phân phối nhiều nhãn. Hàm softmax được biểu diễn như sau : 

$$softmax(x)_{i} = \frac{exp(x_{i})}{\sum_{j=1}^{n}exp(x_{j})}$$

Giả sử tất cả giá trị của $x_{i}$ đều là hằng số $c$. Thì ta có thể thấy là tất cả output đều là $\frac{1}{n}$. Nếu giá trị $c$ có giá trị độ lớn là lớn nghĩa là $-\infty$ hay $\infty$ thì giá trị của softmax ra là giá trị không thể định nghĩa được.

Cách xử lý việc này là thay vì tính $softmax(x)$ thì ta sẽ tính $softmax(z)$ trong đó $z=x-max_{i}x_{i}$.

Mình đã viết hàm softmax để tính toán cho những thứ ở trên : 

```
import numpy as np
def softmax(z):
    """Computes softmax function.
    z: array of input values.
    Returns an array of outputs with the same shape as z."""
    # For numerical stability: make the maximum of z's to be 0.
    shiftz = z - np.max(z)
    exps = np.exp(shiftz)
    return exps / np.sum(exps)
```

Hi vọng các bạn dev mới vào nghề như mình sẽ chú ý những lỗi như thế này và sửa :smile: 
