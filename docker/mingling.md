	docker run 启动镜像
	
	docker run --name webserver -d -p 80:80 nginx 
	指定名字为webserver的容器，-d放在后台，-p端口本地80映射容器80  使用nginx镜像。
	
	docker build   -t  nginx:v4 . 使用当前目录下对的dockerfile创建一个名为nginx,标签v4的镜像。
	 
	docker 指定基础镜像的时候可以指定标签。
	
	docker run -d -p 80:80 nginx
	run（创建并运行一个容器）
	-d 放在后台
	-p 端口映射
	80:80
	前面是服务器端口，后面是容器端口
	nginx docker镜像的名字
	
	
	
	
	docker commit  --选项  容器名或ID  镜像名 :  标签 
	将容器保存为镜像 
	
	docker pull 从dockerhub仓库下载镜像
	docker run  启动镜像成为容器
	
	
	docker exec 进入容器
	docker exec -it webserver bash
	
	docker commit 容器保存为镜像
	docker commit webserver nginx:v2
	
	docker ps查看存活容器 -a  查看所有容器
	docker build 使用dockerfile创建镜像
	docker exec -it  容器id或容器名字 /bin/bash
	
	docker save  导出镜像
	docker image save centos > docker-centos7.4.tar.gz
	
	docker load   导入镜像
	docker image load -i docker-centos7.4.tar.gz
	
	docker export 7691a814370e > ubuntu.tar
	导出容器快照到本地文件
	
	docker inspect 容器名或ID
	查看容器信息 ip地址等
	
	进入容器(目的，调试，排错)
	***	docker exec  （会分配一个新的终端tty）
		docker exec [OPTIONS] CONTAINER COMMAND [ARG...]	
	    docker exec -it  容器id或容器名字 /bin/bash
		docker attach（使用同一个终端）
			docker attach [OPTIONS] CONTAINER
		nsenter(安装yum install -y util-linux 弃用)
		
		
	docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py
	
	iptables -t nat -L -n  查看本地防火墙转发信息	
