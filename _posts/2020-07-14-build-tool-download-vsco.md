Bài này mình sẽ hướng dẫn các bạn làm một tool đơn giản để lấy ảnh chất lượng cao từ vsco về.

Đầu tiên là mình ngồi thử check ảnh trên web của vsco.

![](https://i.imgur.com/7EMj3Wk.png)

Để ý thấy link ảnh nằm trong source code nên mình chỉ cần đơn giản sử dụng requests để download ảnh về đơn giản.


```
from bs4 import BeautifulSoup
import requests
import random
import shutil

def download():
	url = input('Input VSCO url : ')

	res = requests.get(url)
	bs = BeautifulSoup(res.content,'html.parser')
	#print(str(res.content).find('disableSave-mobile css-6dp941'))
	#print(res.content)

	link = str(bs.find("meta",property="og:image",content=True).get("content",None))
	link = link[:link.rfind('?')]
	hash = random.getrandbits(64)
	hash = str(hash)+".jpg"
	response = requests.get(link, stream=True)
	with open(hash, 'wb') as out_file:
		shutil.copyfileobj(response.raw, out_file)
	del response
	print("Done! Saved as {}".format(hash))


if __name__ == "__main__":
	download()
```

Thế là đơn giản chỉ cần nhập URL ảnh mình cần lấy là có thể lấy được ảnh về. Link code mình để ở đây : [github](https://github.com/buiminhptit/vsco_downloader/blob/master/vsco_downloader.py)

