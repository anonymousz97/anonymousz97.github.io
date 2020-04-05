# Cách connect dataset kaggle lên google colab

Như tiêu đề thì mình thấy đây cũng là một vấn đề mà nhiều người quan tâm khi tập luyện với các bộ dữ liệu trên kaggle. Vì vậy hôm nay mình sẽ hướng dẫn các bạn cách connect kaggle với colab và download các dataset về thẳng colab để sử dụng. Điều này sẽ mang lại vài lợi ích như sau : 
- Tốc độ download trên google colab là khá tốt (thực ra là rất tốt đạt cả trăm mb/s).
- Thay vì việc phải download về máy tính rồi upload lên tốn nhiều thời gian và bộ nhớ (có những datasets vượt lên hàng mấy chục GB).
- Download 1 lần xong có thể lưu lên drive luôn để sử dụng.(tương tự với cách down về máy rồi lưu lên drive).

Đầu tiên thì các bạn vào trang kaggle đăng ký 1 tài khoản và vào phần My account như trên hình dưới (góc phải trên).
![](https://i.imgur.com/nOTj7mK.png)


Sau đó thì các bạn kéo xuống phần API và chọn Create New API Tokens. 
![](https://i.imgur.com/5VSJ6G2.png)

Ngay sau khi ấn xong thì nó sẽ download 1 file **kaggle.json** về máy bạn.

Tiếp theo thì bạn mở tab google colab cần download kaggle dataset lên và gõ vào trong cell những dòng này : 
```
!pip install -q kaggle

from google.colab import files

files.upload()
```

Sau đó bạn chạy cell này và upload file **kaggle.json** vừa được download về máy bạn.

Tiếp theo bạn gõ vào cell tiếp theo : 
```
!mkdir ~/.kaggle

!cp kaggle.json ~/.kaggle/

! chmod 600 ~/.kaggle/kaggle.json
```

Lệnh trên để tạo thư mục kaggle xong rồi copy file **kaggle.json** vào đó. Sau đó thì cấp quyền truy cập cho file kaggle.json này.

Sau bước này thì bạn giờ bạn muốn dataset nào trên kaggle thì hãy lấy tên của nó và chạy lệnh sau để lấy về : 
```
! kaggle datasets download -d 'dataset-name'
```

Ví dụ mình muốn lấy dataset recsys-challenge-2015 thì mình gõ như sau : 
```
! kaggle datasets download -d chadgostopp/recsys-challenge-2015
```

Sau đó thì mình có thể chạy unzip file dataset đã download về được bằng câu lệnh: 
```
!unzip filename.zip
```

Vậy là xong!
