# python总结
## 函数
### open(path, , ,encoding, errors)
#### 参数
path可以为绝对路径与相对路径  
encoding可以选择打开文件的编码方式,大部分乱码是因为encoding选错的原因  
errors是遇到不在字符集中的字符会如何处理，默认为strict，即遇见就报错，可以改为ignore，遇见直接<font color='red'>跳过</font>
#### 用法
读取文件内容
#### 注意
wb的时候返回的是比特串，无法对比特串的比特进行操作，除非将比特串变为bytearray。  
此时可以对比特串直接进行**quote**(相当于php中的urlencode)