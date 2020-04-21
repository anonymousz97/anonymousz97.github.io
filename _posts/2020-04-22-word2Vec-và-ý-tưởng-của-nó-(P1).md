## Giới thiệu và tổng quan

Như chúng ta đã biết thì word2vec hay còn gọi là w2v là một bộ chứa các véc tơ đặc nhiều chiều để biểu diễn từng từ một. Ngày xưa để biểu diễn 1 từ ta sử dụng one-hot vector (Một vector sparse chứa toàn chữ số 0 và 1 chữ số 1). Tuy nhiên với cách này thì khi số lượng phần tử trong tập dữ liệu lớn thì việc biểu diễn càng khó khăn hơn. Do đó người ta đã nghĩ ra mô hình word2vec để biểu diễn từng từ đó theo số chiều nhất định (fixed) và mang nhiều ý nghĩa tương đồng nhất của chúng trong ngữ nghĩa.

![](https://i.imgur.com/LvfVpoa.png)

Lưu ý : Đôi khi người ta gọi vecto từ là word embeddings hay là word representations.

Vậy ý tưởng của việc xây dựng mô hình này là gì?

## Ý tưởng mô hình word2vec

- Chúng ta sử dụng một số lượng lớn các câu được thu thập từ nhiều nguồn để huấn luyện.
- Mỗi từ được biểu diễn bởi một vecto đặc với số chiều được xác định trước (fixed) và random khi khởi tạo.
- Với từng câu ta sẽ duyệt qua tất cả phần tử (trừ phần tử đầu) và xét một ô cửa sổ với size = m quanh cái từ đó. Từ đang xét gọi là center word,từ bên cạnh nằm trong cửa sổ gọi là outside word.
- Tính độ tương quan giữa center word với các outside word sau đó để tính xác suất xảy ra outside word khi tồn tại center word ($P(outside\mid center)$).
- Chỉnh giá trị của word vectors sao cho tối ưu giá trị xác suất.

![](https://i.imgur.com/zYyer6u.png)

Hình trên là cửa sổ size=2 và xét ở từ "into" sẽ sang 2 bên.

![](https://i.imgur.com/iZ3Ykvw.png)

Còn đây là khi xét ở từ "banking".

## Hàm mục tiêu (objective function)

![](https://i.imgur.com/44YPSSp.png)

Ta có thể thấy Likelihood là độ thích ứng của cả câu input đầu vào vì nó là tích của các tích xác suất của các từ trong từng cửa sổ trượt từ đầu đến cuối. Ta sử dụng average negative log likelihood làm hàm mục tiêu và gọi là $J(\theta)$ hay còn gọi là loss hay cost function. Do đó việc của chúng ta sẽ là tối thiểu cái $J(\theta)$ này. ($\theta$ đại diện cho tất cả các tham số mình huấn luyện). 

Ta sử dụng công thức của hàm softmax để tính $P(outside\mid center)$ là : 

$$softmax(x){i} = \frac{exp(x{i})}{\sum_{j=1}^{n}exp(x_{j})}$$

Tuy nhiên ở đây ta sẽ phân 1 từ ra làm 2 vector $u_{w}$ và $v_{w}$ là vector khi giá trị từ $w$ là outside hay là center.

Từ đó : 

$$P(outside|center) = \frac{exp(u^{T}_{o}v_{c})}{\sum_{w\in V}exp(u^{T}_{w}v_{c})}$$

Ta sẽ áp dụng với từng từ một như hình sau : 

![](https://i.imgur.com/CPoeA40.png)

Sau đó ta sẽ tính đạo hàm của $J(\theta)$ theo từng parameters trong $\theta$ và cập nhật trọng số của $\theta$. Hình dưới là minh họa về $\theta$ : 

![](https://i.imgur.com/eACOJV6.png)

Ở phần sau mình sẽ nói tiếp về cách tính đạo hàm , chain rule và các mô hình CBOW và Skip-grams vốn là ý tưởng mà word2vec đã kết hợp lại :hamster: Cám ơn các bạn đã chú ý theo dõi!

Tài liệu tham khảo : http://web.stanford.edu/class/cs224n/slides/cs224n-2020-lecture01-wordvecs1.pdf
