# Session 和  JWT

## 一、Session

### 	    1.session登录流程

>  1.浏览器 --- 第一次访问  --->服务器 --->response --->cookie ---> sessionId  --->保存
>
>  2.请求 ---> sessionId(存放在cookie中)  --->根据sessionId 找到 Session 对象  -->在对象里面找到 登录标记<key - value>
>
>   注意 ： cookie  ---  HTTP请求头





### 对于现阶段  -- 大前端时代

#### **抛弃session，不是因为无法实现功能 ，而是在不同的应用场景中session 过于麻烦**

> 原因：1） 要求在cookie中存储,cookie时HTTP协议的内容
>
> ​			2）app、小程序对cookie支持有限
>
> ​			3）session机制属于Java Web 中的 底层实现,拓展session机制会比较复杂



#### 采用Token的方式

##### 1.问题

> 1） token 是如何产生的？
>
> ##### 		1. 一个随机产生的无意义字符串，没法校验
>
> ##### 		2. Token 必须要能够识别出 服务器的标记
>
> 2） 怎么对 token 进行校验？
>
> ##### 		Jwt本身定义了解析的相关内容
>
> 3） token有限制吗？
>
> ##### 		1.生成、传递、保存 是根据自己系统设计的
>
> ##### 		2.如果是登录接口调用 每次都会传递Token 不应该太大
>
> 4） 如何传递token？
>
> ##### 		根据前后端约定 自行传递（任意一种传递方式）
>
> 5） token的安全性如何保证？
>
> 6） token过期了怎么办？

## 二、JWT

![image-20201027171013290](C:\Users\wh-wisdom\AppData\Roaming\Typora\typora-user-images\image-20201027171013290.png)

####  1、jwt-token作用

> 定义了token的内容、生成token、提供校验规则

#### 2、Token的好处

* 方便、简单
* 系统扩展性强

#### 3、安全性论证

* 有效期 

  * token内容中附带有效期，服务端可以判断token是否到期，

  而服务器也应该提供刷新token有效期的接口

* 盗取 

  * Session的保护措施，通过https保存传输过程中的安全性。

  客户端本身可以通过 安全控件（网银 支付宝）

* token数据不加密，做了base64编码

  * 不能存放敏感信息

* 篡改

  * 已登录拿到token的情况下   修改token中的 用户数据 

  * 签名值  

    * 可以有效地防止数据被篡改和伪造  --> 计算(第一部分 +  第二部分) 的hash 值
    * 签名值只有服务端可以计算出来, 签名计算过程中 添加了密码（盐值salt）

    