---
title: 单点登录（SSO）
slug: single-sign-on
categories: 笔记
tags:
  - sso
date: 2025-02-14
---

## 具体理解单点登录

首先，我们先了解一下单点登录是由什么而来的。
## 前言

在早期的时候，项目一般都是单系统，即所有功能都在一个系统上。那时候用户量小，所以单系统项目可以满足需求。下图就是一个单体项目：


![单体项目](https://cdn.img.turingzy.cn/2025/202510181801029.png)


当下，由于用户量激增以及为了方便系统的升级和维护，大多数公司采用的方案是“分布式系统”。直接来说，就是细化功能，将“登录”、“购物”等功能从“商城”中能拆分成多个子系统。如下图：


![示例](https://cdn.img.turingzy.cn/2025/202510181801031.png)


在这种”分体式系统“逐渐流行起来后，出现了一个问题，“随着子系统的增加，用户每次访问应用系统时都需要重新登录”，用户不仅需要记录多个账号和密码，而且浏览体验也不是好。

为了解决用户在多个应用系统中频繁登录的问题，人们随即设计出了**单点登录**（Single Sign-On，简称SSO）。

比如阿里系的[淘宝](https://www.taobao.com/)和[天猫](https://www.tmall.com/)这两个网站，就采用了单点登录的方式。在淘宝完成登录后，就不需要在天猫登录。


![示例](https://cdn.img.turingzy.cn/2025/202510181801032.jpg)


现在知道 SSO 的主要的用途之后，我们再讲讲其设计思路和实现。这里我先提出2个*问题:*

1. 为什么在淘宝登录后，天猫也会完成登录，它们之间如何产生联系的？
2. 在淘宝和天猫这2个系统的后台中它们是如何做到使用同一用户数据的？

对于第二个问题其实很好想到，我们只需要让系统A和系统B使用**同一个数据库**。第一个问题我这里贴几张图来回答。


![|650](https://cdn.img.turingzy.cn/2025/202510181801033.png)


常规登录模式就如上图所示，用户登录系统A后还需要登录系统B、C，非常繁琐。当在我们对A、B、C三个系统采取单点登录（SSO）模式后，就像下图一样，用户通过 SSO 后，系统A、B、C其实就已经完成了登录，对于各个系统之间的联系相当于是在 SSO 中进行构建的。

“系统A和系统B出自同一家公司，那么只要在SSO中构建系统A、B的关系，当我登录了系统A时，SSO 自动帮我完成系统B的登录”。


![|650](https://cdn.img.turingzy.cn/2025/202510181801034.png)


这里，我简要的介绍了下 SSO 的用途和其中涉及到的问题，后续我将基于[Sa-Token](https://sa-token.cc/doc.html#/sso/readme)的单点登录模块进行讲解。

我们其实可以看出来，上图 SSO 端在这里相当于所有子系统登录的*前站*，用户只要没有登录就需要经过 SSO 端，对于后续其他系统的登录，也是通过 SSO 进行调度的。因此，SSO 端我们统一称呼为**认证中心**。

我们需要学习的也就是如何搭建这个 SSO 端，再细分一下，SSO 端其实应该分为 **SSO-Server端** 和 **SSO-Client端**。

然后我再根据系统的搭建方式，由易到难的讲解实现以下单点登录的三个模式：

1. 前端同域，后端同 Redis
2. 前端不同域，后端同 Redis
3. 前端不同域，后端不同 Redis

同域：各个系统前端部署在同一域名下，同 Redis：后台用户数据共享一个 Redis 实例，比如说[淘宝](https://www.taobao.com/)和[天猫](https://www.tmall.com/)这两个系统是前端不同域的模式。

后续我将要讲解如何在以上三个模式下搭建 SSO-Server 和 SSO-Client。

## 模式一 共享Cookie同步会话

>搭建示例在 `https://gitee.com/chiredeqiudong/spring-boot-demo/tree/master/sa-token-demo/sa-token-demo-sso-server`
>也可以直接使用 Sa-Token 官方仓库的demo示例。

首先我们肯定需要搭建一个基本的认证中心（后续称 SSO-Server）。这个认证中心要做到以下功能：


![](https://cdn.img.turingzy.cn/2025/202510181801035.png)

- 这里的登录请求是指所有与 SSO 相关的请求。

```Java
/**  
 * SSO-Server端：处理所有SSO相关请求   
 * http://{host}:{port}/sso/auth  单点登录授权地址
 * http://{host}:{port}/sso/doLogin  账号密码登录接口
 * http://{host}:{port}/sso/checkTicket  Ticket校验接口 
 * http://{host}:{port}/sso/signout  单点注销地址
 */  
@RequestMapping("/sso/*")  
public Object ssoRequest() {  
    return SaSsoServerProcessor.instance.dister();  
}
```

然后我们再根据下图分析模式1下的单点登录：


![示例](https://cdn.img.turingzy.cn/2025/202510181801036.png)


我们主要分析系统B登录的线路，很明显我们需要知道：“为什么 SSO-Server 会通过系统B的登录验证？”。

不用想的是肯定和成功登录系统A有关，而且很明显可以得出这样一个结论：用户通过系统A在 SSO-Server 成功登陆后，SSO-Server 做了**一件事**让系统A、B的*身份得以被标识*，以致于后续用户登录系统B时可以被 SSO-Server *确认身份*。

在经过上述分析后，我们在回顾模式一的特点：“同域，同Redis”。这里重点说明一下会话技术中同域的特点：“**Cookie 共享**”。所谓共享 Cookie，就是主域名 Cookie 在二级域名下的共享，举个例子：写在父域名`stp.com`下的 Cookie，在`s1.stp.com`、`s2.stp.com`等子域名都是可以共享访问的，如下图：


![示例](https://cdn.img.turingzy.cn/2025/202510181801037.gif)


在知道 Cookie 共享这一特点后，SSO-Server 在用户登录成功后做的“那件事”就可以有一个思路了：SSO-Server 将该用户的*登录标识*通过 Cookie 写在了主域名下。

因此，该模式下单点登录的实现方法应该是：用户在登录系统B时，其在 SSO-Server 并不需要登录但是携带的 Cookie 是一个可以表明身份的有效标识，且在 SSO-Server 验证后，B-SSO-Client 成功响应。

理论结束！接下来是demo演示。

修改hosts文件`(C:\windows\system32\drivers\etc\hosts)`，添加以下IP映射，方便单机进行测试：

```
127.0.0.1 sso.stp.com
127.0.0.1 s1.stp.com
127.0.0.1 s2.stp.com
127.0.0.1 s3.stp.com
```

>通过[SwitchHosts](https://github.com/oldj/SwitchHosts)修改Host文件

认证中心和应用端成功启动后，分别打开3个 SSO-Client 应用端：


![示例](https://cdn.img.turingzy.cn/2025/202510181801038.png)


上图是我们模拟的一个多系统登录场景，可以看出来它们都是以`stp.com`域名扩展出来的二级域名。

在`s1`页点击登录，因为我们是第一次登录所以在点击“登录”时会被重定向至`SSO-Server`认证中心进行登录。


![示例](https://cdn.img.turingzy.cn/2025/202510181801039.png)


然后，我们在来看一下成功登录后，SSO-Server 和 SSO-Client 有什么变化：


![示例](https://cdn.img.turingzy.cn/2025/202510181801040.png)


在成功登录后，又重定向回了初始页面，这时候登录状态为true，并且查看 Cookie 可以发现都多出了`satoken`值。然后我们再看Redis的存储情况，如下图，这个`satoken`在 Redis中是用户 ID 的 key ，也就是说这个`satoken`就是前文所说的**用户身份的有效标识**。


![示例](https://cdn.img.turingzy.cn/2025/202510181801041.png)


很好，我们继续系统s2、s3的登录，结果如下图：


![示例](https://cdn.img.turingzy.cn/2025/202510181801042.png)

![示例](https://cdn.img.turingzy.cn/2025/202510181801043.png)


点击登录时，因为有该 Cookie 的存在，所以我们并没有跳转到认证中心（登录自动化了）。这也达到了目标效果：“一次登录，全部生效”。

当点击某一子系统的注销时，其他子系统的登录状态也将注销。虽然浏览器的Cookie还没有过期，但是后台的 Redis 数据被清除了。


![示例](https://cdn.img.turingzy.cn/2025/202510181801044.png)


总结：整个过程中，除了第5步，用户在 SSO 认证中心登录时会被打断，其余过程均是自动化的，当用户在另一个子系统再次点击`[登录]`按钮，由于前端共享 Cookie 得以让 SSO 认证中心确认其身份，所以第四步也将自动化，也就是单点登录的最终目的 —— 一次登录，处处通行。


![示例](https://cdn.img.turingzy.cn/2025/202510181801045.png)


如果我们的子系统*在完全不同的域名下*，我们又该怎么完成单点登录功能呢？

## 模式二 URL重定向传播会话 

>demo搭建示例：`https://gitee.com/chiredeqiudong/spring-boot-demo/tree/master/sa-token-demo/sa-token-demo-sso2-client`

很明显，我们前端部署在不同子域名下，就无法通过共享 Cookie 来同步会话。所以说模式二具体来说就是如何让各个系统有着 SSO-Server 可以认证的身份标识。

模式二是 URL重定向传播会话，这里的“URL重定向”是如何解决无法Cookie共享的呢？下面以图片形式先介绍一下几个角色，分别是“全局会话、子会话、Ticket码”。这三个角色是模式二是能够传播会话的关键。


![示例](https://cdn.img.turingzy.cn/2025/202510181801046.png)


>会话：会话（Session）是指在用户与服务器之间建立的一种逻辑连接，用于在无状态的HTTP协议下维护用户的状态信息。它允许服务器在多次请求之间记住用户的身份、偏好和其他相关信息。
>全局会话：全局会话（Global Session）并不是Java标准中的一个术语，但它通常出现在一些特定的框架或场景中，例如在分布式系统或集群环境下。全局会话的概念是相对于普通会话而言的，它跨越多个服务器实例或应用模块，提供一种全局共享的会话机制。

知道这几个新角色后，式：再根据 Sa-Token 提供的*通过URL重定向传播会话*实现方


![示例](https://cdn.img.turingzy.cn/2025/202510181801047.gif)


1. 用户在 子系统 点击 `[登录]` 按钮。
2. 用户跳转到子系统登录接口 `/sso/login`，并携带 `back参数` 记录初始页面URL。
    - 形如：`http://{sso-client}/sso/login?back=xxx`
3. 子系统检测到此用户尚未登录，再次将其重定向至SSO认证中心，并携带`redirect参数`记录子系统的登录页URL。
    - 形如：`http://{sso-server}/sso/auth?redirect=xxx?back=xxx`
4. 用户进入了 SSO认证中心 的登录页面，开始登录。
5. 用户输入账号密码并登录成功，SSO认证中心再次将用户重定向至子系统的登录接口`/sso/login`，并携带`ticket码`参数。
    - 形如：`http://{sso-client}/sso/login?back=xxx&ticket=xxxxxxxxx`
6. 子系统根据 `ticket码` 从 `SSO-Redis` 中获取账号id，并在子系统登录此账号会话。
7. 子系统将用户再次重定向至最初始的 `back` 页面。

整个过程，除了第四步用户在SSO认证中心登录时会被打断，其余过程均是自动化的，当用户在另一个子系统再次点击`[登录]`按钮，由于*此用户在SSO认证中心已有会话存在*， 所以第四步也将自动化。

总结以上过程，相对模式一的根本改变是`token`不再是通过 Cookie 写到主域名下，而是通过URL重定向的方式将`ticket`作为请求行参数传给子系统。子系统后端根据`ticket`码从`Redis`获取账号信息。这里的`ticket`码我们称之为跨域登录凭证。

比较疑惑的是B系统登录请求的`token`出自何处？仔细分析 <span style="text-decoration: underline; text-decoration-color: red;"> 此用户在SSO认证中心已有会话存在</span> 这段话，这说明B系统登录请求的`token`同“用户与认证中心的会话”有关。

分析整个流程，用户和认证中心可以建立会话的情况，也就是在当前系统未登录然后被重定向的这个时期才会建立会话，也就是说B系统登录请求的`token`大概率是用户在系统A登录时同认证中心会话保留的。

理论结束，后续根据实践的方式逐步探索系统B发出登录请求的`token`来自何处。

首先修改hosts文件`(C:\windows\system32\drivers\etc\hosts)`，添加以下IP映射：

```
127.0.0.1 sa-sso-server.com
127.0.0.1 sa-sso-client1.com
127.0.0.1 sa-sso-client2.com
127.0.0.1 sa-sso-client3.com
```

用户在系统A登录时被重定向至 SSO-Server：


![示例](https://cdn.img.turingzy.cn/2025/202510181801048.png)


上述三张图片分别是 Redis 存储情况、SSO-Server 的重定向 url 以及携带的请求 Cookie（上次测试遗留）。按照之前的理论分析，因为此时用户还没有和认证中心*建立会话*所以在登录系统A时是没有*正确请求 Cookie* 的，所以验证不成功需要重新登录。

正确登录后，我们看发现了那些变化：


![](https://cdn.img.turingzy.cn/2025/202510181801049.png)


我们可以看到 Redis 中有了共享数据，然后我们继续登录系统B，我们需要明确的是因为跨域限制，B-SSO-Client 是肯定不会有 A-SSO-Client 中的请求 Cookie，也就是`token`。


![示例](https://cdn.img.turingzy.cn/2025/202510181801050.png)


全部流程结束，如上图所示系统B登录时总共**三次请求**。第一个是在 B-SSO-Client 中进行登录请求，因为用户尚未在系统B登录所以重定向至 SSO-Server，也就是第二个请求。可以看到在第二张图片中，请求 Cookie 是存在的并且就是当前用户的 token 值，因此认证成功携带`ticket`重定向至登录页，也就是第三个请求。系统B根据 `ticket码` 从 `Redis` 中获取账号id，并在子系统登录此账号会话，完成登录。

系统B发出登录请求的`token`来自何处，现在有了确切的答案。这是因为在前面访问系统A的时候，已经通过 SSO 完成登录，SSO 站点域名的 cookie 中就保存有 token；所以后面访问系统B，就可以直接从 SSO 站点域名 cookie 中读取到 token，通过身份认证，不需要重新登录。

我们再说一下单点登出的实现过程，主要有2步操作：

1. SSO 服务端清除 Redis 中的登录状态
2. 浏览器清除 SSO 站点域名下 cookie 中存储的 token，以及**进行登出操作的业务站点域名下 cookie 中存储的 token**。


![示例](https://cdn.img.turingzy.cn/2025/202510181801051.png)


最后，总结一下“URL重定向传播会话”的关键在于**跨域会话管理**。下图为全局流程图（赋文字解释）。


![示例](https://cdn.img.turingzy.cn/2025/202510181801052.png)

![示例](https://cdn.img.turingzy.cn/2025/202510181801053.png)

>上三张图取自：[SSO单点登录设计实现](https://blog.csdn.net/incredible1024/article/details/134645247)

以上流程解决了跨域模式下的单点登录和登出，但是后端采用了共享 Redis 来同步会话，如果我们的架构设计中 SSO-Client 端与 SSO-Server 端无法共享 Redis，又该怎么完成单点登录？

## 模式三 Http请求获取会话

>demo搭建示例：`https://gitee.com/chiredeqiudong/spring-boot-demo/tree/master/sa-token-demo/sa-token-demo-sso3-client`

首先还是先明确，Client 端与 Server 端无法共享 Redis 对于模式一和模式二的影响有哪些。

根据之前的实现过程截图，可以知道 Redis 中主要存储的是以`token`或者`ticket`作为 key 的用户数据，而如果后端没有共享 Redis，在用户登录后用户数据保存在`x-redis`（某个服务，一般是 SSO-Server），其他系统按照之前的模式是无法获取到用户数据的。所以第一个重要影响就是多个服务之间无法共享用户数据。这也导致 Client 端无法与 Server 端共用一套会话，需要自行维护子会话。

第二个重要影响就是无法“一次注销，全端下线”，需要额外编写代码完成单点注销。


![示例](https://cdn.img.turingzy.cn/2025/202510181801054.png)


模式三就是要在模式二的基础上消除这两个重要影响。首先，在不能共享 Redis 数据后，上图中的三角关系将变为下图中的二元关系。


![](https://cdn.img.turingzy.cn/2025/202510181801055.png)


子系统的用户数据只能来源于 SSO-Server，所以我们需要解决以什么方式从 SSO-Server 获取。

在模式三中我们是通过`http`请求来获取会话，只需要 SSO-Server 提供一个在验证用户身份后**提供数据**的接口。这里我们直接看代码：

1、SSO-Client端 `http` 请求处理器

```java
// 配置SSO相关参数 @Autowired  
private void configSso(SaSsoClientConfig ssoClient) {  
    // 配置Http请求处理器   
	ssoClient.sendHttp = url -> {  
       System.out.println("------ 发起请求：" + url);  
       String resStr = Forest.get(url).executeAsString();  
       System.out.println("------ 请求结果：" + resStr);  
       return resStr;  
    };  
}
```

2、SSO-Server 端数据查询接口

```java
// 示例：获取数据接口（用于在模式三下，为 client 端开放拉取数据的接口）
@RequestMapping("/sso/getData")
public SaResult getData(String apiType, String loginId) {
    System.out.println("---------------- 获取数据 ----------------");
    System.out.println("apiType=" + apiType);
    System.out.println("loginId=" + loginId);

    // 校验签名：只有拥有正确秘钥发起的请求才能通过校验
    SaSignUtil.checkRequest(SaHolder.getRequest());

    // 自定义返回结果（模拟）
    return SaResult.ok()
            .set("id", loginId)
            .set("name", "LinXiaoYu")
            .set("sex", "女")
            .set("age", 18);
}
```

3、SSO-Client端调用认证中心的数据查询接口

```java
// 查询我的账号信息 
@RequestMapping("/sso/myInfo")
public Object myInfo() {
    // 组织请求参数
    Map<String, Object> map = new HashMap<>();
    map.put("apiType", "userinfo");
    map.put("loginId", StpUtil.getLoginId());

    // 发起请求
    Object resData = SaSsoUtil.getData(map);
    System.out.println("sso-server 返回的信息：" + resData);
    return resData;
}
```

这个单点登录的实现流程和模式二的主要区别在于客户端不能够直连 redis 获取数据，需要发送`http`请求向认证中心来共享数据。

在该模式中我觉得**单点注销**也是非常需要学习的操作。在前面 2 个模式中我们只需要消除一一个 redis 实例中的数据即可，现在由于多个服务使用不同的 redis，所以单点注销的难度大大增加。

好在 sa-token 很好的解决了这个问题，下面是”单点注销“的流程。

3. Client 端在校验 ticket 时，将注销回调地址发送到 Server 端。
4. Server 端将此 Client 的注销回调回调信息存储到 List 集合。
5. Client 端向 Server 端发送单点注销请求。
6. Server 端遍历Set集合，逐个通知 Client 端下线。
7. Server 端注销下线。
8. 单点注销完成。


![示例](https://cdn.img.turingzy.cn/2025/202510181801056.png)


很明显在服务端和客户端中我们还需要设计有关注销的接口以及一些设置，具体请看-[sa-token讲解](https://sa-token.cc/doc.html#/sso/sso-type3?id=_5%e3%80%81%e6%97%a0%e5%88%b7%e5%8d%95%e7%82%b9%e6%b3%a8%e9%94%80)。

## 后记

>以下总结非常到位，摘自`https://sa-token.cc/doc.html#/sso/sso-type3?id=_6%e3%80%81%e5%90%8e%e8%ae%b0`

当我们熟读三种模式的单点登录之后，其实不难发现：所谓单点登录，其本质就是**多个系统之间的会话共享**。

当我们理解这一点之后，三种模式的工作原理也浮出水面：

- 模式一：采用共享 Cookie 来做到前端 Token 的共享，从而达到后端的 Session 会话共享。
- 模式二：采用 URL 重定向，以 ticket 码为授权中介，做到多个系统间的会话传播。
- 模式三：采用 Http 请求主动查询会话，做到 Client 端与 Server 端的会话同步。

# 参考资料

【会话技术】https://mp.weixin.qq.com/s/JW7mxXEqrV1rZ_pQOteXGQ
【什么是单点登录】https://zhuanlan.zhihu.com/p/66037342
【漫谈单点登录】https://www.cnblogs.com/EzrealLiu/p/5559255.html
【单点登录原理和简单实现】https://www.cnblogs.com/ywlaker/p/6113927.html
【CAS执行原理探究】https://blog.csdn.net/javaloveiphone/article/details/52439613
【Sa-Token单点登录机制（源码分析）】https://blog.csdn.net/m0_51433562/article/details/127717892