Writeup : ractf2020 Really Simple Algorithm 2 (Crypto)

![](https://i.imgur.com/R5eDaEV.png)


Khi ta netcat lên server thì sẽ được trả lại những giá trị như sau : 

```
n: 223922008395000014283723882760043013065620539801942997270066014714444427363520046178491495846777410229650596356717074951108688822198285613233594532478253401
e: 65537
c: 46927100178484164054422389460642412566557695583199776470820879720759927161614477494084640525145368116489400445149854096703153163465010997139019479726766394
```

Nhìn qua ta có thể thấy đây vẫn là mã hóa RSA. Đây là bài :hamster: Bài này cho n nhưng ko cho p và q do đó mình thử dùng http://factordb.com/ để phân tích thử số n ra 2 số p và q. Kết quả ra được là :kissing_closed_eyes: 

![](https://i.imgur.com/Ft8n7sE.png)

Vậy set thử p bằng 17 còn q bằng 137... gì gì kia và chạy lại file code ở bài trước ta sẽ ra kết quả

```
import math
def getModInverse(a, m):
	if math.gcd(a, m) != 1:
		return None
	u1, u2, u3 = 1, 0, a
	v1, v2, v3 = 0, 1, m

	while v3 != 0:
		q = u3 // v3
		v1, v2, v3, u1, u2, u3 = (
			u1 - q * v1), (u2 - q * v2), (u3 - q * v3), v1, v2, v3
	return u1 % m

p = 17
q = 13171882846764706722571993103531941945036502341290764545298000865555554550795296834028911520398671189979446844512769114771099342482252094896093796028132553
e = 65537
c =  146927100178484164054422389460642412566557695583199776470820879720759927161614477494084640525145368116489400445149854096703153163465010997139019479726766394

n = 223922008395000014283723882760043013065620539801942997270066014714444427363520046178491495846777410229650596356717074951108688822198285613233594532478253401

phi = (p-1)*(q-1)

d = getModInverse(e, phi)

plaintext = pow(c,d,n)

print(plaintext)
```

File code các bạn có thể lấy ở [đây](https://github.com/buiminhptit/CTF/blob/master/RACTF/rsa2.py).

Kết quả được là : 
789424672162027037124394589095952276119053632356820220807985810740834941

Hên quá, bài này dễ :smiley: Lên kt.gy và chuyển từ int sang ascii ta có `ractf{S0m3t1mesS1zeDoesM4773r}`.

Second crypto challenge done!

P/S : Tài liệu về một số cách tấn công mã hóa RSA ([tham khảo](https://github.com/buiminhptit/CTF/blob/master/RACTF/RSA.docx))