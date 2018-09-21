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
	
	git merge命令用于合并指定分支到当前分支
	
	git config --list查看已设配置

	git remote add origin https://github.com/chengwenyue/TSWeb.git 添加远程仓库

	git config --system --list 查看系统config
	git config --global  --list 查看当前用户（global）配置
	git config --local  --list 查看当前仓库配置信息

	
	git config --system --unset credential.helper  清除账户密码
	
	git config --global credential.helper store 保存账户密码
### git pull报错(non-fast-forward)

本地比远程新，直接执行push



---
### git图形化界面

[GitKraken](https://www.gitkraken.com/)


### github初始化

create a new repository on the command line

	echo "# TSWeb" >> README.md
	git init
	git add README.md
	git commit -m "first commit"
	git remote add origin git@github.com:chengwenyue/TSWeb.git
	git push -u origin master

push an existing repository from the command line


	git remote add origin https://github.com/chengwenyue/TSWeb.git
	git push -u origin master


### git本地远程仓库

1.在远程服务器上

	git init --bare 

`--bare`： 创建一个裸仓库，是没有工作区的，只会记录git提交的历史信息， 初始化的目录等同于一个.git目录的内容

2.在本地机器上

	git clone ssh://username@192.168.5.101:22/usr/local/git_repository/demo


使用ssh协议访问远程仓库，

`192.168.5.101`：服务器ip

`22`：ssh端口

`username`：ssh登陆用户名

`/usr/local/git_repository/demo`：远程仓库所在路径

3.在服务器上克隆本地远程仓库

	git clone /usr/local/git_repository/spring-demo/

`/usr/local/git_repository/spring-demo/`：本地远程仓库路径


### git本地服务器

可以在本地搭建类似github的服务，用于git的托管。

- gitlab
- gitblit
- gogs