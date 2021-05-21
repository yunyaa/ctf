MISC:GIF
===========
--------------------
**[题目链接](https://adworld.xctf.org.cn/task/answer?type=misc&number=1&grade=0&id=5104&page=1):<https://adworld.xctf.org.cn/task/answer?type=misc&number=1&grade=0&id=5104&page=1>**  
****  
**解题过程**  
下载附件后,发现其gif文件夹为104个黑白的图片首先想到其黑白分别对应0,1。随后使用python写一个小脚本对其进行颜色进行判断并且归为一个字符串,本人用的是PIL库,应该也有其他的判断方法，随后想到104/8正好为一个整数13,而占位一个字节的ASCII码正好为8位,故猜想其解密之后为13给字符长的字符串也就是flag。随后进行脚本编写。  
	
	```python
	from PIL import Image

	count = 0
	lAll = []
	sOne = ""
	
	for i in range(104):
	    im = Image.open("<yourdir>\{}.jpg".format(i))
	    imData = im.getpixel((0, 0))
	    if count == 8:
	        lAll.append(sOne)
	        sOne = ""
	        count = 0
	        allCount += 1
	    if imData[0] == 12:
	        sOne += "1"
	    else:
	        sOne += "0"
	    count += 1
	
	for one in lAll:
	    target = "0b" + one
	    print(chr(eval(target)), end="")
	
	```