# 微信代理-wechat-agent

#### 微信代理-wechat-agent

微信授权认证获取用户 openid 时序图

1 第一步：用户同意授权，获取code

2 第二步：通过code换取网页授权access_token

3 第三步：刷新access_token（如果需要）

4 第四步：拉取用户信息(需scope为 snsapi_userinfo)


![](https://tva1.sinaimg.cn/large/006tNbRwly1gax9bftycbj31p60m8ta0.jpg)


>常规微信认证流程中，redirect_uri必须在微信后台网页授权域名中配置，否则无法跳转，而微信后台对于网页授权域名有数量限制目前可以配置2个，为了扩大微信认证的使用场景，wechat-agnet提供了代理认证功能，wechat-agnet会判断redirect_uri是否为授权域名并执行不同逻辑。


1、如果认证的域名已经在微信后台配置，认证过程如下：

![](https://tva1.sinaimg.cn/large/006tNbRwly1gax9dzetctj31g50u0400.jpg)


2、如果认证的域名未在微信后台配置(wechat-agent主要针对次场景)，认证过程如下：

![](https://tva1.sinaimg.cn/large/006tNbRwly1gax9eslc2ej319m0u0mzc.jpg)




1、wechat-agent的应用场景
a、调用微信接口需要使用appid、secret，这两项值十分重要，不建议暴露给第三方，通过wechat-agent可以隐藏微信的appid、secret；
b、微信的accessToken为独占资源，刷新accessToken会使旧token失效，只能由一个调用方进行刷新，通过wechat-agent可以集中管理微信的accessToke；
c、业务上有多个第三方应用需要使用微信接口能力，通过wechat-agent可以自由的创建代理，每个代理都能通过wechat-agent获得完整的微信接口能力；
d、某些网络环境下，服务器无法直接访问微信接口，通过wechat-agent进行网络代理转发；

2、wechat-agent实现原理
wechat-agent实现了一套与微信类似的appid/secret/accessToken机制，第三方应用首先通过wechat-agent分配的appid/secret调用wechat-agent接口获得accessToken，然后使用此accessToken调用wechat-agent接口，wechat-agent接口校验assessToken后将调用请求转发给微信，并且将参数换成微信的accessToken，最后把返回值返回给调用方。原理图如下：


![](https://tva1.sinaimg.cn/large/006tNbRwly1gax9kcvvosj31ba0syt9q.jpg)

3、调用wechat-agent必要资料
a、获得wechat-agent分配的appid和secert
b、获得wechat-agent的接口地址（wechat-agent-url）