# Writeup NahamCon CTF

## Candroid (Mobile)

![](https://i.imgur.com/M0ctUZP.png)


Bài này cho ta 1 file .apk sau đó mình download về và đưa lên http://www.javadecompilers.com/ để có thể decompile file apk này ra. Decompile sẽ ra 2 folder như sau :

![](https://i.imgur.com/ppCc6r6.png)


Sau khi ngồi mò 1 lúc mình quyết định là sử dụng đơn giản thử tìm flag trong folder mình decompile ra được bằng cách dùng `ack 'flag'` và mình có kết quả là flag : 
![](https://i.imgur.com/QpH0lp4.png)

Flag nằm trong thư mục candroid.apk/resources/res/values/strings.xml.

FLAG : `flag{4ndr0id_1s_3asy}` 

## Homecooked (Cryptography)

![](https://i.imgur.com/oThFGw5.png)

Bài này thì download file decrypt.py về và check thì sẽ ra đoạn code như sau : 
```
import base64
num = 0
count = 0
cipher_b64 = b"MTAwLDExMSwxMDAsOTYsMTEyLDIxLDIwOSwxNjYsMjE2LDE0MCwzMzAsMzE4LDMyMSw3MDIyMSw3MDQxNCw3MDU0NCw3MTQxNCw3MTgxMCw3MjIxMSw3MjgyNyw3MzAwMCw3MzMxOSw3MzcyMiw3NDA4OCw3NDY0Myw3NTU0MiwxMDAyOTAzLDEwMDgwOTQsMTAyMjA4OSwxMDI4MTA0LDEwMzUzMzcsMTA0MzQ0OCwxMDU1NTg3LDEwNjI1NDEsMTA2NTcxNSwxMDc0NzQ5LDEwODI4NDQsMTA4NTY5NiwxMDkyOTY2LDEwOTQwMDA="

def a(num):
    if (num > 1):
        for i in range(2,num):
            if (num % i) == 0:
                return False
                break
        return True
    else:
        return False
       
def b(num):
    my_str = str(num)
    rev_str = reversed(my_str)
    if list(my_str) == list(rev_str):
       return True
    else:
       return False


cipher = base64.b64decode(cipher_b64).decode().split(",")

while(count < len(cipher)):
    if (a(num)):
        if (b(num)):
            print(chr(int(cipher[count]) ^ num), end='', flush=True)
            count += 1
            if (count == 13):
                num = 50000
            if (count == 26):
                num = 500000
    else:
        pass
    num+=1

print()
```

Đại khái là ta dễ thấy hàm a là hàm check số nguyên tố,hàm b là hàm check đảo xâu. Ta sẽ giải mã base64 cái cipher kia ra 1 list.

`cipher =[100,111,100,96,112,21,209,166,216,140,330,318,321,70221,70414,70544,71414,71810,72211,72827,73000,73319,73722,74088,74643,75542,1002903,1008094,1022089,1028104,1035337,1043448,1055587,1062541,1065715,1074749,1082844,1085696,1092966,1094000]`

Sau đó chạy thử file đó sẽ ra được 1 đoạn flag là `flag{pR1m3s_4re_co0ler_Wh3` sau đó tắc... Mình đợi 1 lúc không được nên mình cancel chương trình và nhận ra lỗi là do chạy vòng lặp quá nhiều. Do đó chỉ cần chỉnh lại hàm a là chạy đến int(math.sqrt(num)) thôi sẽ ra đoạn còn lại của flag.

FLAG : `flag{pR1m3s_4re_co0ler_Wh3n_pal1nDr0miC}`

## Twinning (Cryptography)

![](https://i.imgur.com/hEFAIxw.png)

Bài này khi connect đến server thì sẽ hiện ra như sau : 
![](https://i.imgur.com/YOtadA0.png)

Nhìn qua thì mình có thể giải mã ngay bằng code đơn giản của RSA như sau : 

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

p = 12486845071738693427247285880657563831824646171675159611917701558464405456327259119333926908899840465729616002343942002689477215556473030869671629291940787
q = 9507713202824006853018246633387916367752063091489935338468852397046107765297345961101567617455479816742873156228827880421086979474228778491647759287898071
e = 65537
c =  108939654678453616174088760215732903149885203987808948863470078202999661180553761761290094797844905061396113158351302410841872578407519850047247575605178778188351806331769859422746367183132389482471063266455485501246627097810790878814045056794921807351845420984916236583641461647410952602770893908930071929869

n = p*q

phi = (p-1)*(q-1)

d = getModInverse(e, phi)

plaintext = pow(c,d,n)

print(plaintext)
```

Link code mình để ở [đây](https://github.com/buiminhptit/CTF/blob/master/RACTF/rsa.py) 
Thay các giá trị vào sẽ ra kết quả như trên hình.
FLAG : `flag{thats_the_twinning_pin_to_win}`

## Microsooft (Forensics)
![](https://i.imgur.com/WsoEDcr.png)

Sau khi down file docx về thì ta mở ra ko thấy gì, sử dụng strings với file này thì thấy có rất nhiều file thuộc folder src do đó khả năng là file này là file nén dữ liệu. Từ đó ta chuyển đuôi từ .docx thành .zip và ta mở ra được như sau : 
![](https://i.imgur.com/dDnTMQB.png)
Kiểm tra 1 lượt các file ta thấy file oof.txt chứa đoạn Lorem ipsum... Ở đây mình Ctrl-F và search giá trị flag và ra kết quả.

FLAG : `flag{oof_is_right_why_gfxdata_though}`

## Localghost (Web)
![](https://i.imgur.com/y0egiL2.png)

Bài này khi load trang web ko có gì ngoài hình con ma. Check source code thì ta thấy file js lạ. (<script src="/jquery.jscroll2.js"></script>)
Thử xem file js này thấy đoạn hex như sau : 

```"\x75\x73\x65\x20\x73\x74\x72\x69\x63\x74","\x6A\x73\x63\x72\x6F\x6C\x6C","\x3C\x73\x6D\x61\x6C\x6C\x3E\x4C\x6F\x61\x64\x69\x6E\x67\x2E\x2E\x2E\x3C\x2F\x73\x6D\x61\x6C\x6C\x3E","\x61\x3A\x6C\x61\x73\x74","","\x66\x6C\x61\x67","\x53\x6B\x4E\x55\x52\x6E\x74\x7A\x63\x47\x39\x76\x62\x32\x39\x76\x61\x33\x6C\x66\x5A\x32\x68\x76\x63\x33\x52\x7A\x58\x32\x6C\x75\x58\x33\x4E\x30\x62\x33\x4A\x68\x5A\x32\x56\x39","\x73\x65\x74\x49\x74\x65\x6D","\x6C\x6F\x63\x61\x6C\x53\x74\x6F\x72\x61\x67\x65","\x64\x61\x74\x61","\x66\x75\x6E\x63\x74\x69\x6F\x6E","\x64\x65\x66\x61\x75\x6C\x74\x73","\x65\x78\x74\x65\x6E\x64","\x6F\x76\x65\x72\x66\x6C\x6F\x77\x2D\x79","\x63\x73\x73","\x76\x69\x73\x69\x62\x6C\x65","\x66\x69\x72\x73\x74","\x6E\x65\x78\x74\x53\x65\x6C\x65\x63\x74\x6F\x72","\x66\x69\x6E\x64","\x62\x6F\x64\x79","\x68\x72\x65\x66","\x61\x74\x74\x72","\x20","\x63\x6F\x6E\x74\x65\x6E\x74\x53\x65\x6C\x65\x63\x74\x6F\x72","\x74\x72\x69\x6D","\x73\x72\x63","\x69\x6D\x67","\x66\x69\x6C\x74\x65\x72","\x6C\x6F\x61\x64\x69\x6E\x67\x48\x74\x6D\x6C","\x6C\x65\x6E\x67\x74\x68","\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x69\x6E\x6E\x65\x72","\x3C\x64\x69\x76\x20\x63\x6C\x61\x73\x73\x3D\x22\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x69\x6E\x6E\x65\x72\x22\x20\x2F\x3E","\x77\x72\x61\x70\x41\x6C\x6C","\x63\x6F\x6E\x74\x65\x6E\x74\x73","\x70\x61\x67\x69\x6E\x67\x53\x65\x6C\x65\x63\x74\x6F\x72","\x68\x69\x64\x65","\x63\x6C\x6F\x73\x65\x73\x74","\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x6E\x65\x78\x74\x2D\x70\x61\x72\x65\x6E\x74","\x61\x64\x64\x43\x6C\x61\x73\x73","\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x69\x6E\x6E\x65\x72\x2C\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x61\x64\x64\x65\x64","\x6E\x6F\x74","\x70\x61\x72\x65\x6E\x74","\x3C\x64\x69\x76\x20\x63\x6C\x61\x73\x73\x3D\x22\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x6E\x65\x78\x74\x2D\x70\x61\x72\x65\x6E\x74\x22\x20\x2F\x3E","\x77\x72\x61\x70","\x75\x6E\x77\x72\x61\x70","\x63\x68\x69\x6C\x64\x72\x65\x6E","\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x61\x64\x64\x65\x64","\x72\x65\x6D\x6F\x76\x65\x44\x61\x74\x61","\x2E\x6A\x73\x63\x72\x6F\x6C\x6C","\x75\x6E\x62\x69\x6E\x64","\x64\x69\x76\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x69\x6E\x6E\x65\x72","\x62\x6F\x72\x64\x65\x72\x54\x6F\x70\x57\x69\x64\x74\x68","\x70\x61\x64\x64\x69\x6E\x67\x54\x6F\x70","\x73\x63\x72\x6F\x6C\x6C\x54\x6F\x70","\x74\x6F\x70","\x6F\x66\x66\x73\x65\x74","\x68\x65\x69\x67\x68\x74","\x63\x65\x69\x6C","\x77\x61\x69\x74\x69\x6E\x67","\x70\x61\x64\x64\x69\x6E\x67","\x6F\x75\x74\x65\x72\x48\x65\x69\x67\x68\x74","\x69\x6E\x66\x6F","\x6A\x53\x63\x72\x6F\x6C\x6C\x3A","\x66\x72\x6F\x6D\x20\x62\x6F\x74\x74\x6F\x6D\x2E\x20\x4C\x6F\x61\x64\x69\x6E\x67\x20\x6E\x65\x78\x74\x20\x72\x65\x71\x75\x65\x73\x74\x2E\x2E\x2E","\x6E\x65\x78\x74\x48\x72\x65\x66","\x77\x61\x72\x6E","\x6A\x53\x63\x72\x6F\x6C\x6C\x3A\x20\x6E\x65\x78\x74\x53\x65\x6C\x65\x63\x74\x6F\x72\x20\x6E\x6F\x74\x20\x66\x6F\x75\x6E\x64\x20\x2D\x20\x64\x65\x73\x74\x72\x6F\x79\x69\x6E\x67","\x61\x75\x74\x6F\x54\x72\x69\x67\x67\x65\x72","\x61\x75\x74\x6F\x54\x72\x69\x67\x67\x65\x72\x55\x6E\x74\x69\x6C","\x73\x63\x72\x6F\x6C\x6C\x2E\x6A\x73\x63\x72\x6F\x6C\x6C","\x62\x69\x6E\x64","\x63\x6C\x69\x63\x6B\x2E\x6A\x73\x63\x72\x6F\x6C\x6C","\x3C\x64\x69\x76\x20\x63\x6C\x61\x73\x73\x3D\x22\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x6C\x6F\x61\x64\x69\x6E\x67\x22\x3E","\x3C\x2F\x64\x69\x76\x3E","\x68\x74\x6D\x6C","\x6C\x61\x73\x74","\x3C\x64\x69\x76\x20\x63\x6C\x61\x73\x73\x3D\x22\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x61\x64\x64\x65\x64\x22\x20\x2F\x3E","\x61\x70\x70\x65\x6E\x64","\x65\x72\x72\x6F\x72","\x72\x65\x6D\x6F\x76\x65","\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x6E\x65\x78\x74\x2D\x70\x61\x72\x65\x6E\x74","\x63\x61\x6C\x6C\x62\x61\x63\x6B","\x63\x61\x6C\x6C","\x64\x69\x72","\x6C\x6F\x61\x64","\x64\x69\x76\x2E\x6A\x73\x63\x72\x6F\x6C\x6C\x2D\x61\x64\x64\x65\x64","\x61\x6E\x69\x6D\x61\x74\x65","\x64\x65\x62\x75\x67","\x6F\x62\x6A\x65\x63\x74","\x61\x70\x70\x6C\x79","\x6C\x6F\x67","\x73\x6C\x69\x63\x65","\x70\x72\x6F\x74\x6F\x74\x79\x70\x65","\x66\x6E","\x69\x6E\x69\x74\x69\x61\x6C\x69\x7A\x65\x64","\x65\x61\x63\x68```

Ta dùng [kt.gy](https://kt.gy/tools.html#) để dịch sang sẽ ra đoạn `flagSkNURntzcG9vb29va3lfZ2hvc3RzX2luX3N0b3JhZ2V9`
Đây là mã hóa base64. Thử dịch ra và thêm flag{} vào thì thấy sai. Để ý đề bài cũng nói là flag ko theo format flag{} do đó ta sẽ base64 cả đoạn ý. 
FLAG : `JCTF{spoooooky_ghosts_in_storage}`

## Fake file (Miscellaneous)
![](https://i.imgur.com/ZlBVaRi.png)

Bài này khi connect lên server thì mình chỉ cần chạy câu lệnh `find / -xdev -type f -print0 | xargs -0 grep -H "flag{"` là ra kết quả. Tuy nhiên sau khi ra kết quả thì mình có để ý đường link là `/home/user/..` thì mình mới nhận ra là khi `ls -a` ở thư mục `/home/user` thì mình thấy .. nên nghĩ là quay lại thư mục parents mà ko để ý rằng đó là tên file :+1:  thật là vi diệu .
FLAG : `flag{we_should_have_been_worried_about_u2k_not_y2k}`

