# rtspЭ�����

## rtsp���

rtsp��Real Time Streaming Protocol��RFC2326����ʵʱ������Э�飬��TCP/IPЭ����ϵ�е�һ��**Ӧ�ò�Э��**���涨��һ�Զ�Ӧ�ó��������Ч��ͨ��IP���紫�Ͷ�ý�����ݡ�

RTSP�ǻ���**RTP**��**RTCP**���ж�ý�����ݴ��䡣

* RTP�����ڴ���ý�����ݡ�
* RTCP�����ڽ����������

## rtsp������������

![alt text](/assets/RTSP����.png){:height="80%" width="80%"}

* **OPTIONS**��
  * �ͻ����������˵Ŀ���ָ�
  * �������˻ظ���ǰ���õ�ָ�
* **DESCRIBE**��
  * �ͻ��������������ý�������ļ���ͨ��rtsp��ͷ��url���������󣬸�ʽΪsdp��
  * �������ظ��ͻ���sdp�ļ������ļ����߿ͻ��˷���������Щ����Ƶ������ʲô���ԣ�����������Ϣ��֡�ʵȡ�
* **SETUP**��
  * �ͻ�����������˷��������������������Ự���ӣ�׼����ʼ��������Ƶ���ݣ�������Ϣ��������������Ƶ���ݰ�����UDP����TCP���䣬ָ����RTP��RTCP�˿ڣ��Լ��ǵ��������鲥����Ϣ��
  * ���������յ��ͻ�������󣬸��ݿͻ�������Ķ˿ں�ȷ�����Ϳ������ݵĶ˿��Լ�����Ƶ���ݵĶ˿ڡ�
* **PLAY**��
  * �ͻ������������󲥷�ý�塣
  * �������ظ��ͻ���200 OK! ֮��ʼͨ��SETUP��ָ���Ķ˿ڿ�ʼ�������ݣ�
* **TEARDOWN**��
  * �����������ر����ӻỰ����
  * ������յ���Ϣ����ͻ��˷���200 OK��֮��Ͽ����ӡ�

## rtsp����Ϣ�ṹ

������Ϣ��ʽ��
![alt text](/assets/RTCP������Ϣ��ʽ.png)
�ֶ�|˵��
---|---
����|OPTIONS, DESCRIBE, SETUP, TEARDOWN, PLAY
URI|���ܷ���ַ����rtsp://192.168.1.201:554

�ظ���Ϣ��ʽ��
![alt text](/assets/RTCP�ظ���Ϣ��ʽ.png)
�ֶ�|˵��
---|---
״̬��|ͬhttp�ķ���״̬����200����ʾOK
����|״̬����ı�����

## sdp��ʽ

### ���

sdp��Session Description Protocol��RFC2327���Ự����Э�飩�����˻Ự������ͳһ��ʽ����ý��Ự�д���ý������Ϣ������Ự�����Ľ�����ȥ����Ự��
RTSPЭ����ʹ��sdp����**ý����Ϣ������**������ͨ��SIPЭ�飬��ذ���GB28181����, webRTC���õ���sdp��

### sdp�ṹ

sdp��Ϣ�ɶ���type=value��ɣ�����type��һ���ַ�������ʾ���ͣ�value��һ���ַ�����value�ĸ�ʽ��type����������Э�����ִ�Сд��= ���಻�����пո�

sdp�Ự��������һ��**�Ự������**��session_level_description���Ͷ��**ý�弶����**��media_level description�����Ự���������������������Ự��ý�弶�����ǶԵ�����ý���������������紫������е���Ƶ����Ϣ��
![alt text](/assets/sdp��ʽ.png)

�Ự�����������ֶΣ�
![alt text](/assets/�Ự������.png)

�ֶ�|����|��ʽ
---|---|---
version(��ѡ)|��ʾsdp�İ汾��|v=\<version>
origin(��ѡ)|�ԻỰ�ķ����߽���������|o=\<username> \<sessionid> \<version> \<network type> \<address type> \<address>
Session Name(��ѡ)|�Ự����(Ψһ)|s=\<name>
Connection Data(��ѡ)|��ʾý��������Ϣ|c=\<networktype> \<address type> \<connection address>
Bandwidth(��ѡ)|�����˽���Ĵ���CT��ʾ�ܴ���AS��ʾ����ý���������ֵ|b=\<modifier>:\<bandwidth-value>
Times(��ѡ)|�����˻Ự�Ŀ�ʼʱ��ͽ���ʱ��|t=\<start time> \<stop time>
email(��ѡ)|�����ʼ���ַ|e=\<email address>
attribute(��ѡ)|��ʾһ���Ự�����ý�弶���µ�0����������|a=<*>

ý�弶������Ҫ���������ֶΣ�
![alt text](/assets/sdpý�弶����.png)

�ֶ�|����|��ʽ
---|---|---
media information(��ѡ)|\<media>��ʾý����������"audio","video","application","data","control";\<port>��ʾý�������������Ķ˿�;\<transport>��ʾ����Э��\<fmt list>��ʾý���ʽ|m=\<media> \<port> \<transport type> \<fmt list>
rtpmap(��ѡ)|payload type��ʾ��̬�������ͣ�encoding name��ʾ�������ƣ�clock rate��ʾʱ��Ƶ��|a=rtpmap:\<payload type> \<encoding name>/\<clock rate>

**�Ự������**:

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
a=recvonlya=control:rtsp://192.17.1.63:554/trackID=2
a=rtpmap:8 PCMA/8000
a=Media_header:MEDIAINFO=494D4B48010300000400000111710110401F000000FA000000000000000000000000000000000000;
a=appversion:1.0
```

**ý�弶����**��

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

## RTSPѡ��

### OPTION

������Ϣ��ʽ��
![alt text](/assets/OPTION����.png)

* **OPTIONS**����ʶ�������������;
* **RTSP URI**������ķ���˵�URI����rtsp://��ͷ�ĵ�ַ��һ��Ϊrtsp://ip:554(rtspĬ�϶˿ں�);
* **RTSP VER**����ʶRTSP �汾�ţ�һ��ΪRTSP/1.0;
* **CSeq**�����ݰ����кţ�����OPTIONSһ�����ΪRTSP����ĵ�һ��ָ�һ�����Ϊ1;
* **User-Agent**���û�����;

�ظ���Ϣ��ʽ��
![alt text](/assets/OPTION��Ӧ.png)

* **Public**����ʾ�˷����֧�ֵķ�������OPTIONS��DESCRIBE��PLAY��PAUSE��SETUP��TEARDOWN��SET_PARAMERTER��GET_PARAMETER

OPTION����ʾ����

```()
OPTIONS rtsp://192.17.1.63:554 RTSP/1.0\r\n
CSeq: 1\r\n
User-Agent: Lavf58.42.100\r\
```

```()
RTSP/1.0 200 OK\r\n
CSeq: 1\r\n
Public: OPTIONS, DESCRIBE, PLAY, PAUSE, SETUP, TEARDOWN, SET_PARAMETER, GET_PARAMETER\r\n
Date:  Fri, Apr 10 2020 19:07:19 GMT\r\n
```

### DESCRIBE

�ͻ��˻���������������DESCRIBE��Ϣ������ȡ�Ự������Ϣ��sdp������֪����Щ��Ƶ���Լ���Ƶ���ķֱ��ʡ�֡�ʡ��������Ϣ�ȡ�

����ṹ��
![alt text](/assets/DESCRIBE����.png)
��Ϣͷ����DESCRIBEΪ�������ͣ�URIΪ����ķ�������ַ��RTSP_VERΪRTSP�汾

* **Accept**��ָ���������ݵĸ�ʽ����application/sdp��ʾ����sdp��Ϣ��
* **CSeq**��RTSP���кţ�һ��Ϊ2��
* UserAgent���û�����

�ظ��ṹ��
����DESCRIBE��Ϣ������˵Ļظ������ֿ��ܣ�����Ҫ��֤���򷵻�401�����ڿͻ�����֤���ͻ����ٴη��Ͱ�����֤��Ϣ��DESCRIBEָ�������յ�����֤��Ϣ��DESCRIBE���󣬷���sdp��Ϣ���ͻ��ˣ��������Ҫ��֤����ֱ�ӷ���sdp��

��Ҫ��֤��״̬��Ϊ401����
![alt text](/assets/DESCRIBE�ظ�(��֤).png)

�ͻ�����֤����Authorization�ֶΣ���
![alt text](/assets/DESCRIBE����(��֤).png)

��֤�ɹ�������sdp��Ϣ����
![alt text](/assets/DESCRIBE(��֤�ɹ�).png)

* **Content-type**���ظ��������ͣ�ֵapplication/sdp��
* **Cotent-Base**��һ����RTSP URI��ʾ��
* Content-length����ʾ���ص�sdp��Ϣ�ĳ��ȣ�

DESCRIBE����ʾ����

��һ��DESCRIBE����

```()
DESCRIBE rtsp://192.17.1.63:554 RTSP/1.0
Accept: application/sdp
CSeq: 2
User-Agent: Lavf58.42.100
```

����˻ظ���401��Ϣ:

```()
RTSP/1.0 401 Unauthorized
CSeq: 2
WWW-Authenticate: Digest realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", stale="FALSE"
Date: Fri, Apr 10 2020 19:07:19 GMT
```

�ڶ���DESCRIBE����:

```()
DESCRIBE rtsp://192.17.1.63:554 RTSP/1.0
Accept: application/sdp
CSeq: 3
User-Agent: Lavf58.42.100
Authorization: Digest username="admin", realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", uri="rtsp://192.17.1.63:554", response="8f1987b6da1aeb3f3744e1307d850281"
```

��֤OK��Ϣ:

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

#### setup����

SETUP�����������ָ��ý��������ʲô��ʽ���䣻ÿ����PLAY֮ǰ����ִ��SETUP����������SETUP����ʱ���ͻ��˻�ָ�������˿ڣ�**ż���˿���������RTP����Ƶ�����ݣ����ڵ������˿����ڽ���RTCP�����ƣ�����**��

����ṹ��
![alt text](/assets/SETUP����.png)

* SETUP����Ϣ���ͣ�
* URI�������RTSP�������ĵ�ַ��
* RTSP_VER��RTSP�İ汾��
* **TRANSPORT**��ý�����Ĵ��䷽ʽ�������������Э����RTP/UDP��ָ���ǵ������鲥���ǹ㲥�����������˿ڣ�һ�����������ڽ���RTCP���ݣ�һ��ż�������ڽ���RTP���ݣ�
* CSeq�����ݰ��������кţ�
* User-Agent���û�����
* **Session**���ỰID��
* Authorization����֤��Ϣ��

����֮�����쳣�����RTSP�������ظ�200 OK��Ϣ��ͬʱ��Transport�ֶ�������sever_port��ָ���Եȵķ����RTP��RTCP����Ķ˿ڣ�����ssrc�ֶΣ�����mode�ֶΣ�ͬʱ����һ��session id�����ڱ�ʶ���λỰ���ӣ�֮��ͻ��˷���PLAY�����ʱ����Ҫʹ�ø��ֶΡ�

�ظ��ṹ��
![alt text](/assets/SETUP��Ӧ.png)

SETUPʵ��

����

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

#### play����

PLAY��Ϣ�ǿͻ��˷��͵Ĳ������󣬷��Ͳ��������ʱ�����ָ���������䣬���𲥷������������������������˿�ʼ������Ƶ������ʼ��ͻ��˰���֮ǰ��**TRASPORT**��Լ���õķ�ʽ��������Ƶ���ݰ���

����ṹ��
![alt text](/assets/PLAY����.png)

* **RTSP URI**�������RTSP ��ַ��
* RTSP version���汾�ţ�
* CSeq����������кţ�
* User-Agent���û�����
* **Session**���Ựid��**SETUP����֮�󣬷���˷��ص�session id**��
* Authorizatiuon����֤��Ϣ��
* **Range**��PLAY��Ϣ���еģ���ʾ**���󲥷ŵ�ʱ���**��ʹ��ntpʱ������ʾ��

��Ӧ�ṹ��
![alt text](/assets/PLAY��Ӧ.png)

* **Session**���ỰID��SETUP����ʱȷ����ID��
* **RTP-info**������url��seq��rtptime��url��ʾ��Ƶ���ŵĵ�ַ��seq��ʾ��һ��rtp��Ƶ���ݰ���ʼ�����кţ�rtptime��ʾ��Ƶ��ʼ���ŵ�ʱ�����

PLAYʾ����

����

```()
PLAY rtsp://192.17.1.63:554/ RTSP/1.0\r\n
Range: npt=0.000-\r\n
CSeq: 6\r\n
User-Agent: Lavf58.42.100\r\n
Session: 337474243\r\n
Authorization: Digest username="admin", realm="IP Camera(23306)", nonce="a946c352dd3ad04cf9830d5e72ffb11e", uri="rtsp://192.17.1.63:554/", response="9ea6c2659d3bce8d129ca3549ecc7fbf"\r\n\r\n
```

��Ӧ:

```()
RTSP/1.0 200 OK\r\n
CSeq: 6\r\n
Session: 337474243\r\n
RTP-Info: url=rtsp://192.17.1.63:554/trackID=1;seq=3658;rtptime=1710363406,url=rtsp://192.17.1.63:554/trackID=2;seq=6598;rtptime=4065225152\r\n
Date: Fri, Apr 10 2020 19:07:20 GMT\r\n\r\n
```

### PAUSE

#### pause����

��ͣ�����ʹ����������ʱ�жϣ��൱����ͣ������������URLָ��һ������ַ�������Ը����Ļطź�¼�ƻᱻ�жϡ�

����ṹ��
![alt text](/assets/PAUSE����.png)

* **RTSP URI**�����������ַ��
* RTSP Version��RTSP�İ汾�ţ�
* CSeq����Ϣ���кţ�
* **Session**���ỰID��SETUP����ʱ����˷��ص�ID��

��Ӧ�ṹ��
![alt text](/assets/PAUSE��Ӧ.png)

PAUSEʾ����

����

```()
PAUSE rtsp://example.com/fizzle/foo RTSP/1.0\r\n
CSeq��834\r\n
Session��12345678\r\n\r\n
```

��Ӧ��

```()
RTSP/1.0  200 OK\r\n
CSeq:834\r\n
Session��12345678\r\n\r\n
```

### TEARDOWN

#### tracedown����

TEARDOWN���ڽ��������䣬ͬʱ�ͷ���֮��ص���Դ��TEARDWON֮������RTSP����Ҳ��֮������

����ṹ��
![alt text](/assets/TEARDOWN����.png)

* **URI**�����������ַ��
* RTSP Version��RTSP�İ汾�ţ�
* **Session**��SETUP��Ϣ��server���صĻỰid��

�ظ��ṹ��
![alt text](/assets/TEARDOWN��Ӧ.png)

* **Session**��SETUP��Ϣ��ظ��ͻ��˵ĻỰid��
  
TEARDOWNʾ����

����

```()
TEARDOWN rtsp://192.17.1.73:554/Streaming/Channels/101/?transportmode=unicast&profile=Profile_1 RTSP/1.0\r\n
CSeq: 10\r\n
Authorization: Digest username="admin", realm="bcad28138995", nonce="a1a5b9d3865180dccbaffb1cb2eb2a27", uri="rtsp://192.17.1.73:554/Streaming/Channels/101/", response="c5c005ef16638dfa326dcfc0c3f54aab"\r\n
User-Agent: LibVLC/3.0.11 (LIVE555 Streaming Media v2016.11.28)\r\n
Session: 1659421772\r\n\r\n
```

��Ӧ��

```()
RTSP/1.0 200 OK\r\n
CSeq: 10\r\n
Session: 1659421772\r\n
Date: Thu, Aug 27 2020 18:31:13 GMT\r\n\r\n
```

### GET_PARAMETER

#### get_parameter����

GetParameret�������������ȡ������һ�����ڻ�ȡʱ�䷶Χ��**�����͵�������û������������ʱ������������RTSP����**��

�����ʽ��

![alt text](/assets/GET_PARAMETER����.png)

��Ӧ��ʽ��
![alt text](/assets/GET_PARAMETER��Ӧ.png)

GET Parameterʾ����

����

```()
GET_PARAMETER rtsp://192.17.1.73:554/Streaming/Channels/101/?transportmode=unicast&profile=Profile_1 RTSP/1.0\r\n
CSeq: 7\r\n
Authorization: Digest username="admin", realm="bcad28138995", nonce="a1a5b9d3865180dccbaffb1cb2eb2a27", uri="rtsp://192.17.1.73:554/Streaming/Channels/101/", response="4764a1f2772821f5528ebbb2ad18c3f9"\r\n
User-Agent: LibVLC/3.0.11 (LIVE555 Streaming Media v2016.11.28)\r\n
Session: 1659421772\r\n\r\n
```

��Ӧ��

```()
RTSP/1.0 200 OK\r\n
CSeq: 7\r\n
Date: Thu, Aug 27 2020 18:29:00 GMT\r\n
```

����GET_PARAMETER��û�����û�ȡ���������Իظ�����Ϣ��Ҳû�в�����ص��ֶΡ����GET_PARAMTER������������ͬ���͵Ĳ�������ظ���Ϣ��ʱ����Ҫ����Ӧ�ĸò����������Ϣ���ء�

### SET_PARAMETER

#### set_parameter����

SET_PARAMETER�������ڸ�URIָ��������ַ���ò�����

���ͻ�����Ҫȷ��Ϊʲôĳһ���ض�������ʧ��ʱ������Ӧ��ֻ����һ��������

��������а����������ֵ���������ֻ�������еĲ������ɹ����õ�����£��Ż���Ч��

����������ĳ���������ظ����ó���ͬ��ֵ����������ı������ֵ��

����ṹ��
![alt text](/assets/SET_PARAMETER����.png)

��Ӧ�ṹ��
![alt text](/assets/SET_PARAMETER��Ӧ.png)

* **״̬�롢״̬����**�������˶�������ĳ������������Ļظ�״̬����״̬��451����ʾ��Ч������Invalid Parameter����
* **CSeq��Content-length��Content-type**����SET_PARAMETER����Ĳ���һ�£�

## RTP

### RTP���

RTP��һ��Ӧ�ò�Э�飬�����Э�������TCP����UDP��

RTP���ݰ�����������ɣ�һ������RTP Heaeder��һ������RTP body��RTP Headerռ������12���ֽڣ����72���ֽڣ���һ������RTP Payload������**��װʵ�ʵ���Ƶ����**�����װh264�������Ƶ���ݡ�

**ͷ�ṹ**��
![alt text](/assets/RTPͷ��ʽ.png)

* **V**���汾�ţ�2��bit��
* **P**�����λ��1��bit��Ϊ0��ʾ��β��û����丽���ֽڣ�Ϊ���ʾ�У�
* **X**����չλ��1��bit��Ϊ1��ʾ��ͷ���滹��һ����չͷ����
* **CC**��CSRC������4bit����ʾ����CSRC�ĸ���0~15����
* **M**�����λ��1��bit��ֵΪ0����ʾ�����ݰ���һ֡���ݵ����һ֡��ֵΪ1ʱ����ʾ�����ݰ���һ֡���ݵ����һ�����ݰ���
* **PT**����ʾ **==��������==**��7��bit��GB28181��H.264��PTֵΪ98��
* **SN**�����кţ�16��bit��
* **ʱ���**��32��bit����¼���ݺ��ز��ֲ���ʱ�̣�
* **ͬ����Դ��SSRC����ʶ��**��32��bit�����RTP����Դ��һ��RTP�Ự�в���������SSRCֵ��
* **��Լ��Դ��CSRC����ʶ��**�����15����ÿ������32��bit��

## RTCP

### RTCPЭ��


