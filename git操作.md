	? 未知状态（可能没有被添加到版本库中）
	A 添加状态，未被commit
	git status -s //查看状态
	git add <path> //添加文件或目录
	git commit //提交到本地仓库   -m 信息
	git push origin master  ，将本地的master分支推送到origin主机的master分支。如果master不存在，则会被新建。
	
	git rm <path>  -r文件夹
	git push命令用于将本地分支的更新，推送到远程主机。它的格式与git pull命令相似。
	
	git pull命令用于从另一个存储库或本地分支获取并集成(整合)。git pull命令的作用是：取回远程主机某个分支的更新，再与本地的指定分支合并，它的完整格式稍稍有点复杂。
	
	要取回origin主机的next分支，与本地的master分支合并，需要写成下面这样 -
	git pull origin next:master
	
	git branch 列出所有分支
	git checkout (branchname) 切换分支
	
	git clone -b 指定分支 url
	
	git config --list查看已设配置

###git pull报错(non-fast-forward)

本地比远程新，直接执行push



---
###git图形化界面

[GitKraken](https://www.gitkraken.com/)


###git reset

根据–soft –mixed –hard，会对working tree和index和HEAD进行重置:

`git reset --mixed`：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，只保留源码，回退commit和index信息

`git reset --soft`：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可

`git reset --hard`：彻底回退到某个版本，本地的源码也会变为上一个版本的内容，此命令 慎用！