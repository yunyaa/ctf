# sql注入
## 基于约束的sql攻击  
### 攻击手法
#### 说明
在SQL中执行字符串处理的时候，字符串末尾的空格会被删除，“abc   ”会被当作“abc”处理，对于绝大多数情况来说是成立的(where字句 insert语句)  
在所有的insert查询中，sql会根据varchar(n)来限制字符串的最大字节长度，如果大于n个字符，那么会截断字符串。  
#### 例子  
创建一个数据表users，其包含username和password列，并且字段的最大长度限制为25个字符。然后，我将向username字段插入“vampire”，向password字段插入“my_password”。  

	mysql> CREATE TABLE users (
	    ->   username varchar(25),
	    ->   password varchar(25)
	    -> );
	Query OK, 0 rows affected (0.09 sec)
	mysql> INSERT INTO users
	    -> VALUES('vampire', 'my_password');
	Query OK, 1 row affected (0.11 sec)
	mysql> SELECT * FROM users;
	+----------+-------------+
	| username | password    |
	+----------+-------------+
	| vampire  | my_password |
	+----------+-------------+
	1 row in set (0.00 sec)  

-------------------------
	mysql> SELECT * FROM users
	    -> WHERE username='vampire       ';
	+----------+-------------+
	| username | password    |
	+----------+-------------+
	| vampire  | my_password |
	+----------+-------------+
	1 row in set (0.00 sec)

现在我们假设一个存在漏洞的网站使用了前面提到的PHP代码来处理用户的注册及登录过程。为了侵入任意用户的帐户（在本例中为“vampire”），只需要使用用户名“vampire[许多空白符]1”和一个随机密码进行注册即可。对于选择的用户名，前25个字符应该只包含vampire和空白字符，这样做将有助于绕过检查特定用户名是否已存在的查询。

	mysql> SELECT * FROM users
	    -> WHERE username='vampire                   1';
	Empty set (0.00 sec)  
需要注意的是，在执行SELECT查询语句时，SQL是不会将字符串缩短为25个字符的。因此，这里将使用完整的字符串进行搜索，所以不会找到匹配的结果。接下来，当执行INSERT查询语句时，它只会插入前25个字符。
