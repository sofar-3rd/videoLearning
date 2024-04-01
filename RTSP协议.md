# rtsp协议解析

## rtsp简介

rtsp（Real Time Streaming Protocol，RFC2326），实时流传输协议，是TCP/IP协议体系中的一个**应用层协议**。规定了一对多应用程序如何有效地通过IP网络传送多媒体数据。

RTSP是基于**RTP**和**RTCP**进行多媒体数据传输。

* RTP：用于传输媒体数据。
* RTCP：用于交互控制命令。

## rtsp基本交互流程

![alt text](/assets/RTSP流程.png){:height="80%" width="80%"}

* **OPTIONS**：
  * 客户端请求服务端的可用指令。
  * 服务器端回复当前可用的指令。
* **DESCRIBE**：
  * 客户端向服务器请求媒体描述文件，通过rtsp开头的url来发起请求，格式为sdp。
  * 服务器回复客户端sdp文件，该文件告诉客户端服务器有哪些音视频流，有什么属性，如编解码器信息，帧率等。
* **SETUP**：
  * 客户端向服务器端发起建立连接请求，请求建立会话连接，准备开始接收音视频数据，请求信息描述了期望音视频数据包基于UDP还是TCP传输，指定了RTP，RTCP端口，以及是单播还是组播等信息。
  * 服务器端收到客户端请求后，根据客户端请求的端口号确定发送控制数据的端口以及音视频数据的端口。
* **PLAY**：
  * 客户端向服务端请求播放媒体。
  * 服务器回复客户端200 OK! 之后开始通过SETUP中指定的端口开始发送数据！
* **TEARDOWN**：
  * 向服务器发起关闭连接会话请求。
  * 服务端收到消息后，向客户端发送200 OK，之后断开连接。

## rtsp的消息结构

请求消息格式：
![alt text](/assets/RTCP请求消息格式.png)
字段|说明
---|---
方法|OPTIONS, DESCRIBE, SETUP, TEARDOWN, PLAY
URI|接受方地址，如rtsp://192.168.1.201:554

回复消息格式：
![alt text](/assets/RTCP回复消息格式.png)
字段|说明
---|---
状态码|同http的返回状态，如200，表示OK
解释|状态码的文本解释

## sdp格式

### 简介

sdp（Session Description Protocol，RFC2327，会话描述协议）定义了会话描述的统一格式，在媒体会话中传递媒体流信息，允许会话描述的接收者去参与会话。
RTSP协议中使用sdp进行**媒体信息的描述**，语音通话SIP协议，监控安防GB28181国标, webRTC都用到了sdp。

### sdp结构

sdp信息由多行type=value组成，其中type是一个字符串，表示类型，value是一个字符串，value的格式视type而定，整个协议区分大小写，= 两侧不允许有空格。

sdp会话描述包含一个**会话级描述**（session_level_description）和多个**媒体级描述**（media_level description）。会话级描述的作用域是整个会话，媒体级描述是对单个的媒体流进行描述，如传输过程中的视频流信息。

![alt text](/assets/sdp格式.png)

会话级描述包含字段(带*号标记的行为可选)：

![alt text](/assets/会话级描述.png)

字段|描述|格式
---|---|---
version(必选)|表示sdp的版本号|v=\<version>
origin(必选)|对会话的发起者进行了描述|o=\<username> \<sessionid> \<version> \<network type> \<address type> \<address>
Session Name(必选)|会话名称(唯一)|s=\<name>
Connection Data(可选)|表示媒体连接信息|c=\<networktype> \<address type> \<connection address>
Bandwidth(可选)|描述了建议的带宽，CT表示总带宽，AS表示单个媒体带宽的最大值|b=\<modifier>:\<bandwidth-value>
Times(必选)|描述了会话的开始时间和结束时间|t=\<start time> \<stop time>
email(可选)|描述邮件地址|e=\<email address>
attribute(可选)|表示一个会话级别或媒体级别下的0个或多个属性|a=\<attribute>:\<value>

会话级别描述中的`connection`('c=')和`attribute`('a=')信息**适用于该会话所有的媒体级别描述，除非媒体级别描述重新定义了同名属性**。

会话级描述必须包含一个`connection`('c=')信息或者每个媒体级描述包含一个`connection`('c=')信息。

通常连接地址是D类IP多播地址，如果会话不是多播的，则连接地址包含由`attribute`（**附加属性**）字段确定的**预期数据源**、**数据中继**、**数据接收器**的完全限定域名或单播IP地址。

媒体级描述主要包含以下字段(带*号标记为可选)：
![alt text](/assets/sdp媒体级描述.png)

字段|描述|格式
---|---|---
media information(必选)|\<media>表示媒体类型例如"audio","video","application","data","control";\<port>表示媒体流发往传输层的端口;\<transport>表示传输协议\<fmt list>表示媒体格式|m=\<media> \<port> \<transport type> \<fmt list>
rtpmap(可选)|payload type表示动态负载类型,98表示H264，encoding name表示编码名称，clock rate表示时钟频率|a=rtpmap:\<payload type> \<encoding name>/\<clock rate>

**SDP示例**:

```()
v=0
o=- 1586545639954157 1586545639954157 IN IP4 192.17.1.63
s=Media Presentation
e=NONE
b=AS:5100
t=0 0
a=control:rtsp://192.17.1.63:554/
m=video 0 RTP/AVP 96
c=IN IP4 0.0.0.0
b=AS:5000
a=recvonly
a=x-dimensions:1920,1080
a=control:rtsp://192.17.1.63:554/trackID=1
a=rtpmap:96 H264/90000
a=fmtp:96 profile-level-id=420029; packetization-mode=1; sprop-parameter-sets=Z01AKI2NQDwBE/LgLcBAQFAAAD6AAAw1DoYACYFAABfXgu8uNDAATAoAAL68F3lwoA==,aO44gA==
m=audio 0 RTP/AVP 8
c=IN IP4 0.0.0.0
b=AS:50
a=recvonly
a=control:rtsp://192.17.1.63:554/trackID=2
a=rtpmap:8 PCMA/8000
a=Media_header:MEDIAINFO=494D4B48010300000400000111710110401F000000FA000000000000000000000000000000000000;
a=appversion:1.0
```

---

```()
v=0
o=34020000001320000010 0 0 IN IP4 192.17.1.202
s=Play
c=IN IP4 192.17.1.202
t=0 0
m=video 5500 RTP/AVP 96 97 98
a=rtpmap:96 PS/90000
a=rtpmap:97 MPEG4/90000
a=rtpmap:98 H264/90000
a=recvonly
```

## RTSP选项

### OPTION

请求消息格式：
![alt text](/assets/OPTION请求.png)

* **OPTIONS**：标识请求命令的类型;
* **RTSP URI**：请求的服务端的URI，以rtsp://开头的地址，一般为rtsp://ip:554(rtsp默认端口号);
* **RTSP VER**：标识RTSP 版本号，一般为RTSP/1.0;
* **CSeq**：数据包序列号，由于OPTIONS一般而言为RTSP请求的第一条指令，一般而言为1;
* **User-Agent**：用户代理;

回复消息格式：
![alt text](/assets/OPTION响应.png)

* **Public**：表示了服务端支持的方法，有OPTIONS，DESCRIBE，PLAY，PAUSE，SETUP，TEARDOWN，SET_PARAMERTER，GET_PARAMETER

OPTION示例

请求:

```()
OPTIONS rtsp://192.17.1.63:554 RTSP/1.0\r\n
CSeq: 1\r\n
User-Agent: Lavf58.42.100\r\n
```

响应:

```()
RTSP/1.0 200 OK\r\n
CSeq: 1\r\n
Public: OPTIONS, DESCRIBE, PLAY, PAUSE, SETUP, TEARDOWN, SET_PARAMETER, GET_PARAMETER\r\n
Date:  Fri, Apr 10 2020 19:07:19 GMT\r\n
```

### DESCRIBE

客户端会继续向服务器发送DESCRIBE消息，来获取会话描述信息（sdp），得知有哪些视频流以及视频流的分辨率、帧率、编解码信息等。

请求结构：
![alt text](/assets/DESCRIBE请求.png)
消息头部：DESCRIBE为请求类型；URI为请求的服务器地址；RTSP_VER为RTSP版本

* **Accept**：指明接收数据的格式，如application/sdp表示接收sdp信息；
* **CSeq**：RTSP序列号，一般为2；
* UserAgent：用户代理；

回复结构：
对于DESCRIBE消息，服务端的回复有两种可能：若需要认证，则返回401，并在客户端认证，客户端再次发送包含认证信息的DESCRIBE指令，服务端收到带认证信息的DESCRIBE请求，返回sdp信息给客户端；如果不需要认证，则直接返回sdp。

需要认证（状态码为401）：
![alt text](/assets/DESCRIBE回复(认证).png)

客户端认证（带Authorization字段）：
![alt text](/assets/DESCRIBE请求(认证).png)

认证成功（返回sdp信息）：
![alt text](/assets/DESCRIBE(认证成功).png)

* **Content-type**：回复内容类型，值application/sdp；
* **Cotent-Base**：一般用RTSP URI表示；
* Content-length：表示返回的sdp信息的长度；

DESCRIBE示例

第一次DESCRIBE请求：

```()
DESCRIBE rtsp://192.17.1.63:554 RTSP/1.0
Accept: application/sdp
CSeq: 2
User-Agent: Lavf58.42.100
```

服务端回复的401消息:

```()
RTSP/1.0 401 Unauthorized
CSeq: 2
WWW-Authenticate: Digest realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", stale="FALSE"
Date: Fri, Apr 10 2020 19:07:19 GMT
```

第二次DESCRIBE请求:

```()
DESCRIBE rtsp://192.17.1.63:554 RTSP/1.0
Accept: application/sdp
CSeq: 3
User-Agent: Lavf58.42.100
Authorization: Digest username="admin", realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", uri="rtsp://192.17.1.63:554", response="8f1987b6da1aeb3f3744e1307d850281"
```

验证OK消息:

```()
RTSP/1.0 200 OK
CSeq: 3
Content-Type: application/sdp
Content-Base: rtsp://192.17.1.63:554/
Content-Length: 712

v=0
o=- 1586545639954157 1586545639954157 IN IP4 192.17.1.63
s=Media Presentation
e=NONE
b=AS:5100
t=0 0
a=control:rtsp://192.17.1.63:554/
m=video 0 RTP/AVP 96
c=IN IP4 0.0.0.0
b=AS:5000
a=recvonly
a=x-dimensions:1920,1080
a=control:rtsp://192.17.1.63:554/trackID=1
a=rtpmap:96 H264/90000
a=fmtp:96 profile-level-id=420029; packetization-mode=1; sprop-parameter-sets=Z01AKI2NQDwBE/LgLcBAQFAAAD6AAAw1DoYACYFAABfXgu8uNDAATAoAAL68F3lwoA==,aO44gA==
m=audio 0 RTP/AVP 8
c=IN IP4 0.0.0.0
b=AS:50
a=recvonly
a=control:rtsp://192.17.1.63:554/trackID=2
a=rtpmap:8 PCMA/8000
a=Media_header:MEDIAINFO=494D4B48010300000400000111710110401F000000FA000000000000000000000000000000000000;
a=appversion:1.0
```

### SETUP

#### setup作用

SETUP请求的作用是指明媒体流该以什么方式传输；每个流PLAY之前必须执行SETUP操作；发送SETUP请求时，客户端会指定两个端口，**偶数端口用来接收RTP（视频）数据，相邻的奇数端口用于接收RTCP（控制）数据**。

请求结构：
![alt text](/assets/SETUP请求.png)

* SETUP：消息类型；
* URI：请求的RTSP服务器的地址；
* RTSP_VER：RTSP的版本；
* **TRANSPORT**：媒体流的传输方式，具体包括传输协议如RTP/UDP；指出是单播，组播还是广播；声明两个端口，一个奇数，用于接收RTCP数据，一个偶数，用于接收RTP数据；
* CSeq：数据包请求序列号；
* User-Agent：用户代理；
* **Session**：会话ID；
* Authorization：认证信息；

请求之后，若异常情况，RTSP服务器回复200 OK消息，同时在Transport字段中增加sever_port，指明对等的服务端RTP和RTCP传输的端口，增加ssrc字段，增加mode字段，同时返回一个session id，用于标识本次会话连接，之后客户端发起PLAY请求的时候需要使用该字段。

回复结构：
![alt text](/assets/SETUP响应.png)

SETUP实例

请求：

```()
SETUP rtsp://192.17.1.63:554/trackID=1 RTSP/1.0
Transport: RTP/AVP/UDP;unicast;client_port=26968-26969
CSeq: 4
User-Agent: Lavf58.42.100
Authorization: Digest username="admin", realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", uri="rtsp://192.17.1.63:554/trackID=1", response="e29ca030062df6022faa77fefde40b28"
```

```()
RTSP/1.0 200 OK
CSeq: 4
Session: 337474243;timeout=60
Transport: RTP/AVP/UDP;unicast;client_port=26968-26969;server_port=8284-8285;ssrc=4a7fb757;mode="play"
Date: Fri, Apr 10 2020 19:07:19 GMT
```

### PLAY

#### play作用

PLAY消息是客户端发送的播放请求，发送播放请求的时候可以指定播放区间，发起播放请求后，如果连接正常，则服务端开始播放视频，即开始向客户端按照之前在**TRASPORT**中约定好的方式发送音视频数据包。

请求结构：
![alt text](/assets/PLAY请求.png)

* **RTSP URI**：请求的RTSP 地址；
* RTSP version：版本号；
* CSeq：请求的序列号；
* User-Agent：用户代理；
* **Session**：会话id，**SETUP请求之后，服务端返回的session id**；
* Authorizatiuon：认证信息；
* **Range**：PLAY消息特有的，表示**请求播放的时间段**，使用ntp时间来表示。

响应结构：
![alt text](/assets/PLAY响应.png)

* **Session**：会话ID，SETUP返回时确定的ID；
* **RTP-info**：由url、seq、rtptime组成
  * url: 视频播放的地址;
  * seq: 第一个rtp视频数据包开始的序列号;
  * rtptime: 视频开始播放的时间戳；

PLAY示例：

请求：

```()
PLAY rtsp://192.17.1.63:554/ RTSP/1.0\r\n
Range: npt=0.000-\r\n
CSeq: 6\r\n
User-Agent: Lavf58.42.100\r\n
Session: 337474243\r\n
Authorization: Digest username="admin", realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", uri="rtsp://192.17.1.63:554/", response="9ea6c2659d3bce8d129ca3549ecc7fbf"\r\n\r\n
```

响应:

```()
RTSP/1.0 200 OK\r\n
CSeq: 6\r\n
Session: 337474243\r\n
RTP-Info: url=rtsp://192.17.1.63:554/trackID=1;seq=3658;rtptime=1710363406,url=rtsp://192.17.1.63:554/trackID=2;seq=6598;rtptime=4065225152\r\n
Date: Fri, Apr 10 2020 19:07:20 GMT\r\n\r\n
```

### PAUSE

#### pause作用

暂停请求会使得流传输暂时中断（相当于暂停），如果请求的URL指向一个流地址，则仅针对该流的回放和录制会被中断。

请求结构：
![alt text](/assets/PAUSE请求.png)

* **RTSP URI**：请求的流地址；
* RTSP Version：RTSP的版本号；
* CSeq：消息序列号；
* **Session**：会话ID，SETUP请求时服务端返回的ID。

响应结构：
![alt text](/assets/PAUSE响应.png)

PAUSE示例：

请求：

```()
PAUSE rtsp://example.com/fizzle/foo RTSP/1.0\r\n
CSeq：834\r\n
Session：12345678\r\n\r\n
```

响应：

```()
RTSP/1.0  200 OK\r\n
CSeq:834\r\n
Session：12345678\r\n\r\n
```

### TEARDOWN

#### tracedown作用

TEARDOWN用于结束流传输，同时释放与之相关的资源，TEARDWON之后，整个RTSP连接也随之结束。

请求结构：
![alt text](/assets/TEARDOWN请求.png)

* **URI**：请求的流地址；
* RTSP Version：RTSP的版本号；
* **Session**：SETUP消息后server返回的会话id；

回复结构：
![alt text](/assets/TEARDOWN响应.png)

* **Session**：SETUP消息后回复客户端的会话id；
  
TEARDOWN示例：

请求：

```()
TEARDOWN rtsp://192.17.1.73:554/Streaming/Channels/101/?transportmode=unicast&profile=Profile_1 RTSP/1.0\r\n
CSeq: 10\r\n
Authorization: Digest username="admin", realm="bcad28138995", nonce="a1a5b9d3865180dccbaffb1cb2eb2a27", uri="rtsp://192.17.1.73:554/Streaming/Channels/101/", response="c5c005ef16638dfa326dcfc0c3f54aab"\r\n
User-Agent: LibVLC/3.0.11 (LIVE555 Streaming Media v2016.11.28)\r\n
Session: 1659421772\r\n\r\n
```

响应：

```()
RTSP/1.0 200 OK\r\n
CSeq: 10\r\n
Session: 1659421772\r\n
Date: Thu, Aug 27 2020 18:31:13 GMT\r\n\r\n
```

### GET_PARAMETER

#### get_parameter作用

GetParameret用作向服务器获取参数，一般用于获取时间范围。**当发送的请求中没有相关请求参数时，则用作保持RTSP连接**。

请求格式：

![alt text](/assets/GET_PARAMETER请求.png)

响应格式：
![alt text](/assets/GET_PARAMETER响应.png)

GET Parameter示例：

请求：

```()
GET_PARAMETER rtsp://192.17.1.73:554/Streaming/Channels/101/?transportmode=unicast&profile=Profile_1 RTSP/1.0\r\n
CSeq: 7\r\n
Authorization: Digest username="admin", realm="bcad28138995", nonce="a1a5b9d3865180dccbaffb1cb2eb2a27", uri="rtsp://192.17.1.73:554/Streaming/Channels/101/", response="4764a1f2772821f5528ebbb2ad18c3f9"\r\n
User-Agent: LibVLC/3.0.11 (LIVE555 Streaming Media v2016.11.28)\r\n
Session: 1659421772\r\n\r\n
```

响应：

```()
RTSP/1.0 200 OK\r\n
CSeq: 7\r\n
Date: Thu, Aug 27 2020 18:29:00 GMT\r\n
```

由于GET_PARAMETER的没有设置获取参数，所以回复的消息中也没有参数相关的字段。如果GET_PARAMTER请求中有请求不同类型的参数，则回复消息的时候需要将对应的该参数的相关信息返回。

### SET_PARAMETER

#### set_parameter作用

SET_PARAMETER方法用于给URI指定的流地址设置参数。

当客户端想要确定为什么某一个特定的请求失败时，请求应该只包含一个参数。

如果请求中包含多个参数值，则服务器只有在所有的参数被成功设置的情况下，才会生效。

服务器允许某个参数被重复设置成相同的值，但不允许改变参数的值。

请求结构：
![alt text](/assets/SET_PARAMETER请求.png)

响应结构：
![alt text](/assets/SET_PARAMETER响应.png)

* **状态码、状态描述**：给出了对于设置某个参数的请求的回复状态，如状态码451，表示无效参数（Invalid Parameter）；
* **CSeq、Content-length、Content-type**：与SET_PARAMETER请求的参数一致；

## RTP

### RTP简介

RTP是一种应用层协议，传输层协议可以是TCP或者UDP。

RTP数据包由两部分组成，一部分是RTP Heaeder，一部分是RTP body，RTP Header占用最少12个字节，最多72个字节；另一部分是RTP Payload，用来**封装视频数据**，如封装h264编码的视频数据。

**头结构**：
![alt text](/assets/RTP头格式.png)

* **V**：版本号，2个bit；
* **P**：填充位，1个bit。为0表示包尾部没有填充附加字节；为则表示有；
* **X**：扩展位，1个bit。为1表示包头后面还有一个扩展头部；
* **CC**：CSRC个数，4bit。表示后面CSRC的个数0~15个；
* **M**：标记位，1个bit。值为0，表示该数据包非一帧数据的最后一帧，值为1时，表示该数据包是一帧数据的最后一个数据包；
* **PT**：表示 **==荷载类型==**，7个bit，GB28181中H.264的PT值为98；
* **SN**：序列号，16个bit；
* **时间戳**：32个bit，记录数据荷载部分采样时刻；
* **同步信源（SSRC）标识符**：32个bit。标记RTP的来源，一个RTP会话中不能有两个SSRC值。
* **特约信源（CSRC）标识符**：最多15个，每个都是32个bit。

## RTCP

### RTCP协议

#### RTCP简介

RTSP用于建立连接及发送请求等，RTP用于实际的媒体数据传输，RTCP(RTP Control Protocol)是针对RTP的控制协议，RTCP提供了数据分发质量反馈信息。

RTCP包格式：
![alt text](/assets/RTCP包格式.png){:height="85%" width="85%"}

* **V(2bit)**：Version，表示RTCP版本号，当前规范定义的版本号为2，==RTP数据包中的版本号与RTCP数据包的中的版本号是一致的==;
* **P(1bit)**：填充位，表示是否需要填充，0表示不填充，其不属于控制信息。在某些情况下（如加密）需要进行填充，在填充的情况下，Padding的最后一个字节用于计算应该忽略多少个字节；
* **RC(5bit)**：接收方报告计数，表示在该数据包中的接收方报告块的数量，该字段0值是有效的，但没有实际意义；
* **PT(8bit)**：RTCP的数据包的分组类型，RTCP包含的分组类型如下：

类型|缩写表示|意义
---|---|---
200|SR（Sender Report）|发送端报告
201|RR（Receiver Roport）|接收端报告
202|SDES（Source Descripition Items）|源点
203|BYE|结束
204|APP（Application）|特定应用

* **length(16bit)**：RTCP数据包的大小。计算方式：假设RTCP数据包的长度为32个字节，32/4=8，总共有8组4个字节，8-1=7，此时RTCP数据包中length的值为7；
* **SSRC(32bit)**：同步源标识；
* **RR**：Recevier Report，接受端报告；

##### SR（Send Report）

![alt text](/assets/SS格式.png)

* NTP时标：NTP时间戳
* RTP时标：RTP时间戳
* **发送者包计数**：从开始传输到当前SR包生成的时间段内，==发送端发送的RTP数据包的总个数==。若发送者更改其SSRC，则该计数重置；

* **发送者数据8位组计数**：从开始传输到当前SR包生成的时间段内，==发送端发送的总的数据的大小的八位组计数==，不包含头信息以及填充信息。若发送者更改可SSRC，需要重置该值，该字段可用来估计平均码率。

##### RR(Receiver report)

![alt text](/assets/RR格式.png)

* **SSRC（32bit）**: 发送端的信源标识符，与发送端的SSRC一致；
* **丢包数（8bit）**：前一个SR或RR包发送后，到当前的SR包或RR包的间隔内，来自源（用源SSRC标识）发送的数据包的丢失个数；
* **累积丢包数（24bit）**: 自开始接受源（用源SSRC标识）发送的数据开始，累积丢失的数据包的个数；
* **扩展包序号（32bit）**：低16位为当前接收到的来自源的（用源SSRC标识）数据包的最大序列号；**高16位表示RT包序列号的循环计数**，RTP数据包中表示序列号的长度为2个字节，即最大的RTP序列号为65536，如果序列号超了65536，假设为65537，这个时候RTCP在扩展包序号中对其说明。序列号循环一轮为65536，高16位表示第几轮循环，从0开始计数；
* **间隔抖动（32bit）**：RTP数据包间隔时间的统计估计，以时间戳为单位，用无符号整数表示；
* **LSR（32bit）**：last SR timestamp，表示上一个SR数据包的NTP时间戳！由于NTP时间戳为64bit，LSR为32bit，LSR取上一个SR的NTP时间戳的中间32位：如上一个SR数据包的NTP时间戳为“0x 00 01 7d 6e 3b 64 5a 1c ”，则LSR为0x  7d 6e 3b 64；
* **DLSR（32bit）**：发送当前RR包的时间与上一个SR之间的时间间隔，以1/65536为单位，如，本次RR包与上一次SR的时间间隔为264ms，则本字段的值为0.264*65536=17301.54。

##### SD(source description)

![alt text](/assets/SD格式.png)
Source Description是按照KLV的格式组织的，key表示具体的类型，length为长度，value为具体的值，key占用1个字节，length占用1个字节。

* **CNAME(值为1)**:  规范终端标识，像SSRC标识，==CNAME标识在RTP连接的所有参加者中应是唯一的==；

* **NAME(值为2)**: 用户名称，用于描述源的用户名；

* **E-mail(值为3)**: 电子邮件地址，用于描述源的邮件地址；

* **PHONE(值为4)**: 用于描述源的电话号码；

* **LOC(值为5)**: 用于描述源的地理位置；

* **TOOL(值为6)**: 用于描述应用或工具的名称，表示产生流的应用的名称与版本，如"videotool 1.2"；

* **NOTE(值为7)**: 用于描述源当前状态的过渡信息；

* **PRIV(值为8)**: 用于描述针对源的扩展项；

**RTCP中通过Sender Report和Receive Report在RTP数据传输中提供当前连接中RTP包发送的情况，RTP包接收的情况，RTP包丢失的情况**。通过这些信息反馈，可以实现对网络传输做一些调整和控制，这就是RTCP的主要功能。
