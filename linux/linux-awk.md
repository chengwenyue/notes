## Linux AWK

命令使用方式

	awk [选项参数] 'script' var=value file(s)
	或
	awk [选项参数] -f scriptfile var=value file(s)


awk 默认是以 空格或`tab`分割。
可以通过 `-F` 指定分隔符。例如：

	以 # 分割
	awk -F # '{print $1,$2}' log.txt 


### 1. AWK内置变量


<table class="reference"><thead><tr><th>变量</th><th>描述</th></tr></thead><tbody><tr><td>$n</td><td>当前记录的第n个字段，字段间由FS分隔</td></tr><tr><td>$0</td><td>完整的输入记录</td></tr><tr><td>ARGC</td><td>命令行参数的数目</td></tr><tr><td>ARGIND</td><td>命令行中当前文件的位置(从0开始算)</td></tr><tr><td>ARGV</td><td>包含命令行参数的数组</td></tr><tr><td>CONVFMT</td><td>数字转换格式(默认值为%.6g)ENVIRON环境变量关联数组</td></tr><tr><td>ERRNO</td><td>最后一个系统错误的描述</td></tr><tr><td>FIELDWIDTHS</td><td>字段宽度列表(用空格键分隔)</td></tr><tr><td>FILENAME</td><td>当前文件名</td></tr><tr><td>FNR</td><td>各文件分别计数的行号</td></tr><tr><td>FS</td><td>字段分隔符(默认是任何空格)</td></tr><tr><td>IGNORECASE</td><td>如果为真，则进行忽略大小写的匹配</td></tr><tr><td>NF</td><td>一条记录的字段的数目</td></tr><tr><td>NR</td><td>已经读出的记录数，就是行号，从1开始</td></tr><tr><td>OFMT</td><td>数字的输出格式(默认值是%.6g)</td></tr><tr><td>OFS</td><td>输出记录分隔符（输出换行符），输出时用指定的符号代替换行符</td></tr><tr><td>ORS</td><td>输出记录分隔符(默认值是一个换行符)</td></tr><tr><td>RLENGTH</td><td>由match函数所匹配的字符串的长度</td></tr><tr><td>RS</td><td>记录分隔符(默认是一个换行符)</td></tr><tr><td>RSTART</td><td>由match函数所匹配的字符串的第一个位置</td></tr><tr><td>SUBSEP</td><td>数组下标分隔符(默认值是/034)</td></tr></tbody></table>

> 来自菜鸟教程	

例子：demo.awk


	#!/bin/awk -f
	BEGIN{
	
		printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n","FILENAME","ARGC","FNR","FS","NF","NR","OFS","ORS","RS";
		printf "---------------------------------------------\n"
	}
	
	{
		printf "%4s %4s %4s %4s %4s %4s\n",FILENAME,ARGC,FNR,FS,NF,NR
		# for (i = 1; i <= NF; ++i) printf "$%s : %4s\n", i,$i;
	}
	
	END{
		printf "---------------------------------------------\n";
		printf "%4s %4s %4s \n","OFS","ORS","RS";
		printf "%4s %4s %4s\n",OFS,ORS,RS;
	}


### 2. AWK 工作原理

AWK 工作流程可分为三个部分：

- 读输入文件之前执行的代码段（由BEGIN关键字标识）。
- 主循环执行输入文件的代码段。
- 读输入文件之后的代码段（由END关键字标识）。

命令结构如下：

	awk 'BEGIN{ commands } pattern{ commands } END{ commands }'

包括三个块：

1. 开始块 `BEGIN {awk-commands}`
2. 主体块 `/pattern/ {awk-commands}`
3. 结束块 `END {awk-commands}`

其中`pattern{ commands }`可以有多个，根据前面的`pattern`进行匹配执行。
例如：

	# 所有的行都会进入此body
	{
		# printf "%4s %4s %4s %4s %4s %4s\n",FILENAME,ARGC,FNR,FS,NF,NR
	    city = substr($8, 13, 2)
		arr[city]++;
	}
	# 只会在$2以17开头的行才会进入此body
	$2 ~ /^17/{
		printf "%s \n",$0;
	}

执行流程：

![AWK工作流程](https://www.runoob.com/wp-content/uploads/2018/10/20170719154838100.png)

具体步骤如下：

1. 通过关键字 BEGIN 执行 BEGIN 块的内容，即 BEGIN 后花括号 {} 的内容。
2. 完成 BEGIN 块的执行，开始执行body块。
3. 读入有 \n 换行符分割的记录。
4. 将记录按指定的域分隔符划分域，填充域，$0 则表示所有域(即一行内容)，$1 表示第一个域，$n 表示第 n 个域。
5. 依次执行各 BODY 块，pattern 部分匹配该行内容成功后，才会执行 awk-commands 的内容。
6. 循环读取并执行各行直到文件结束，完成body块执行。
7. 开始 END 块执行，END 块可以输出最终结果。

注：

1. BEGIN和END块都只执行一次
2. 对于每一行都会执行body块

###3. AWK数组
###4. AWK条件判断和循环
###5. AWK内置函数
###6. AWK自定义函数

### 参考博客

- [https://www.runoob.com/linux/linux-comm-awk.html](https://www.runoob.com/linux/linux-comm-awk.html)