# nginx 请求限制


> ### http段配置请求限制, rate限制速率，限制一秒钟最多一个IP请求 

	limit_req_zone $binary_remote_addr zone=req_zone:10m rate=1r/s;
> ### 格式：

	server {
	    listen       80;
		server_name  localhost;
	
		#charset koi8-r;
		#access_log  /var/log/nginx/host.access.log  main;
	
		location / {
		    root   /usr/share/nginx/html;
		    index  index.html index.htm;
		//1r/s只接收一个请求,其余请求拒绝处理并返回错误码给客户端
		    limit_req zone=req_zone;
		//请求超过1r/s,剩下的将被延迟处理,请求数超过burst定义的数量, 多余>的请求返回503
		    limit_req zone=req_zone burst=3 nodelay;
	}
	
> ### 测试:

	yum install  httpd-tools
	ab -n 50 -c 20 http://49.232.14.83/index.html   50个请求  并发20 


> ### 基于IP的访问控制 禁止某一个IP访问  

 	location ~^/1.html{
 	        root /data/test/;
 	        index index.html;
 	        deny 115.171.62.124;  拒绝 deny 
 	        allow all; 			  允许 allow
 			//allow 115.171.62.124  	注意顺序
 			//deny all; 
 	}

 
> ### 用户的访问控制

	yum install httpd-tools
	htpasswd  -c  /etc/nginx/auth_conf admin
	htpasswd  -b  /etc/nginx/auth_conf admin1 admin123 添加新的用户及密码   
	
	location /down {
	    root /data/test/;
	    autoindex on;
	    autoindex_localtime on;
	    autoindex_exact_size off;
	auth_basic "请输入用户名及密码";
	auth_basic_user_file /etc/nginx/auth_conf;
	}


