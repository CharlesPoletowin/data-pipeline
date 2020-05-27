# Ceph
[link](https://blog.csdn.net/mingongge/article/details/100788388)
## intro
Ceph是一个统一的分布式存储系统，设计初衷是提供较好的性能、可靠性和可扩展性。

Ceph项目最早起源于Sage就读博士期间的工作（最早的成果于2004年发表），并随后贡献给开源社区。在经过了数年的发展之后，目前已得到众多云计算厂商的支持并被广泛应用。RedHat及OpenStack都可与Ceph整合以支持虚拟机镜像的后端存储。

## feature
+ 高性能
+ 高可用性
+ 高可扩展性
+ 特性丰富


支持三种接口： 
<br>
>    Object：有原生的API，而且也兼容Swift和S3的API。
>   Block：支持精简配置、快照、克隆。
>   File：Posix接口，支持快照。

## 三种存储类型
+ 块存储
+ 文件存储
+ 对象存储

---
[官方文档](https://docs.ceph.com/docs/master/)