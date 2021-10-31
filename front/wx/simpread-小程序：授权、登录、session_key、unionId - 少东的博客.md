> 本文由 [简悦 SimpRea 转码， 原文地址 https://blog.csdn.net/qq_33594380/article/details/80431582

微信应用的一个很大的优势就在于使用过程中是不需要进行注册和显式登录的，大部分问题基本上可以一键解决。但是在授权、登录和获取用户信息的过程中都发生了哪些事情，今天我们就来讨论一下。这篇文章主要分析以下几个问题：

*   授权和登录的意义
*   session_key 的作用
*   unionId 的作用，有哪些获取途径
*   在应用中如何保存用户登录态

**1. 授权和登录的意义**

首先必须要明白，授权和登录实际上是两个操作。

**1.1 授权（已废弃）**

那授权的作用是啥呢？从小程序官方文档中我们可以看到授权操作只需通过 wx.authorize() 接口便可以完成，以下是文档中对授权操作的描述：

提前向用户发起授权请求。调用后会立刻弹窗询问用户是否同意授权小程序使用某项功能或获取用户的某些数据，但不会实际调用对应接口。如果用户之前已经同意授权，则不会出现弹窗，直接返回成功。  

也就是说，授权过程实际上只是在小程序前端获得了操作部分 wx 接口的访问许可，**这个过程实际上是不会与开发者服务器发生任何关系的**。那这些访问许可包含哪些内容呢？再来看微信官方提供的 scope 列表：

![](https://img-blog.csdn.net/2018052411141359?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

**注：新版 api 已废弃 wx.authorize()，具体信息查看 [https://developers.weixin.qq.com/miniprogram/dev/api/open.html](https://developers.weixin.qq.com/miniprogram/dev/api/open.html)  
**

**1.2 登录**

所谓的登录就是要让开发者服务器知道当前的用户是谁？**在传统的 web 应用中，我们必须要让用户输入账号和密码才能实现登录操作。但是在微信应用中，我们可以通过微信服务器来完成这个操作，获取到与当前用户对应的唯一标志（openId）**，具体操作实现流程如下：

注：每个用户相对于每个微信应用（公众号或者小程序）的 openId 是唯一的，也就是说一个用户相对于不同的微信应用会存在不同的 openId  

![](https://img-blog.csdn.net/20180524112142305?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

从上图中，我们可以看出，小程序中登录步骤如下：

① 小程序前端使用 wx.login() 从微信服务器获取 code

② 小程序前端将 code 发送给开发者服务器，开发者服务器利用 appId、appSecret 和 code 向微信服务器换换取用户 openId 和 session_key

③ 开发者服务器自定义登录态并将其与 openId 和 session_key 关联起来然后写 session

④ 开发者服务器将登录态返回给小程序前端，小程序前端使用 wx.setStorageSync() 将登录态保存起来

⑤ 小程序前端在执行业务请求时将登录态发送给开发者服务器，以便开发者服务器知道当前操作的用户是哪位。

也就是说，在整个过程中小程序前端是拿不到用户 openId 的，它只能通过开发者服务器发给它的登录态来告诉服务器当前用户的信息。登录过程中涉及 session_key 和 unionId，于是又引出了下面的问题。

**2. session_key 的作用**

那么，session_key 在登录的过程中或者登录完成后起什么作用呢？一起来看一下。

**2.1 wx.getUserInfo**

首先来看一下 wx.getUserInfo 这个 api：

![](https://img-blog.csdn.net/20180524123555865?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

在设置 withCredentials 属性为 true 的情况下，这个 api 可以拿到 encryptedData，iv 等敏感信息，encryptedData 需要使用 session_key 进行解密，解密后可以拿到的数据如下：

![](https://img-blog.csdn.net/20180524123818208?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

也就是说，**session_key** 的作用之一是将小程序前端从微信服务器获取到的 encryptedData 解密出来，获取到 openId 和 unionId 等信息。但是在 1.2 登录过程中我们可以看到开发者服务器是能够直接拿到用户的 openId 信息的，而且 unionId 也是有其他获取途径的，所以 session_key 在这里的作用看起来有点鸡肋。

**2.2 getPhoneNumber**

session_key 更重要的作用大概体现在获取用户手机方面（可能还包含其他敏感信息获取 api）。

![](https://img-blog.csdn.net/20180524124458258?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

从文档中可以看到 getPhoneNumber 返回的用户数据是加密过的，只有使用 session_key 才能解密，而小程序前端没有 session_key，所以无法获取到用户的手机，只能传到开发者服务器进行处理。

#### **3. unionId 的作用，有哪些获取途径？**

关于 unionId 的作用，可以参考 Ref 中的连接。简单来说，就是同一用户针对同意微信公众平台下绑定的所有应用都具有相同的 unionId。

获取途径有三种，在官方文档中写的比较清楚：

![](https://img-blog.csdn.net/20180524125354501?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

**4. 在应用中如何保存用户登录态**

保存用户登录态，一直以来都有两种解决方案：前端保存和后端保存。

**4.1 后端保存**

在 1.2 步骤③ 中写 session 的时候可以直接设定过期时间，定期通知小程序前端重新进行登录（wx.login）。

**4.2 前端保存**

因为 session_key 存在时效性问题（毕竟是用来查看敏感信息），而小程序前端可以通过 wx.checkSession() 来检查 session_key 是否过期。所以可以通过这个来作为保存用户登录态的机制，这也是小程序文档中推荐的方法：

![](https://img-blog.csdn.net/20180524130217777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNTk0Mzgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  

Ref: [微信小程序 获取 session_key 和 openid](https://blog.csdn.net/qq_31383345/article/details/54094021)

Ref: [微信 UnionID 作用](https://blog.csdn.net/u014033756/article/details/52230258)

Ref: [小程序官方文档](https://developers.weixin.qq.com/miniprogram/dev/api/signature.html#wxchecksessionobject)

*   [点赞 25](javascript:;)
*   [收藏](javascript:;)
*   [分享](javascript:;)
*   *   文章举报

 [![](https://profile.csdnimg.cn/3/0/6/3_qq_33594380) ![](https://g.csdnimg.cn/static/user-reg-year/1x/4.png)](https://blog.csdn.net/qq_33594380) [scut_少东](https://blog.csdn.net/qq_33594380) 发布了 54 篇原创文章 · 获赞 79 · 访问量 25 万 + [关注](https://im.csdn.net/im/main.html?user>私信
                        </a>
                                                            <a data-report-click=)