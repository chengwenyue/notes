## Vim 使用记录

### 命令模式操作

	dd 剪切光标所在的一行
	u  撤销
	v  进入选择模式
	V  按行移动
	p  粘贴到光标之后 
	P  粘贴到光标之前
	x  删除当前光标字符
	r  替换当前光标字符
	/ 查找
### 末行模式操作

	w  保存
	q  退出
	sp [文件] 上下分屏 
			ctrl + ww 切换分屏
			ctrl + wq 退出当前分屏
	vps [文件] 左右分屏 

### Vim 查找

	/ 

	n 跳转到下一个匹配项

### Vim 设置项

	set hlsearch  #高亮搜索

	set nu  # 显示行号
	set nonu # 关闭行号显示

	set autoindent # 自动缩进
### Vim 修改tab键为4个空格

修改vimrc文件

	vim /etc/vim/vimrc
	
	centos : vim /etc/vimrc
新增如下内容

    set ts=4
    set expandtab
    set autoindent
