## 1 SQL语言的基本概述

### 1.1Sql语言的基本概述

SQL语言在功能上主要分为如下3大类：

* DDL（Data Definition Languages、数据定义语言），这些语句定义了不同的数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和数据表的结构。
  
  * 主要的语句关键字包括 CREATE 、 DROP 、 ALTER 等。

* DML（Data Manipulation Language、数据操作语言），用于添加、删除、更新和查询数据库记录，并检查数据完整性。
  
  * 主要的语句关键字包括 INSERT 、 DELETE 、 UPDATE 、 SELECT 等。
  
  * SELECT是SQL语言的基础，最为重要。

* DCL（Data Control Language、数据控制语言），用于定义数据库、表、字段、用户的访问权限和安全级别。
  
  * 主要的语句关键字包括 GRANT 、 REVOKE 、 COMMIT 、 ROLLBACK 、 SAVEPOINT 等。

> 因为查询语句使用的非常的频繁，所以很多人把查询语句单拎出来一类：DQL（数据查询语言）。还有单独将 COMMIT 、 ROLLBACK 取出来称为TCL （Transaction Control Language，事务控制语言）。

### 1.2SQL语言的规则与规范

* SQL 可以写在一行或者多行。为了提高可读性，各子句分行写，必要时使用缩进

* 每条命令以 ; 或 \g 或 \G 结束

* 关键字不能被缩写也不能分行

* 关于标点符号
  
  * 必须保证所有的()、单引号、双引号是成对结束的
  
  * 必须使用英文状态下的半角输入方式字符串型和日期时间类型的数据可以使用单引号（' '）表示
  
  * 列的别名，尽量使用双引号（" "），而且不建议省略as


* MySQL 在 Windows 环境下是大小写不敏感的

* MySQL 在 Linux 环境下是大小写敏感的
  
  * 数据库名、表名、表的别名、变量名是严格区分大小写的
  
  * 关键字、函数名、列名(或字段名)、列的别名(字段的别名) 是忽略大小写的。

* 推荐采用统一的书写规范：
  
  * 数据库名、表名、表别名、字段名、字段别名等都小写
  
  * SQL 关键字、函数名、绑定变量等都大写

### 1.3基本select语句

1 基本条件查询 **SELECT field FROM table_name WHERE query**

2 列别名 **SELECT last_name AS name, commission_pct commFROM employees;**

* 紧跟列名，也可以在列名和别名之间加入关键字AS，别名使用双引号，以便在别名中包含空格或特殊的字符并区分大小写。

* AS 可以省略

3 去除重复行 SELECT DISTINCT department_id FROM employees;

* 在SELECT语句中使用关键字DISTINCT去除重复行

* DISTINCT 需要放到所有列名的前面，如果写成 SELECT salary, DISTINCT department_idFROM employees 会报错

* DISTINCT 其实是对后面所有列名的组合进行去重，你能看到最后的结果是 74 条，因为这 74 个部门id不同，都有 salary 这个属性值。如果你想要看都有哪些不同的部门（department_id），只需要写 DISTINCT department_id 即可，后面不需要再加其他的列名了。

4 显示表结构 

```sql
DESCRIBE employees;
或
DESC employees;
```

其中，各个字段的含义分别解释如下：

* Field：表示字段名称。

* Type：表示字段类型，这里 barcode、goodsname 是文本型的，price 是整数类型的。

* Null：表示该列是否可以存储NULL值。

* Key：表示该列是否已编制索引。PRI表示该列是表主键的一部分；UNI表示该列是UNIQUE索引的一部分；MUL表示在列中某个给定值允许出现多次。

* Default：表示该列是否有默认值，如果有，那么值是多少。

* Extra：表示可以获取的与给定列有关的附加信息，例如AUTO_INCREMENT等。

5 分页

分页公式 

```sql
SELECT * FROM table
LIMIT(PageNo - 1)*PageSize,PageSize;
```

6 链接查询

```sql
#实现A - A∩B
select 字段列表
from A表 left join B表
on 关联条件
where 从表关联字段 is null and 等其他子句;
```

```sql
#实现B - A∩B
select 字段列表
from A表 right join B表
on 关联条件
where 从表关联字段 is null and 等其他子句;
```

```sql
#实现查询结果是A∪B
#用左外的A，union 右外的B
select 字段列表
from A表 left join B表
on 关联条件
where 等其他子句
union
select 字段列表
from A表 right join B表
on 关联条件
where 等其他子句
```

```sql
#实现A∪B - A∩B 或 (A - A∩B) ∪ （B - A∩B）
#使用左外的 (A - A∩B) union 右外的（B - A∩B）
select 字段列表
from A表 left join B表
on 关联条件
where 从表关联字段 is null and 等其他子句
union
select 字段列表
from A表 right join B表
on 关联条件
where 从表关联字段 is null and 等其他子句
```

### 1.4当行函数

**基本函数**

* ABS(x)           返回x的绝对值

* SIGN(X)         返回X的符号。正数返回1，负数返回-1，0返回0

* PI() 返回圆周率的值

* CEIL(x)，CEILING(x) 返回大于或等于某个值的最小整数

* FLOOR(x) 返回小于或等于某个值的最大整数

* LEAST(e1,e2,e3…) 返回列表中的最小值

* GREATEST(e1,e2,e3…) 返回列表中的最大值

* MOD(x,y) 返回X除以Y后的余数

* RAND() 返回0~1的随机值

* RAND(x)返回0~1的随机值，其中x的值用作种子值，相同的X值会产生相同的随机数

* ROUND(x) 返回一个对x的值进行四舍五入后，最接近于X的整数

* ROUND(x,y) 返回一个对x的值进行四舍五入后最接近X的值，并保留到小数点后面Y位

* TRUNCATE(x,y) 返回数字x截断为y位小数的结果

* SQRT(x) 返回x的平方根。当X的值为负数时，返回NULL

**字符串函数**

* ASCII(S) 返回字符串S中的第一个字符的ASCII码值

* CHAR_LENGTH(s) 返回字符串s的字符数。作用与CHARACTER_LENGTH(s)相同

* LENGTH(s) 返回字符串s的字节数，和字符集有关

* CONCAT(s1,s2,......,sn) 连接s1,s2,......,sn为一个字符串

* CONCAT_WS(x,s1,s2,......,sn)同CONCAT(s1,s2,...)函数，但是每个字符串之间要加上x

* INSERT(str, idx, len,replacestr)将字符串str从第idx位置开始，len个字符长的子串替换为字符串replacestr

* REPLACE(str, a, b) 用字符串b替换字符串str中所有出现的字符串a

* UPPER(s) 或 UCASE(s) 将字符串s的所有字母转成大写字母

* LOWER(s) 或LCASE(s) 将字符串s的所有字母转成小写字母

* LEFT(str,n) 返回字符串str最左边的n个字符

* RIGHT(str,n) 返回字符串str最右边的n个字符

* LPAD(str, len, pad) 用字符串pad对str最左边进行填充，直到str的长度为len个字符

* RPAD(str ,len, pad) 用字符串pad对str最右边进行填充，直到str的长度为len个字符

* LTRIM(s) 去掉字符串s左侧的空格

* RTRIM(s) 去掉字符串s右侧的空格

* TRIM(s) 去掉字符串s开始与结尾的空格

* TRIM(s1 FROM s) 去掉字符串s开始与结尾的s1

* TRIM(LEADING s1FROM s)去掉字符串s开始处的s1

* TRIM(TRAILING s1FROM s)去掉字符串s结尾处的s1

* REPEAT(str, n) 返回str重复n次的结果

* SPACE(n) 返回n个空格

* STRCMP(s1,s2) 比较字符串s1,s2的ASCII码值的大小

* SUBSTR(s,index,len)返回从字符串s的index位置其len个字符，作用与SUBSTRING(s,n,len)、MID(s,n,len)相同

* LOCATE(substr,str)返回字符串substr在字符串str中首次出现的位置，作用于POSITION(substrIN str)、INSTR(str,substr)相同。未找到，返回0

* ELT(m,s1,s2,…,sn)返回指定位置的字符串，如果m=1，则返回s1，如果m=2，则返回s2，如果m=n，则返回sn

* FIELD(s,s1,s2,…,sn) 返回字符串s在字符串列表中第一次出现的位置

* FIND_IN_SET(s1,s2)返回字符串s1在字符串s2中出现的位置。其中，字符串s2是一个以逗号分隔的字符串

* REVERSE(s) 返回s反转后的字符串

* NULLIF(value1,value2)比较两个字符串，如果value1与value2相等，则返回NULL，否则返回value1
  
  

**日期和时间函数**

获取日期时间

* CURDATE() ，CURRENT_DATE()返回当前日期，只包含年、月、日

* CURTIME() ， CURRENT_TIME()返回当前时间，只包含时、分、秒

* NOW() / SYSDATE() / CURRENT_TIMESTAMP() / LOCALTIME() /LOCALTIMESTAMP()返回当前系统日期和时间

* UTC_DATE()返回UTC（世界标准时间）日期

* UTC_TIME()返回UTC（世界标准时间）时间

日期与时间戳的转换

* UNIX_TIMESTAMP()以UNIX时间戳的形式返回当前时间。SELECT UNIX_TIMESTAMP() ->1634348884UNIX_TIMESTAMP(date) 

* 将时间date以UNIX时间戳的形式返回。

* FROM_UNIXTIME(timestamp) 将UNIX时间戳的时间转换为普通格式的时间

