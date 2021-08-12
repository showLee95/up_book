# k8s主要组件
### master机器：
	> 1：apiserver  提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制； 
	> 2：scheduler  负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；  
	> 3：controller manager负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；  
	                   （集群内部的管理控制中心，保证集群处于预定化的工作状态，及时自动发现执行自动化修复）  
	> 4:（Replication Controllers）副本控制器，指Controller Manager中的Replication Controller。  
	                            （副本控制器的作用即保证集群中一个控制器所关联的Pod副本数始终保持预设值。多杀少增）  

### etcd机器：
	> 1：etcd：etcd保存了整个集群的状态；（类似数据库，用来存放集群kebelet的key.value的信息。）
	
### node机器：
	> 1：kubelet    负责维护容器的生命周期，同时也负责Volume（CVI）和网络（CNI）的管理；
				（负责将节点上key.vlue信息传输给apiserver组件，apiserver存放在etcd。）
	> 2：Container runtime 负责镜像管理以及Pod和容器的真正运行（CRI）；
	> 3：kube-proxy 负责为Service提供cluster内部的服务发现和负载均衡；

### 其他；	
	> kube-dns负责为整个集群提供DNS服务
	> Ingress Controller为服务提供外网入口
	> Heapster提供资源监控
	> Dashboard提供GUI
	> Federation提供跨可用区的集群
	> Fluentd-elasticsearch提供集群日志采集、存储与查询	
