# SIP业务基础

[TOC]

### 呼叫等待

> 通话期间，有新用户打进来，新进来的用户被置于等待；案例还包括了呼叫保持过程

![wh-hjdd][101]

```s
（1）AB正常通话。
（2）在AB通话的阶段，用户C向A发起呼叫，终端代理C发送Invite消息给代理服务器。
（3）代理服务器向终端C回送100Trying响应，表示呼叫已在处理中。
（4）代理服务器把Invite消息转发给A。
（5）用户A振铃，并且终端A向代理服务器发送182 Queued响应
（6）代理服务器向终端C转发该182 Queued响应信息
新进用户C被置于等待

（7）用户A按下呼叫保持键(拍叉键)，代理终端A向代理服务器发送Invite消息，请求与代理终端B呼叫保持(in-dialog)
（8）代理服务器向终端A回送100Trying响应，表示呼叫已在处理中
（9）代理服务器转发此消息给终端代理B。
（10）终端B向代理服务器回送100Trying响应，表示呼叫已在处理中
（11）终端B收到呼叫保持请求后，发送200OK给代理服务器，表示接受呼叫保持
（12）代理服务器转发200OK响应给终端代理A
（13）代理终端A收到消息后向代理服务器发送ACK消息进行确认
（14）代理服务器将ACK确认消息转发到代理终端B
A、B之间呼叫保持

（15）终端代理A发送200OK给代理服务器，表示接受C的呼叫。
（16）代理服务器转发200OK给终端代理C。
（17）终端代理C向代理服务器回送ACK确认。
（18）代理服务器向代理终端A转发收到的ACK确认。
A、C之间开始通话。

（19）用户C挂机，终端代理C向代理服务器发送Bye请求消息。
（20）代理服务器转发Bye消息给终端代理A
（21）终端代理A发送200OK给代理服务器，表示接受请求。
（22）代理服务器转发200OK响应给终端代理C
A、C之间通话结束

（23）终端代理A重新发送Invite请求给代理服务器，请求和终端代理B恢复通话。
（24）代理服务器向代理终端B转发收到的Invite请求。
...
```

**概括**：
(1)新进用户C发起INVITE请求，A先向B发送**182 Queues**响应，然后A发起与B呼叫保持请求，请求成功后(与B呼叫保持)，发送200OK给C，C回应ACK，A与C通话<br>
(2)A要恢复与B的通话需要重新发起请求消息



### 呼叫保持

> 暂停与原来的电话(呼叫保持)， 打出新的电话

![wh-hjbc][201]

...
用户A按下呼叫保持键，代理终端A向代理服务器发送Invite消息，请求与代理终端B呼叫保持，然后呼出新的电话(C)

**问**：**呼叫保持怎么实现**？**同样是INVITE请求，如何判断这个INVITE是用来实现呼叫保持的**?

> 参考文章：[《SIP通话中Hold功能的实现 》](http://blog.sina.com.cn/s/blog_414e587f010008oh.html)

通过INVITE携带的SDP内容不同来实现，用于呼叫保持的`INVITE中的SDP`/`对方回复200 OK中的SDP`主要特殊地方：

![hold][204]


而正常的INVITE和200 OK中SDP的媒体属性(a)都为**sendrecv**


### 呼叫转移


#### 呼叫转移（盲转）

> 座席电话拍叉后拨打相应电话，然后直接挂机

终端代理

![wh-hjzy-m][301]

...
用户A按下呼叫保持键，代理终端A向代理服务器发送Invite消息，请求与代理终端B呼叫保持(in-dialog)，然后呼出新的电话(C)并挂机。用户A对B发送REFER请求，B接受REFER请求并主动向C呼叫(B呼叫C之前会向A发送呼叫保持请求)



#### 呼叫转移（询问）

> 座席电话拨打相应电话后，等待 `响铃或通话成功后讯问` 后再进行下一步处理

![wh-hjzy-xun][303]


### 呼叫前转（无应答、遇忙和无条件）

> 略



### 三方通话

> 前提条件：B开通三方通话；操作流程：1、B呼A，A接听；2、B拍叉呼C；3、C接听；4、B拍叉按3；



(1)AB通话
(2)A按下拍叉键，请求与B呼叫保持(re-INVITE)，然后呼叫C(INVITE)，AC通话
(3)A按下拍叉键，请求与C呼叫保持(re-INVITE)，然后按下3后A会向B、C发送INVITE解保持；此时B、C是没有建立通话的，而是经过A这里的语音混合之后再发给B、C

















[101]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/01%E5%91%BC%E5%8F%AB%E7%AD%89%E5%BE%85%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/ws-hjdd-001.png
[201]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/02%E5%91%BC%E5%8F%AB%E4%BF%9D%E6%8C%81%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/wh-hjbc-001.png
[202]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/02%E5%91%BC%E5%8F%AB%E4%BF%9D%E6%8C%81%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/hold-001.png
[203]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/02%E5%91%BC%E5%8F%AB%E4%BF%9D%E6%8C%81%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/hold-002.png
[204]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/02%E5%91%BC%E5%8F%AB%E4%BF%9D%E6%8C%81%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/hold.png
[301]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/03%E5%91%BC%E5%8F%AB%E8%BD%AC%E7%A7%BB%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/wh-hjzy-m-01.png
[302]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/03%E5%91%BC%E5%8F%AB%E8%BD%AC%E7%A7%BB%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/wh-hjzy-m-02.png
[303]:https://raw.githubusercontent.com/TongxinV/xxxx001/master/assets/03%E5%91%BC%E5%8F%AB%E8%BD%AC%E7%A7%BB%E6%8A%A5%E6%96%87%26%E5%9B%BE%E7%89%87/wh-hjzy-xun-01.png