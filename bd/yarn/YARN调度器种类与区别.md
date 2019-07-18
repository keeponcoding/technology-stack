**三种调度器**
> 1.FIFO调度器(FIFO Scheduler)  
> 2.容量调度器(Capacity Scheduler)  
> 3.公平调度器(Fair Scheduler)  

**三种调度器比较**  
![fifoscheduler](https://github.com/keeponcoding/technology-stack/tree/master/images/fifo-scheduler.png)  
简单易懂，不需要配置，但是不适用集群共享
如图所示 第二个job提交后必须等到第一个job运行结束后才能够启动运行  
![capacityscheduler](https://github.com/keeponcoding/technology-stack/tree/master/images/capacity-scheduler.png)      
一个独立的专门队列保证小作业已提交就可以运行，由于队列容量是为队列中的那个作业所保留的，所以这种策略是以牺牲真个集群的利用率为代价的
同等集群配置下，FIFO的大job比Capacity的job运行时间更长，因为其分配的资源没有FIFO多  
![fairscheduler](https://github.com/keeponcoding/technology-stack/tree/master/images/fair-scheduler.png)     
不需要预留一定量的资源，因为调度器会在所有运行的作业之间动态平衡资源，如图第一个job运行时，集群中仅有此job运行，所以占用了整个集群的资源，当第二个job启动时，它被分配集群一半的资源，这样每个job都会公平的分享资源
> ⚠️ 第二个作业的启动到获得公平共享资源之间会有时间滞后，因为它必须等待第一个作业使用的容器用并释放出资源。当第二个作业结束并且不再申请资源后，大作业将再次使用全部的集群资源，这保证了集群资源可以得到较高的利用并且可以保证第二个作业及时完成