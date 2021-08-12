# 负载均衡高可用
#### nginx+keepalived

> 1.安装
> * 在lb01与lb02上分别安装keepalived软件
		[root@lb01 ~]# yum install keepalived -y  
		[root@lb02 ~]# yum install keepalived -y

> 2.配置 

> * 配置lb01, keepalived-master  
	
	
		[root@lb01 ~]# vim /etc/keepalived/keepalived.conf
		global_defs {               
		         router_id lb01          
		      }
		      
		vrrp_instance VI_1 {
		    state MASTER
		    interface eth0
		    virtual_router_id 51
		    priority 150
		    advert_int 1
		    authentication {
		        auth_type PASS
		        auth_pass 1111
		    }
		    virtual_ipaddress {
		        10.0.0.3/24 dev eth0
		    }
		}
	
> * 配置lb02, keepalived-backup

		[root@lb02 ~]# cat /etc/keepalived/keepalived.conf
		global_defs {
		    router_id lb02
		}
		      
		    vrrp_instance VI_1 {
		    state BACKUP
		    interface eth0
		    virtual_router_id 51
		    priority 100
		    advert_int 1
		    authentication {
		        auth_type PASS
		        auth_pass 1111
		    }
		    virtual_ipaddress {
		        10.0.0.3/24 dev eth0
		    }
		}	
			
# 启动keepalived
	systemctl enable keepalived
	systemctl start keepalived
	
	

