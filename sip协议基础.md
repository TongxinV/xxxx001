# SIP协议基础

[TOC]

### 1.SIP协议概念

1.1 SIP协议

> SIP 协议虽然属于应用层协议，然而 SIP 本身并不提供任何服务。但是 SIP 是通信的基础，在 SIP 这个通信的基础上可以用来构建不同的服务

SIP（会话发起协议）属于IP应用层协议，用于在IP网上为用户提供会话应用。会话（Session）指两方或多方用户之间的语音、视频、及其他媒体形式的通信，具体可能是IP电话、会议、即时消息等等

![p-sip-0001]()

1.2 协议栈

SIP是一个信令协议，它对应于传统电话网络中的呼叫信令协议（比如SS7 ISUP）。构建一个完整多媒体通信架构还需要结合其他一些协议，必要的有：RTP，用于媒体传输；RSVP，用于QOS保证等等

![p-sip-0002]()

1.3 基本功能

1.3.1会话的发起与管理

> SIP主要用于创建、修改和终止一个会话

一个**创建会话**的简单的例子如下图所示：

![p-sip-001]()

```
发起方向目的方发送一个SIP请求消息（INVITE），其中包含提议的会话参数的描述，请求在二者之间建立一个会话；
目的方返回一个SIP响应消息（200 OK），其中包含接受的会话参数的描述，接受会话建立请求
发起方发送一个SIP请求消息（ACK）确认会话的建立。
```

一个**修改会话**的简单例子如下图所示：

![p-sip-002]()

```
会话中的任意一方可以发送一个SIP请求消息（reINVITE），其中包含提议的新的会话参数，请求修改二者之间的会话；
另一方返回一个SIP响应消息（200 OK），其中包含接受的新的会话参数，接受会话修改请求
```

一个**结束会话**的简单例子如下图所示：

![p-sip-003]()

```
会话中的任意一方可以发送一个SIP请求消息（BYE），请求结束会话；
另一方返回一个SIP响应消息（200 OK），接受会话结束请求
```

1.3.2 用户位置管理

> 用于支持用户（终端）的移动性。猜测参与的服务器为注册服务器（Register Server）加位置服务器（Location Server）

![p-sip-004]()


### 2.SIP体系结构(基本网络模型)

> SIP协议组件：SIP 用户代理（User Agent，UA）和 SIP 用户服务器（User Server，US）

![p-sip-005]()

**用户代理UA**本身又可分为：客户机端UAC（User Agent Client，用户代理客户机）和服务器端UAS（User Agent Server，用户代理服务器）。用户代理客户机是发起请求的主叫应用；客户代理服务器是通话的被呼叫端，主要负责接受、重定向或者拒绝请求，给到来的请求发送响应。用户代理在发起呼叫的时候它是用户代理客户端，而当被呼叫的时候它是用户代理服务器端

**用户服务器US**是 SIP 消息在到达其最终目的地前所经过的逻辑节点，这些服务器用于对请求进行路由和重定向，既进行名字解析和用户定位。主要包括以下几种服务器：
```
注册服务器（Registrar Server）：负责接收和处理 REGISTER 请求的服务器；
有状态代理服务器（Proxy Server）：负责接收和转发SIP请求。可解析并重构SIP消息的部分信息。但是这些重构不会影响请求或会话的状态
无状态代理服务器（Stateless Server）：其负责将上游的请求向下游转发和将下游的响应向上游转发，其不纪录请求或会话的状态
重定向服务器（Redirect Server）：负责将请求的地址映射为新地址；它对请求进行重定向但是不参与事务的处理
位置服务器（Location Server）：跟踪用户的位置；
```

### 3.SIP协议结构

> 事务用户层\事务层\传输层\语法和编码层

暂时没遇到要去分析这一层面的内容，有空再补充


### 4.SIP事务与SIP消息

> SIP每个操作体现为一个所谓的事务(由一个request、0到多个临时response以及一个最终response组成)

**SIP事务**：SIP所提供的功能(比如语音业务、三方通话等)是通过一些原子性的基本功能（比如注册（registration），发起会话（Initiation）、会话结束等）**组合**而成。每个原子性基本功能是通过一个SIP操作完成的。SIP操作基于类似HTTP的请求/响应事务模型，每个操作的调用过程体现为一个所谓**事务**。其中SIP请求消息中的方法（Method）指示出调用的操作

![p-sip-006]()

下表是在RFC 3261中定义的一些基本方法及其对应的SIP操作(不是全部):

| 方法Method    | SIP操作(事务)  | 
| :------------ |:---------------|
| INVITE        | 会话邀请(修改) |
| ACK           | 确认会话邀请   | 
| CANCEL        | 取消会话邀请   | 
| BYE           | 结束会话       |
| REGISTER      | 注册           | 
| OPTIONS       | 查询服务器能力 |

**SIP消息**：SIP 消息有两类：请求消息（request）和响应消息（response）。除了第一行分别是请求行（Request-Line）和状态行（Status-Line）以外(有统称为起始行start line)，SIP请求消息和SIP响应消息的剩下部分的组成基本类似，包括消息头域（message header）和消息体（message body）两部分

![p-sip-007]()

#### SIP请求消息

根据请求行中的方法(method)的不同，SIP请求消息有很多种，分别完成各种操作的调用，不同操作可以自由组合以实现各种功能，下面进行简单的介绍：

* INVITE/**reINVITE**：INVITE 发起会话邀请。reINVITE（在已存在的对话中发送的INVITE称为reINVITE）修改已建立会话的参数
* ACK：完成会话建立的3次握手 [INVITE-200-ACK],仅仅用于INVITE
* BYE ：结束会话。
* CANCEL：取消正在建立中会话(INVITE已发送，但尚未收到最终响应（final response）)。
* **UPDATE**：更新会话参数。它被建议用于替代re-INVITE，与re-INVITE不同在于：它可以在初始INVITE未完成时发送，能用于在早对话(early dialog)中更新会话参数。

上面的5个请求可用于会话建立与管理

* REGISTER：登记UA当前的联系地址（contact）
* OPTIONS：查询服务器或对端UA的能力，具体包括支持的方法（method），扩展（extensions）、编解码（codecs）等。
* PRACK：临时响应（Provisional Response）确认。用于确认收到了临时响应，例如 “183 Session  Progress”，以支持临时响应的可靠传送。它不能应用于“100 Trying ”，只有101～199 临时响应需要可靠传送。如果没有收到 PRACK，响应消息将被重传。
 
* NOTIFY：事件通知，具体的事件包括、业务状态的改变（MWI，...），用户状态的改变等等。
* SUBSCRIBE：订阅/取消(Expires=0）事件通知。
* PUBLISH：发布事件状态。PUBLISH与REGISTER的相似之处在于：允许用户在另一个实体（状态管理实体/registrar）中创建、修改和移除自己的状态。对PUBLISH 请求的寻址与对于SUBSCRIBE 请求的寻址是一样的，PUBLISH 请求的Request-URI 中填入的是用户希望发布其事件状态的资源地址

上面的3个请求可构成SIP事件发布-订阅-通知机制

* INFO：用于传送 mid-call 信令信息，同一时刻只能有一个 INFO 事务存在。通常用于携带 PSTN 信令消息（作为 MIME 附件），例如，ISDN UUI （用户到用户信息）。
* MESSAGE：针对即时消息 (IM)的扩展，用于传送即时消息。MESSAGE请求通过MIME附件中携带内容。MESSAGE 请求自身不发起 SIP 对话，在正常的用法中，每个即时消息都是单独存在的，非常类似 pager 消息。MESSAGE 请求可以在其他SIP请求发起的对话上下文中发送。
* **REFER**：指示接收者 (Request-URI所标识的) 应该使用请求中提供的信息联系第三方。典型应用：Call Transfer features 。Allowed outside an established dialogue。

#### SIP响应消息


|状态码|描述|例子|
|:---|:---|:---|
|1xx | Informational<br>请求收到，处理中             | 180 Ringing<br>181 Call is Being Forwarded |
|2xx | Success<br>操作已成功完成                     | 200 OK|
|3xx | Redirection<br>请求被重定向                   | 300 Multiple Choices<br>302 Moved Temporarily|
|4xx | Client Error<br>请求包含错误的文法，或者无法在本服务器上完成 | 401 Unauthorized<br>408 Request Timeout
|5xx | Server Error<br>请求有效，但服务器无法完成    | 503 Service Unavailable<br>505 Version Not Supported
|6xx | Global Failure<br>请求在任何服务器上都无法完成| 600 Busy Everywhere<br>603 Decline

关于每个错误码的具体含义见这里：[sip Response Codes(响应码)]()
关于错误的处理见这里：[...(略)文章根据工作遇到的再补充...]()


#### SIP消息头域

> 前6个头域是所有SIP消息中的必需的头域

| 消息头域 | 描述| 
|:---|:---|
| Via | 用于记录请求经由的路径<br>例：<br>Via: SIP/2.0/UDP 192.168.78.165:44794;branch=z9hG4bK-d87543-0625ef7c430eb355-1--d87543-;rport<br>Via:...<br>...<br>Via头域是被服务器插入request中，用来检查路由环的，并且可以使response根据via找到返回的路。**它不会对未来的request 或者是response造成影响** |
| 
| Call-ID | 用于唯一标识一个特定的会话或注册消息<br>应具有随机性，保证全球唯一 | Call-ID: NGNlM...SDF...DIyYjQ |
| From | 源端SIP URL，标识请求发送方；UAC本地标签<br>例：<br>From: `"8888"<sip:8888@192.168.78.141>`;tag=b40d6a51<br>如果一个SIP消息中没有Contact或者Record-Route头域，那么callee就会根据From头域作为后续的Request消息头中的相关头域的内容，比如：如果Alice打一个电话给Bob，From头域的内容是 From:Alice<sip:alice@example.org>。那么Bob打给Alice时就会使用 sip:alice@example.org作为To头域和Request-URI头域的内容<br>**总的来说，如果有Route，request就应该根据Route发送，如果没有就根据Contact头域发送，如果连Contact都没有，就根据From头域发送** |
| To   | 目标SIP URL，标识请求接受方；UAS本地标签<br>例：<br>INVITE 中的To: `"1212"<sip:1212@192.168.78.141>`;<br>180响应中的To: `"1212"<sip:1212@192.168.78.141>`;tag=e9f70d91(由uas添加） |
| Max-Forwards | 消息最大转发次数。服务每次转发消息时将此域值减1，当变成0时，服务器发送 483响应（Too Many Hops response）|  |
| Cseq | 请求序列号，用于区分同一个会话中的不同请求 |
| | |
| Contact | 后续Request将根据Contact头域的内容决定目的地的地址，同时将Contact头域的内容放到Request-URI中 |
| Record-Route/Route |  Record-Route头域一般是被proxies插入到request中的，这样后续的Request如何有着和前面一样的call-id就会被路由到这些proxies，它也会被User Agent作为发送后续request的依据，Record-Route用于记录路由路径。将来用来构造request中的Route头域<br>详细细节可以参考http://blog.csdn.net/stefmoon/article/details/5532441 |
| | | 
| Supported | 列举出UAC或者UAS支持的扩展 | 
| Allow | 列举出UAC或者UAS支持的方法(method) |
| Accept | 接受的消息体(Body)协议，比如Http、SDP|
| PRACK | SIP可选的扩展方式，用于支持临时应答的可靠性，是保证临时消息(101-199)可靠传输的机制。<br>PRACK的实现：UAC与UAS对是否支持该扩展的协商，就是通过一个option tag -- 100rel<br>参考文章：《有关SIP中的PRACK的含义和使用?》http://blog.chinaunix.net/uid-790245-id-2037585.html<>
 《SIP ——了解PRACK》http://blog.csdn.net/wind19/article/details/7440839 |

| |
|:---| 
| 其余省略 |



### 一个简单的例子(建立、修改和控制多媒体会话)

> https://www.ibm.com/developerworks/cn/opensource/os-cn-sip-intro/#major4







附：rfc3261文档结构


