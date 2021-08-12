# nginx代理
> ### nginx 跳转

	vim /etc/nginx/proxy_params     通用代理文件
	
	proxy_redirect default;
	proxy_set_header Host $http_host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	
	proxy_connect_timeout 30;
	proxy_send_timeout 60;
	proxy_read_timeout 60;
	
	proxy_buffer_size 32k;
	proxy_buffering on;
	proxy_buffers 4 128k;
	proxy_busy_buffers_size 256k;
	proxy_max_temp_file_size 256k;


	vim /etc/nginx/conf.d/test.conf
	server {
		listen 8080;                               访问8080端口
		location / {
			proxy_pass  http://49.232.14.83:80;    跳转其他服务器
			include proxy_params;                  引用proxy_params文件,传送主机头信息 到49.232.14.83上
	}



> ### nginx 负载均衡  SLB网络负载均衡  轮询

	upstream ops_load {                                       定义资源池，http层，必须在server层上面
	        server 49.232.14.83:8081;                         
	        server 49.232.14.83:8082;                         
	        server 49.232.14.83:8083;                         
	}                                                         
	server {                                                                                                     
	        listen 80;                                        
	        server_name blog.leebao.com;                      
	        index index.html;                                 
	                                                          
	        location / {                                      
	                proxy_pass http://ops_load;               转发的资源池
	}
	}


> ### nginx状态			概述

| nginx状态| 概述|
| -----  | ---- | 
|  down | 当前的server暂时不参与负载均衡| 
| backup | 预留的备份服务器| 
| max_fails | 允许请求失败的次数| 
| fail_timeout | 经过max_fails失败后, 服务暂停时间| 
| max_conns | 限制最大的接收连接数| 


	upstream load_pass {
	    server 49.232.14.83:8001 down;
	    server 49.232.14.83:8002 backup;
	    server 49.232.14.83:8003 max_fails=1 fail_timeout=10s;         服务器配置不一样会用到
	}
	
	location  / {
	    proxy_pass http://ops_load;
	    include proxy_params;
	}

| 调度算法 | 概述|
| -----  | ------ | 
|  轮询 | 按时间顺序逐一分配到不同的后端服务器(默认)| 
| weight | 加权轮询,weight值越大,分配到的访问几率越高| 
| ip_hash | 每个请求按访问IP的hash结果分配,这样来自同一IP的固定访问一个后端服务器| 
| url_hash | 按照访问URL的hash结果来分配请求,是每个URL定向到同一个后端服务器| 
| least_conn | 最少链接数,那个机器链接数少就分发| 
| hash关键数值 | hash自定义的key| 


> #### Nginx负载均衡权重轮询具体配置  

	upstream load_pass {
	    server 192.168.56.11:8001;
	    server 192.168.56.12:8002 weight=5;
	    server 192.168.56.13:8003;
	}

> #### Nginx负载均衡ip_hash具体配置 
 
	upstream load_pass {
	    ip_hash;
	    server 192.168.56.11:8001;
	    server 192.168.56.12:8002;
	    server 192.168.56.13:8003;
	}

> #### Nginx负载均衡url_hash具体配置

	upstream load_pass {
	    hash $request_uri;
	    server 192.168.56.11:8001;
	    server 192.168.56.12:8002;
	    server 192.168.56.13:8003;
	}

> #### nginx的TCP四层代理

	vim /etc/nginx/nginx.conf
	stream {
	        upstream ssh_proxy {
	                hash $remote_addr consistent;
	                server 192.168.56.103:22;
	        }
	        upstream mysql_proxy {
	                hash $remote_addr consistent;
	                server 192.168.56.103:3306;
	        }
	    server {
	        listen 6666;
	        proxy_connect_timeout 1s;
	        proxy_timeout 300s;
	        proxy_pass ssh_proxy;
	    }
	    server {
	        listen 5555;
	        proxy_connect_timeout 1s;
	        proxy_timeout 300s;
	        proxy_pass mysql_proxy;
	    }
	}
	



> #### nginx动静分离

	upstream ops_load {
	        server 49.232.14.83:8080;
	        server 49.232.14.83:8082 down;
	        server 49.232.14.83:8083 down;
	}
	server {
	
	        listen 80;
	        server_name blog.leebao.com;
	        index index.html;
	
	        location ~ .*\.(jpg|png|gif)$ {                       jpg类似格式照片代理到另外一台机器上
	                proxy_pass http://10.40.59.25;                jpg类似格式照片代理到另外一台机器上
	                include proxy_params;                         jpg类似格式照片代理到另外一台机器上
	}
	        location ~ .*\.(jsp)$ {                               jsp格式通过资源吃代理到另外一台机器上
	                proxy_pass http://ops_load;                   jsp格式通过资源吃代理到另外一台机器上
	                include proxy_params;                         jsp格式通过资源吃代理到另外一台机器上
	}
	}

> #### nginx 区分手机访问方式

	        listen 80;
	        server_name 49.232.14.83;
	        location / {
	                if ($http_user_agent ~* "android"){
	                        root /data/android;
	                }
	                if ($http_user_agent ~* "Iphone"){                $http_user_agent  日志格式
	                        root /data/iphone;
	                }
	}
	}



