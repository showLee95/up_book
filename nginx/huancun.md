> ### nginx 文件压缩

	vim /etc/nginx/mime.types   压缩文件类型对应格式
	server{
	        listen 80;
	        server_name _;
	        location ~ .*\.(jpg|gif|png)$ {                       匹配规则
	                gzip on;	                                  压缩是否开启
	                gzip_http_version 1.1;                        压缩协议版本
	                gzip_comp_level 2;                            压缩级别
	                gzip_types text/plain application/json application/x-javascript application/css application/xml application/xml+rss text/javascript application/x-httpd-php image/jpeg image/gif image/png;
																  压缩那类文件
					root /data/test/images;                      
	        }
	}



> ### nginx 浏览器缓存

        location ~ .*\.(jpg|gif|png)$ {
                gzip on;
                gzip_http_version 1.1;
                gzip_comp_level 2;
                gzip_types text/plain application/json application/x-javascript application/css application/xml application/xml+rss text/javascript application/x-httpd-php image/jpeg image/gif image/png;
                root /data/test/images;
                expires 30d;                             缓存30天
			   #expires 1h；							 缓存1小时
		}


> ### ngxin静态资源防盗链

		valid_referers none blocked 49.232.14.83;
		    if ($invalid_referer) {
		        return 403;
		}

测试  

		curl -e "http://www.baidu.com" -I http://49.232.14.83/huancun.png
		HTTP/1.1 403 Forbidden
		Server: nginx/1.17.3
		Date: Thu, 10 Oct 2019 03:29:29 GMT
		Content-Type: text/html
		Content-Length: 153
		Connection: keep-alive

