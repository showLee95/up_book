# 搭建k8s:（安装，配置，启动）
> ### master机器：
> #### 1：安装：  
   > etcd  
   > flannel  
   > docker  
   > kubernets  
>##### !!!关闭防火墙	systemctl  stop  firewalld.service
				 
> #### 2：配置
   >  1:etcd：
		
			vim /etc/etcd/etcd.conf  
			ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
				etcd存储数据的目录
			ETCD_LISTEN_CLIENT_URLS="http://localhost:2379,http://10.40.59.223:2379"
				etcd对外服务监听端口
			ETCD_NAME="etcd"
				etcd节点名称
			ETCD_ADVERTISE_CLIENT_URLS="http://10.40.59.223:2379"
				
   >  2:flanneld
   
			（1）、从etcd中获取出/k8s/network/config的值
			（2）、划分subnet子网，并在etcd中迚行注册
			（3）、将子网信息记录到/run/flannel/subnet.env中
			etcdctl mkdir /k8s/network 
		    etcdctl set /k8s/network/config '{"Network": "10.255.0.0/16"}'
			
		    vim /etc/sysconfig/flanneld
			FLANNEL_ETCD_ENDPOINTS="http://10.40.59.223:2379"
				etcd的网络地址
			FLANNEL_ETCD_PREFIX="/k8s/network"
				etcd的key前缀
			FLANNEL_OPTIONS="--iface=eth0"
				指定通信网卡
			
   >  3:kubernets
   
		    vim /etc/kubernetes/apiserver
			KUBE_API_ADDRESS="--insecure-bind-address=0.0.0.0"
				监听所有接口 0.0.0.0
			KUBE_API_PORT="--port=8080"
			KUBELET_PORT="--kubelet-port=10250"
			KUBE_ETCD_SERVERS="--etcd-servers=http://10.40.59.223:2379"
				etcd的服务地址和监听端口
			KUBE_SERVICE_ADDRESSES="--service-cluster-ip-range=10.254.0.0/16"
				k8s服务的ip分配范围。
			KUBE_ADMISSION_CONTROL="--admission-control=AlwaysAdmit"
				对所有请求允许，不限制，总是承认（AlwaysAdmit）
			KUBE_API_ARGS=""
			
			vim /etc/kubernetes/config
			KUBE_LOGTOSTDERR="--logtostderr=true
			KUBE_LOG_LEVEL="--v=0"
			KUBE_ALLOW_PRIV="--allow-privileged=false"
			KUBE_MASTER="--master=http://10.40.59.223:8080
				指定在10.40.59.223地址监听8080端口
			
		
			vim etc/kubernetes/scheduler
			KUBE_SCHEDULER_ARGS="0.0.0.0"
				监听所有接口
			
> #### 3：启动 

			systemctl restart kube-apiserver kube-controller-manager kube-scheduler flanneld etcd
			systemctl enable kube-apiserver kube-controller-manager kube-scheduler flanneld etcd
			systemctl status kube-apiserver kube-controller-manager kube-scheduler flanneld etcd

#### 排错:	
   > 1：8080端口启动失败，查看apiserver文件，重启apiserver
   > 2：flanneld 启动报错 key not found /k8s ，  etcdctl mkdir /k8s/network 没有和flannel配置文件中FLANNEL_ETCD_PREFIX="/k8s/network

	slave机器：
		1：安装 flannel
				docker
				kubernetes
		2：配置
			1.flannel：
				vim  /etc/sysconfig/flanneld
				FLANNEL_ETCD_ENDPOINTS="http://10.40.59.223:2379"
				FLANNEL_ETCD_PREFIX="/k8s/network"
				FLANNEL_OPTIONS="--iface=eth0"
				
			2.kubernetes：
				vim /etc/kubernetes/config
				KUBE_LOGTOSTDERR="--logtostderr=true"
				KUBE_LOG_LEVEL="--v=0"
				KUBE_ALLOW_PRIV="--allow-privileged=false"
				KUBE_MASTER="--master=http://10.40.59.223:8080"
				配置master的地址端口。

				
				vim /etc/kubernetes/kubelet
				KUBELET_ADDRESS="--address=0.0.0.0"
					需要远程连接服务，所以需要所有端口
				KUBELET_HOSTNAME="--hostname-override=node1"
					改成节点名称
				KUBELET_API_SERVER="--api-servers=http://10.40.59.223:8080"
				KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=registry.access.redhat.com/rhel7/pod-infrastructure:latest"
				KUBELET_ARGS=""
				
				vim /etc/kubernetes/proxy
				KUBE_PROXY_ARGS=""
					默认监听所有，不用修改
		3：启动
			systemctl restart flanneld kube-proxy kubelet docker
			systemctl enable flanneld kube-proxy kubelet docker
			systemctl status flanneld kube-proxy kubelet docker
			
># 验证  

	kubectl get nodes
    ping node节点上的flanneld网卡

