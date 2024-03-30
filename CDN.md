# Content Delivery Network

## DNS的记录类型

域名-->IP地址，在DNS系统中，最常见的资源记录方式是Internet类记录，该记录由包含4个字段的数据构成：**Name、Value、Type、TTL**。其中Name和Value可以理解为一对键值对，但是其具体含义取决于Type的类型，TLL记录了该条记录应当从缓存中删除的时间。Type主要有以下三种类型：

* **A记录(Address)**：A记录用于描述**目标域名到IP地址**的映射关系，将目标域名与A记录的Name字段进行匹配，将成功匹配的Value(IP地址)输出到DNS回应报文中。
* **NS记录(Name Server)**：NS记录用于描述**目标域名到负责解析该域名的DNS**的映射关系，根据目标域名对NS记录的Name字段进行匹配，将成功匹配的记录的Value字段(负责解析目标域名的DNS的IP地址)输出到DNS回应报文中。
* **CNAME记录**：CNAME记录用于描述**目的域名和别名**的对应关系，CNAME记录则可以将一个域名(别名)转换为另一个域名，如果多条CNAME记录指向同一个域名，则可以将多个不同的域名的请求指向同一台服务器主机。**CNAME记录通常还对应了一条A记录，用于提供被转换的域名的IP地址**。

## 通过CDN获取缓存内容

![alt text](/assets/image.png){:height="50%" width="50%"}

以客户端发起对 "www.edu.lagou.com/index.html" 的HTTP请求为例进行说明：

1. 用户发起对 "www.edu.lagou.com/index..html" 的HTTP请求，首先查询**浏览器缓存**，再到**本地DNS**查询域名的IP地址。
2. 本地DNS向**根DNS、顶级DNS、权威DNS**迭代发送**DNS查询报文**。

3. 权威DNS查找到一条CNAME记录将 "edu.lagou.com" 映射到 "edu.lagou.cdn.com"*(域名->域名)*，并且查找到一条A记录将 "edu.lagou.cdn.com" 映射到一个IP地址 *(域名->lP)*，这个IP就是**全局负载均衡系统**(GSLB)的地址。
4. 本地DNS向GSLB发送DNS查询报文，GSLB根据**本地DNS的IP地址**判断用户的大致位置，筛选出合适的**负载均衡服务器**(SLB)的IP地址，填入DNS回应报文并回复本地DNS。
5. 本地DNS回复客户端的DNS请求，将上一步的IP地址作为最终结果回复给客户端，客户端根据IP地址向SLB发送HTTP请求："www.edu.lagou.com/index.htm"。
6. SLB综合考虑缓存服务器集群中各个节点的**资源限制条件、负载情况**等因素，筛选出最优的缓存节点后回应客户端的HTTP请求 **(状态码为302,重定向地址为最优缓存节点的IP地址)**。
7. 客户端接收到SLB的HTTP回复后，**重定向**到该缓存节点上，缓存节点判断请求的资源**是否存在且有效**，将缓存的资源直接回复给客户端，否则到源站进行数据更新再回复。

* GSLB(Global Server Load Balancing):全局负载均衡服务器是 ***对物理集群的负载均衡***。
* SLB(Server load balancing):负载均衡服务器是 ***对集群内物理主机的负载均衡***。
