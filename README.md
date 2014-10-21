Sqlmap
======

注入分法不同，种类不同，来个简单的分类：<br/>

        1.get型:        sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx"
        2.post型:       sqlmap -u "http://xxx.xx.xxx/xx.xxx" --data="xxxx=xxxx&xxxx=xxx"
        3.cookie类注入: sqlmap -u "http://xxx.xx.xxx/xx.xxx?xx=xxx" --cookie="xxx=xxx&xxx=xxx" --level=2


