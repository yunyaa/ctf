# php及伪协议总结
------------------------
## 函数  
### php strtr 函数  
#### 用法
替换字符串中字符串
#### 语法  
strtr(string,from,to)
#### 例子
	echo strtr("Hilla Warld","ia","eo");

| 参数 | 描述 |
| ---- | ---- |
| string | 必需。规定要转换的字符串。 |
| from | 必需（除非使用数组）。规定要改变的字符。 |
| to | 必需（除非使用数组）。规定要改变为的字符。 |
| array | 必需（除非使用 from 和 to）。数组，其中的键名是更改的原始字符，键值是更改的目标字符。 |

### php substr函数  
#### 用法
返回字符串的一部分  
#### 语法  
	substr(string,start,length)
| 参数 | 描述 |
| ---- | ---- |
| string | 必需。规定要返回其中一部分的字符串。 |
| start	 | 	必需。规定在字符串的何处开始。 <br>正数 - 在字符串的指定位置开始<br>负数 - 在从字符串结尾开始的指定位置开始<br>0 - 在字符串中的第一个字符处开始 |
| length | 可选。规定被返回字符串的长度。默认是直到字符串的结尾。<br>正数 - 从 start 参数所在的位置返回的长度<br>负数 - 从字符串末端返回的长度 |

### preg_match函数
#### 用法
执行匹配正则表达式  
#### 语法	
	preg_match ( string $pattern , string $subject , array &$matches = null , int $flags = 0 , int $offset = 0 ) : int|false

|  参数   | 描述  |
|  ----  | ----  |
| pattern  | 要搜索的模式，字符串类型。 |
| subject  | 输入字符串。|
| matches | 如果提供了参数matches，它将被填充为搜索结果。 $matches[0]将包含完整模式匹配到的文本， $matches[1] 将包含第一个捕获子组匹配到的文本，以此类推。|
| flags | flags 可以被设置为以下标记值的组合： |
| offset | 通常，搜索从目标字符串的开始位置开始。可选参数 offset 用于 指定从目标字符串的某个位置开始搜索(单位是字节)。 |   

#### 返回值 
preg_match()返回 pattern 的匹配次数。 它的值将是0次（不匹配）或1次，因为preg_match()在第一次匹配后 将会停止搜索。preg_match_all()不同于此，它会一直搜索subject 直到到达结尾。 如果发生错误preg_match()返回 false。  
int or flase;

### php file_get_content函数  
#### 用法  
把文件读到一个字符串中
#### 语法  
	file_get_contents(path,include_path,context,start,max_length)

|  参数   | 描述  |
|  ----  | ----  |
| path  | 要读取的文件 |
| include_path  | 可选。如果您还想在 include_path（在 php.ini 中）中搜索文件的话，请设置该参数为 '1'。|
| context | 可选。规定文件句柄的环境。context 是一套可以修改流的行为的选项。若使用 NULL，则忽略。|
| start | 文件开始位置 |
| max_lenth | 读取字节长度 |  

### serialize/unserialize
#### 用法
serialize将变量进行序列化返回一个字符串  
unserialize将字符串进行反序列化得到被序列化的变量。
#### 作用  
若反序列化的参数可控，则可以构造任意变量。若代码中有一个类则可以构造此类的对象。  
#### 格式  
	1 <?php
	2 class test{
	3     private $test1="hello";
	4     public $test2="hello";
	5     protected $test3="hello";
	6 }
	7 $test = new test();
	8 echo serialize($test);  //  O:4:"test":3:{s:11:" test test1";s:5:"hello";s:5:"test2";s:5:"hello";s:8:" * test3";s:5:"hello";}
	9 ?>

通过对网页抓取输出是这样的   <font color='red'>O:4:"test":3:{s:11:"\00test\00test1";s:5:"hello";s:5:"test2";s:5:"hello";s:8:"\00*\00test3";s:5:"hello";}</font>  
当序列化字符串设定的类的成员属性个数大于实际个数的时候，会绕过wakeup函数。
#### __wakeup函数  
wakeup函数会在反序列话的时候自动执行，然而其可以进行绕过

#### 字符串逃逸漏洞
序列化的字符串在经过过滤函数不正确的处理而导致对象注入，目前看到都是因为过滤函数放在了serialize函数之后，要是放在序列化之前应该就不会产生这个问题
#### 反序列化删除文件
	a[]=1&b[]=2&c=O:5:"Jesen":4:{s:8:"filename";s:19:"./sandbox/lock.lock";s:7:"content";i:8;s:2:"me";O:10:"ZipArchive":5:{s:6:"status";i:0;s:9:"statusSys";i:0;s:8:"numFiles";i:0;s:8:"filename";s:0:"";s:7:"comment";s:0:"";}}
内置类ZipArchive来删除文件，因为ZipArchive恰好有个open方法，当第二个参数为8时可以删除指定文件
#### 类属性名序列化
private属性序列化的时候格式是%00类名%00成员名
protect属性序列化的时候格式是%00*%00成员名

## 伪协议
### file协议
#### 条件
<font color="red">php.ini</font>中  
- <font color="red">allow_url_fopen</font>:off/on  
-  <font color="red">allow_url_include</font>:off/on  
#### 作用
用来访问本地文件系统，ctf中**读取文件**且不受allow_url_fopen和allow_url_include的影响  
<font color="red">include()/require()/include_once()/require_once()</font>参数可控的情况下，如导入为非.php文件，则仍按照php语法进行解析，这是include()函数所决定的。  
### php协议
#### 条件
<font color="red">php.ini</font>中  
- <font color="red">allow_url_fopen</font>:off/on  
-  <font color="red">allow_url_include</font>:off/on <font color="red">php://input php://stdin php://memory php://temp</font>需要on  
#### 作用  
php:// 访问各个输入/输出流（I/O streams），在CTF中经常使用的是php://filter和php://input，php://filter用于**读取源码**，php://input用于**执行php代码**。
#### 说明  
PHP 提供了一些杂项输入/输出（IO）流，允许访问 PHP 的输入输出流、标准输入输出和错误描述符，  
内存中、磁盘备份的临时文件流以及可以操作其他读取写入文件资源的过滤器。  

|  协议   | 作用  |
|  ----  | ----  |
|php://input|可以访问请求的原始数据的只读流，在POST请求中访问POST的data部分，在（form表单属性）enctype="multipart/form-data" 的时候php://input 是无效的。| 
| php://output  | 只写的数据流，允许以 print 和 echo 一样的方式写入到输出缓冲区。|
| php://fd	 | (>=5.3.6)允许直接访问指定的文件描述符。例如 php://fd/3 引用了文件描述符 3。（文件描述符是内核用来标识文件的非负整数）|
| php://memory php://temp | (>=5.1.0)一个类似文件包装器的数据流，允许读写临时数据。两者的唯一区别是 php://memory 总是把数据储存在内存中，而 php://temp 会在内存量达到预定义的限制后（默认是 2MB）存入临时文件中。临时文件位置的决定和 sys_get_temp_dir() 的方式一致。|
| php://filter | (>=5.0.0)一种元封装器，设计用于数据流打开时的筛选过滤应用。对于一体式（all-in-one）的文件函数非常有用，类似 readfile()、file() 和 file_get_contents()，在数据流内容读取之前没有机会应用其他过滤器。 | 

#### php://filter协议参数  
|参数|描述|
|---|---|
|resource=<要过滤的数据流>|必须。它指定了你要筛选过滤的数据流。|
|read=<读链的过滤器>|可选项。可以设定一个或多个过滤器名称，以管道符（*\*）分隔。|
|write=<写链的过滤器>	|可选项。可以设定一个或多个过滤器名称，以管道符（\	）分隔。|
|<; 两个链的过滤器>|任何没有以 read= 或 write= 作前缀的筛选器列表会视情况应用于读或写链。|

#### [过滤器](https://www.php.net/manual/zh/filters.php):<https://www.php.net/manual/zh/filters.php>  
#### 示例
1. filter://read=convert.base64-encode/resource=[文件名]  
2. php://input

#### zip:// & bzip2:// & zlib:// 协议
#### 示例
1. zip://  
	zip://[压缩文件绝对路径]%23[压缩文件内的子文件名]（#编码为%23）
2. compress.bzip2://file.bz2  
	http://127.0.0.1/include.php?file=compress.bzip2://E:\phpStudy\PHPTutorial\WWW\phpinfo.bz2

3. compress.zlib://file.gz
	http://127.0.0.1/include.php?file=compress.zlib://E:\phpStudy\PHPTutorial\WWW\phpinfo.gz

#### data协议
#### 条件
- <font color="red">allow_url_fopen</font>:on  
-  <font color="red">allow_url_include</font>:on 
#### 作用  
自PHP>=5.2.0起，可以使用data://数据流封装器，以传递相应格式的数据。通常可以用来执行PHP代码。
#### 示例
	data://text/plain,
	data://text/plain;base64,
	http://127.0.0.1/include.php?file=data://text/plain;base64,PD9waHAgcGhwaW5mbygpOz8%2b

### http:// & https:// 协议
#### 条件
- <font color="red">allow_url_fopen</font>:on  
-  <font color="red">allow_url_include</font>:on 
#### 作用  
常规 URL 形式，允许通过 HTTP 1.0 的 GET方法，以只读访问文件或资源。CTF中通常用于远程包含。

### phar://协议  
phar://协议与zip://类似，同样可以访问zip格式压缩包内容  
### 示例
	http://127.0.0.1/include.php?file=phar://E:/phpStudy/PHPTutorial/WWW/phpinfo.zip/phpinfo.txt

## 类型比较绕过
### md5弱比较绕过
#### 方法一  
当被比较的a,b两数在进行md5()运算后满足科学计数法形式的话，php会将其当作科学计数法的数字来进行比较而0e****是以0为底故以0e开头的两个数字会相等(只适用于==)  
#### 方法二  
md5()函数无法处理数组，如果传入的为数组，会返回NULL，所以两个数组经过加密后得到的都是NULL,也就是相等的。

也就是说a[]=1,2 b[]=2,3 该验证依然可以绕过。  
### md5强比较  
#### 方法一  
用fastcoll生成md5相同但sha1与文件内容不同的文件。
### strcmp()绕过  
#### 方法一  
此函数在5.3之前的php中如果比较的类型不是str那么会return 0;  
## php短标签
### 语法
<?=(表达式)?>  
<?php echo (表达式)?>
#### 例子
<?=$a?>  
<?php echo $a?>
#### 注意
需要把php.ini中的short_open_tag设为on
