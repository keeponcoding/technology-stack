##### YARN应用运行机制
![d4586b9616e9e67423a95f44a1f85b0a.png](evernotecid://2EB99A42-EE96-499C-93A4-4C877A924D9E/appyinxiangcom/19791940/ENResource/p33)
> 1.首先客户端联系资源管理器(RM)，要求他运行一个application master 进程  
> 2.资源管理器(RM)找到一个能够在容器中启动application master的节点管理器  
> 3.向资源管理器申请更多的容器  
> 4.运行分布式计算  

**资源管理器(resource manager)**  
管理集群资源  
**节点管理器(node manager)**  
运行在集群中所有节点之上且能够启动和监控各容器(container)  