# Phân loại thư rác sử dụng NaiveBayes

1 bài tập lớn môn trí tuệ nhân tạo của mình về tạo 1 model có thể phân biệt được thư rác và thư thường sử dụng phương pháp naive_bayes.

Đầu tiên là mình phải hiểu rõ cách làm và hướng giải quyết :v

Mình sẽ nói sơ qua về cách làm :3 Đầu tiên là mình sẽ phải đếm số từ trong các thư trong dataset.Và sau đó mình sẽ cho qua vài filter để giảm những từ sida ko có ý nghĩa =)) Ở đây mình dùng nltk.stopword và sau đó dùng stem của bộ nltk ( sorry vì mình mới đc biết cái thư viện này nên ko ns rõ đc, chỉ set up đơn giản ). Sau đó đọc hết tất cả thư và gán nhãn cho nó 1 là spam và 0 là thư thường.Sau đó chia bộ train và bộ test , test model lưu lại và load lên để predict thôi =)))


<strong>Trước hết là import thư viện : </strong>

<div class="center" markdown="0">
<img class=" size-full wp-image-181 aligncenter" src="https://st4rtt0l34rn.files.wordpress.com/2018/05/11.png" alt="1" width="339" height="171" />
</div>

<div class="center" markdown="0">
<strong>Bắt đầu với bước 1  thôi :  Đếm số từ trong các  thư trong dataset</strong>
</div>


<div class="center" markdown="0">
<img class=" size-full wp-image-182 aligncenter" src="https://st4rtt0l34rn.files.wordpress.com/2018/05/21.png" alt="2" width="996" height="652" />
</div>

<strong>Bước 2 : Viết hàm trích xuất đặc điểm của các từ </strong>

<div class="center" markdown="0">
<img class=" size-full wp-image-183 aligncenter" src="https://st4rtt0l34rn.files.wordpress.com/2018/05/3.png" alt="3" width="612" height="624" />
</div>

<strong>Bước 3 : Chạy để lưu file model ra</strong>

<img class=" size-full wp-image-184 aligncenter" src="https://st4rtt0l34rn.files.wordpress.com/2018/05/4.png" alt="4" width="589" height="397" />

<strong>Và bước 4 là bước cuối cùng :v tạo file main.py để load model và predict :</strong>

<div class="center" markdown="0">
<img class=" size-full wp-image-185 aligncenter" src="https://st4rtt0l34rn.files.wordpress.com/2018/05/5.png" alt="5" width="584" height="633" /></div>


Các bạn có thể tham khảo code ở đường link bên dưới,có j ko hiểu có thể comment hoặc inb cho mình để mình giải thích :D

Link download bộ dữ liệu : <a href="http://www2.aueb.gr/users/ion/data/enron-spam/">http://www2.aueb.gr/users/ion/data/enron-spam/</a>

Code github : <a href="https://github.com/LearnCoding199x/spam_mail_using_naive_bayes">https://github.com/LearnCoding199x/spam_mail_using_naive_bayes</a>