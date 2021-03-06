### 微博个人信息爬虫

​	前段时间比较忙，没怎么更新。这次搞了微博，但是微博毕竟是个国内的大公司，那反爬技术还是厉害的。本来打算搞个多线程直接搞到全站数据的。结果稍微试了一下发现不但封ip、封cookie、动态渲染，甚至还封浏览器代理。所以最终搞了这个阉割版的。其实对于上面的几个封锁都有相应的解决策略：ip池（得花钱没搞，Ps：其实搞了结果ip质量不行就放弃了），cookie池（我没那么多号，只放了一个作为演示），代理池（网上搞了一套），动态渲染的问题也用selenium解决了。本次爬虫针对微博用户的信息，主要是两大功能：单人数据爬取、多人数据爬取。其中多人数据爬取我搞了个粉丝信息爬取，可以先爬取某博主的粉丝id，再对所有粉丝的信息进行爬取。整体来说，这次爬虫真的是我搞了这么久写的最难的一个了。

**功能**

* 个人信息爬取
* 多人信息爬取
  * 粉丝id爬取
    * 粉丝信息爬取
  * 依照给出的id列表对列表中的多人进行爬取

**环境**

1. Windows10
2. python3.7

**使用方法**

本次的使用方法相比之前也要繁琐些，需要用户自己登陆账号找到cookies，放入到cookie池中。

首先给大家提供两个微博的网址，下面的步骤中会用到：[旧版ui](https://weibo.cn)，[新版ui](https://weibo.com/)

1. 首先我们需要一个或多个微博账号（多个账号就可以搭建cookie池了，延长爬虫寿命的重要道具），没有的同学可以自己注册一下。
2. 然后我们分别进入到上面两个网站中，分别登陆我们的微博账号，然后在开发者工具中获取我们需要的cookie，由于github贴的图可能会挂掉，所以这里如何找cookie我贴一个[网站](https://jingyan.baidu.com/article/5d368d1ea6c6e33f60c057ef.html)照着里面的做就可以找到cookie了。
3. 经过上面的步骤我们现在应该有了两种不同的cookie一个是从[旧版ui](https://weibo.cn)中获取的cookie，一个是从[新版ui](https://weibo.com/)获取的cookie，将二者分别粘粘到项目目录中的old_cookies.txt和new_cookies.txt中。如果有多个cookie（多个账号，每个账号的cookie只能算作一个）则一行一个的粘进去就行了（一行粘多个就会报错哦）
4. 填好了上面的cookie之后我们直接到spider.py文件中点击运行即可了。
5. 运行后我们只需要选择我们需要的功能输入即可。

> **tips**
>
> 1. 程序是无法直接运行功能3（爬取粉丝（多人）信息）的，需要先由用户提供一个id列表（提供一个id.txt文件，每个用户的id为一行），或提前执行功能2（爬取粉丝id）的。通过上述两种方法后我们可以获取id.txt文件，然后再执行功能3即可获取用户信息。获取到的用户信息会保存在项目路径的"微博粉丝信息.csv"文件中。
> 2. 因为技术不足的原因，所以爬取的数据可能会存在一些缺漏。如果需要对爬取的数据进行清洗，则需要用户手动运行一个pandas_data_cleaning.py文件，执行过后会生成一个“微博粉丝信息（数据清洗后）.csv”文件。经过清洗后的文件是不存在：重复数据、数据缺失等问题的。但依旧会有部分数据不准确的问题。
> 3. user_agent.txt、new_cookies.txt、old_cookies.txt文件删除后会导致程序无法运行，所以请谨慎删除。
> 3. 如果想要使用ip代理池，请参考：[proxy](https://github.com/srx-2000/spider_collection/tree/master/proxy_pool)

**技术栈**

1. json
2. requests
3. parsel
4. pandas
5. re
6. time
7. os
8. random
9. threading
9. selenium

**更新日志**

* 2021.9.6

​	**Add**

​	已加入ip代理池技术，现在已经可以做到不被封ip了，同时原来的版本本身就附带了user-agent池了，所以这里仅差一个cookie池了，而这个需要大量的账号作为支持，由于本人无法获取到大量的账号所以此功能暂时未实现，但在文件目录中给出了old_cookies.txt和new_cookies.txt文件，所以如果有建立cookie池的需求的话只需要在上述两个文件中添加相应的cookie即可，具体添加方法上面已经给出了。

* 2022.1.4

​	**Add**

1. 新添加了：微博认证，所在地区，教育经历，个性签名，生日星座，5个人属性。

​	**Fix**

1. 修复了因微博ui改版带来的信息获取缺失bug
2. 更改了用户评论数，点赞数，转发数的收集机制，从原先的全部收集，改为如果用户微博数量大于20页的话，那么就仅对用户的前20页的微博进行数据统计，大幅降低了运行时长
3. 个人信息匹配从使用re，改为使用selenium
4. 修复了粉丝爬取时json取值报错的bug
5. 修复因未填写cookie而产生的数组越界bug，并给出明确提示【在新旧cookie.txt文件中更新cookie】
