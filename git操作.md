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

###git pull报错(non-fast-forward)

本地比远程新，直接执行push
