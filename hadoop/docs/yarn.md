# Yarn
[(yarn架构图)](../pictures/yarn.jpg)<br>
`yarn是hadoop上的资源管理和任务调度系统。`

- ResourceManager(RM)  
负责整个集群的资源管理和调度，在集群中有两个实例，一主一备，用于做灾备切换。主要由两个组件构成：调度器（Scheduler）和应用程序管理器（Applications Manager）。<br>
- 调度器（Scheduler）   
调度器根据容量、队列等限制条件（如每个队列分配一定的资源，最多执行一定数量的作业等），将系统中的资源分配给各个正在运行的应用程序。调度器仅根据各个应用程序的资源需求进行资源分配，而资源分配单位是Container，从而限定每个任务使用的资源量。Scheduler不负责监控或者跟踪应用程序的状态，也不负责任务因为各种原因而需要的重启（由ApplicationMaster负责）。总之，调度器根据应用程序的资源要求，以及集群机器的资源情况，为用程序分配封装在Container中的资源。调度器是可插拔的，例如CapacityScheduler、FairScheduler。（PS：在实际应用中，只需要简单配置即可）<br>
- 应用程序管理器（Application Manager）  
应用程序管理器负责管理整个系统中所有应用程序，包括应用程序提交、与调度器协商资源以启动AM、监控AM运行状态并在失败时重新启动等，跟踪分给的Container的进度、状态也是其职责。<br>
- ApplicationMaster(AM)  
负责应用程序相关的事务，比如任务调度、任务监控和容错等。每一个提交到yarn上的应用程序对应一个AM
- Client   
用户提交job的地方
- NodeManager(NM)   
对应集群中的每台服务器节点，负责container的管理，监视其资源使用情况并汇报给RM。
- Container    
NM上具体执行任务的地方


## 资源管理任务调度流程
①首先用户在client上提交任务，这时候client会发送请求给RM，请求启动AM<br>
②RM会随机找一台NM启动AM，AM会划分应用为一个个的task任务，放入任务队列
③AM会向RM请求用于执行任务的container资源   
④因为RM知道NM的资源情况，RM会返回给AM一批NM   
⑤AM会将任务分配发送到拿到的NM节点上，请求NM执行任务      
⑥NM会将任务放到container中执行，container大小可以调整

## Yarn调度器介绍
- FIFO Scheduler(先进先出调度器)   
FIFO Scheduler把应用按提交的顺序排成一个队列，这是一个先进先出队列，在进行资源分配的时候，先给队列中最头上的应用进行分配资源，待最头上的应用需求满足后再给下一个分配，以此类推。FIFO Scheduler是最简单也是最容易理解的调度器，也不需要任何配置，但它并不适用于共享集群。大的应用可能会占用所有集群资源，这就导致其它应用被阻塞。
- Capacity Scheduler(容量调度器)   
在配置文件yarn-site.xml中默认配置的调度器，会给小任务队列预先分配一定的资源，小任务的大小由配置决定。同样，占用一定资源就导致运行大任务会比FIFO模式慢
- Fair Scheduler(公平调度器)    
根据作业数量平均分配动态调整资源，不管任务大小，保证每个任务都能拿到资源<br>
实际生产中一般用fair调度器，分为不同的queue，每个团队或者每种类型的任务用自己单独的queue执行任务
