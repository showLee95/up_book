# 持续化集成
> 1：获取代码（直接拉取）  
> 2：编译（可选）  
> 3：配置文件放进去  
> 4：打包  
> 5：SCP到目标服务器  
> 6：将目标服务器移除集群  
> 7：解压  
> 8：放置到webroot  
> 9：scp差异文件  
> 10：重启  	



	#!/bin/bash
	#Data/time
	CDATE=`date +%Y-%m-%d`
	CTIME=`date +%H-%M-%S`
	
	NODE_LIST='49.232.14.83'
	#shell env
	SHELL_NAME="deploy.sh"
	SHELL_DIR="/home/www"
	SHELL_LOG="${SHELL_DIR}/${SHELL_NAME}.log"
	#Code Env
	PRO_NAME="web-demo"
	CODE_DIR="/deploy/code/web-demo"
	CONFIG_DIR="/deploy/config/web-demo"
	TMP_DIR="/deploy/tmp"
	TAR_DIR="/deploy/tar"
	LOCK_FILE="/home/www/deploy.lock"
	
	usage(){
		echo $"Usage: $0 [ deploy | rollback ]"
	}
	writelog(){
	   LOGINFO=$1
	   echo "${CDATE} ${CTIME}: ${SHELL_NAME} : ${LOGOINFO}" >> ${SHELL_LOG}
	}
	
	
	shell_lock(){
		touch ${LOCK_FILE}
	}
	
	shell_unlock(){
		rm -f ${LOCK_FILE}
	
	}
	
	code_get(){
		writelog "code_get";
		cd $CODE_DIR && echo "git pull"
		cp -r ${CODE_DIR} ${TMP_DIR}/
		API_VER='123'
	}
	
	
	
	code_build(){
		  echo 'code_build'
	
	}
	
	code_config(){
		echo code_config
		/bin/cp -r $CONFIG_DIR/bash/* $TMP_DIR/$PRO_NAME
		PKG_NAME="${PRO_NAME}"_"$API_VER"_"${CDATE}-${CTIME}"
		cd  ${TMP_DIR} && mv ${PRO_NAME} ${PKG_NAME}
	}
	
	
	code_tar(){
		writelog "code_tar"
		cd ${TMP_DIR} && tar zcf   ${PKG_NAME}.tar.gz ${PKG_NAME} 
		writelog "${PKG_NAME}.tar.gz"
	}
	
	
	code_scp(){
		echo code_scp
		for node in $NODE_LIST;do
			scp ${TMP_DIR}/${PKG_NAME}.tar.gz $node:/opt/webroot/
		done
	}
	
	
	cluster_node_remove(){
		echo cluster_node_remove
	}
	
	#解压
	code_deploy(){
		echo code_deploy
		for node in $NODE_LOST;do 
			ssh $node  "cd /opt/webroot && tar zxf ${PKG_NAME}.tar.gz"
		done	
		scp ${CONFIG_DIR}/other/192.168.56.12.crontab.xml 192.168.56.12:/opt/webroot/${PKG_NAME}
	}
	
	
	config_diff(){
		echo config_diff
	}
	
	code_test(){
		echo code_test
	}
	
	
	cluster_node_in(){
		echo cluster_node_in
	
	}
	rollback(){
		echo rollback
	}
	
	
	main(){
	    if [ -f  ${LOCK_FILE}  ];then
		echo "已经执行" && exit;
	    fi
	    DEPLOY_METHOD=$1
	    case $DEPLOY_METHOD in
	        deploy)
	                shell_lock;
			code_get;
	                code_build;
	                code_config;
	                code_tar;
	                code_scp;
	                cluster_node_remove;
	                code_deploy;
	                config_diff;
	                code_test;
	                cluster_node_in;
			shell_unlock;
	                ;;
	        rollback)
			shell_lock;
	                rollback;
			shell_unlock;
	                ;;
	        *)
	                usage;
	    esac
	}
	main $1

> ### 所有的web服务都应该使用普通用户，web服务都不应该监听80端口，除了负载均衡 
> ### 可能会用到得命令

	ssh-keygen  -t rsa
	将id_rsa.pub文件复制到需要登录的机器上改名authorized_keys，600权限
	sleep 60; 60秒后执行下一步


