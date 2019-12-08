### Linus shell 编程

#### 输出重定向符号

	>>
	
	命令的输出重定向

例如：

	#!/bin/sh
	/bin/date +%F >> /test/shelldir/ex2.info
	echo "disk info:" >> /test/shelldir/ex2.info
	/bin/df -h >> /test/shelldir/ex2.info
	echo >> /test/shelldir/ex2.info
	echo "online users:" >> /test/shelldir/ex2.info
	/usr/bin/who | /bin/grep -v root >> /test/shelldir/ex2.info
	echo "memory info:" >> /test/shelldir/ex2.info
	/usr/bin/free -m >> /test/shelldir/ex2.info
	echo >> /test/shelldir/ex2.info
	#write root
	/usr/bin/write root < /test/shelldir/ex2.info && /bin/rm /test/shelldir/ex2.info
	#crontab -e
	#0 9 * * 1-5 /bin /sh /test/ex2.sh

把系统信息定时的发送给管理员

#### 变量

1.临时变量

shell脚本中定义的变量，脚本执行完成后自动销毁。使用 `=` 赋值，使用 `$+变量名` 取出变量值

`""`中的变量可以自动替换，`''`中的变量不会输出

例如

	DATE=`/bin/date +%Y%m%d`
	echo "TODAY IS $DATE" 

可以将命令输出的内容赋值给变量
	
例如

	#!/bin/sh
	PWD=`pwd`
	echo $PWD


2.位置变量
	
使用以"$1" "$2" … "$n" 的形式输出所有参数 （1-9），shell执行时的参数

	/bin/ls -l $1
	/bin/ls -l $2
	/bin/ls -l $3

3.特殊变量

	$* 程序的所有参数
	$# 程序参数个数
	$$ 这个程序的PID
	$! 执行上一个后台命令的PID
	$? 执行上一个命令的返回值
	$(0-9) 显示位置变量

例如

	#!/bin/sh
	echo '$# :' $#
	echo '$* :' $*
	echo '$? :' $?
	echo '$$ :' $$
	echo '$0 :' $0

4.永久变量

系统环境变量等

	$PATH

#### 键盘录入

	#!/bin/sh
	read f s t
	echo "the first is $f"
	echo "the second is $s"
	echo "the third is $t"

#### if语句

语法格式 ： if test -d $1 then ... else  fi

变量测试语句可用[]进行简化，如test -d $1 等价于 [ -d $1 ]

注意：["空格" -d $1 "空格"]
例子：

	#!/bin/sh
	# if test $1 then ... else ... fi
	if [ -d $1 ] 
	then 
		echo "this is a directory!"
	else
		echo "this is not a directory!"
	fi


测试变量语法

	str1 = str2　　　　　　当两个串有相同内容、长度时为真 
	str1 != str2　　　　　 当串str1和str2不等时为真 
	-n str1　　　　　　　 当串的长度大于0时为真(串非空) 
	-z str1　　　　　　　 当串的长度为0时为真(空串) 
	str1　　　　　　　　   当串str1为非空时为真
	int1 -eq int2　　　　两数相等为真 
	int1 -ne int2　　　　两数不等为真 
	int1 -gt int2　　　　int1大于int2为真 
	int1 -ge int2　　　　int1大于等于int2为真 
	int1 -lt int2　　　　int1小于int2为真 
	int1 -le int2　　　　int1小于等于int2为真
	
	-r file　　　　　用户可读为真 
	-w file　　　　　用户可写为真 
	-x file　　　　　用户可执行为真 
	-f file　　　　　文件为正规文件为真 
	-d file　　　　　文件为目录为真 
	-c file　　　　　文件为字符特殊文件为真 
	-b file　　　　　文件为块特殊文件为真 
	-s file　　　　　文件大小非0时为真 
	-t file　　　　　当文件描述符(默认为1)指定的设备为终端时为真
	
	-a 　 　　　　　 与 
	-o　　　　　　　 或 
	!　　　　　　　　非

字符串匹配

	if [  `echo $str | grep -e regexp`  ];then .


if elif 语句

	#!/bin/sh
	# if test  then ... elif test then ... else ... fi
	if [ -d $1 ]
		then
		echo "is a directory!"
	elif [ -f $1 ]
		then
		echo "is a file!"
	else 
		echo "error!"
	fi 

#### for循环

for ... done语句格式：

	for 变量 in 名字表
	do 
		命令列表
	done
例如

	#!/bin/sh
	# for var in [params] do ... done
	for var in 1 2 3 4 5 6 7 8 9 10
	do 
		echo "number is $var"

	done

遍历文件：

        for file in ./querymanage*
        do
        if test -f $file
        then
                echo $file
        fi
        done


		echo $1 
		for file in ./querymanage.log.2019-0$1*
		do
		if test -f $file
		then
		    echo $file
		    echo 'grep '5473973' '$file
		    if [ `grep '5473973' $file` ]
		        then
		           echo 'found in '$file
		    fi
		fi
		done

select语句格式：

	#!/bin/sh
	# select var in [params] do ... done
	select var in "java" "c++" "php" "linux" "python" "ruby" "c#" 
	do 
	    break
	done
	echo "you selected $var"
