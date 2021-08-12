	#!/bin/bash
	for i in `seq 2 254`;
	do
	        ping  -c 2 10.10.100.$i > /dev/null
	        ret=$?
	        if [ $ret -ne 0 ];then
	                echo "ping 10.10.100.${i} no"
	        else
	                echo "ping 10.10.100.${i} yes"
	        fi
	done
