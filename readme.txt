一些命令：

1.初始仓库 git init

2.新增 更新文件到仓库 git add ... --> git commit -m '...'
可以多个add 然后一次提交commit

3.查看仓库状态 git status

4.查看修改的内容 git diff

5.查看提交历史 
	git log
	git log --pretty=oneline 单行查看

6.查看操作历史
	git reflog

7.回滚 更新到指定版本
	git reset --hard HEAD^  回到上一个版本
	git reset --hard HEAD^^ 回到上上个版本
    git reset --hard 版本的ID（前几位即可了）

8.add -> 暂存区 -> commit -> 仓库 | 每次修改都要add 下 才能更新

***************
远程仓库
9.
本机 ssh-keygen -t rsa -C "youremail@example.com" 生成 ./ssh 文件夹 含id_rsa id_rsa.pub 两个文件

登陆GitHub 打开设置 里面的 "SSH Keys" 页面 Add SSH Key 填上任意Title 在Key文本框中粘贴id_rsa.pub的内容 确定即可

GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了

$ git remote add origin git@github.com:Turtle1991/mygit.git （如果用https的那种方式 每次都要输用户名和密码）
把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

*****
注意：提示出错信息：fatal: remote origin already exists.

	解决办法如下：
    	1、先输入$ git remote rm origin
   		2、再输入$ git remote add origin git@github.com:Turtle1991/mygit.git		
		就不会报错了！

*****
从现在起，只要本地作了提交，就可以通过命令：
$ git push origin master
把本地master分支的最新修改推送至GitHub 同步

10.github上先生成一个仓库（如fromGitHub，记得同时生成README.md文件） 然后用 $ git clone git@github.com:Turtle1991/fromGitHub.git 将仓库克隆到本地 这样本地就有了仓库 同时也是跟github是同步的

***************
分支管理

新建分支 git branch name
切换分支 git checkout name
查看当前分支（*指向为当前分支） git branch
新建和切换分支 git checkout -b name
合并分支(name)到当前分支 git merge name
删除分支 git branch -d name

合并分支 会将分支上的版本给都合并到这边的分支上来

如果master 分支 和 dev 分支 上都有新的版本出现 在合并分支的时候会出现冲突conflict 此时冲突的文件 里面会把冲突的内容都显示出来 (<<<< ==== >>>>) 让你选择是要留下那个 然后保存提交 （add commit）得到一个新的版本（conflict fixed） 这样就解决了冲突，后面分支 dev 就可以合并 master分支了（不会发生冲突），从而dev分支也会得到一个最新的版本。

合并后 在master 分支中 保存了master 和 dev 冲突的两个版本 还有 最终的那个解决版本
dev 分支 同理

*************************************************************
可以查看分支的合并情况：
git log --graph --pretty=oneline --abbrev-commit
*************************************************************
！！！！！！！！！！！！！这个重要！！！！！！可以看到整个项目的分支流程图！！！
在合并分支的时候 如果没有加上 --no-ff 选项 则采用“Fast forward”方式进行合并，如果合并的时候master分支没有冲突啊 什么的 ，那么后面从分支流程图里 是无法看出该版本是合并得来的，加上该选项后，就可以看出该版本是合并得到的
git merge --no-ff -m 'merge with no-ff' dev
(因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去)
**********************************************************************************

git diff <source_branch> <target_branch> 合并改动前 可以用这个对比分支的不同

***********
实际项目中master是主线，每个开发小组都有自己的分支，比如dev-1, dev-2，每个组都要干两件事：
1)把自己的dev-N往master merge：自己的修改提交到master，
2)把master往自己的dev-N merge：获得别人的修改
***********

BUG分支

说是 在dev分支上工作 后发现有BUG要修复 此时又不想提交 dev分支上的修改 就可以用
git stash 来把当前的工作现场“储藏”起来，然后切换到 master 分支 创建一个修复 BUG的分支issue-1 分支 （git checkout -b issue-1）在分支上修复BUG，然后切回 master 分支,合并issue-1分支（git merge --no-ff issue-1），然后删除issue-1分支。再切回 dev分支，恢复之前的工作现场（git stash pop），继续之前的工作，然后提交修改。切回master分支，合并分支，如果有conflict，则处理，得到一个新的版本。

首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支

Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；
另一种方式是用git stash pop，恢复的同时把stash内容也删了（一般用这种）

可以用 git stash list 查看stash的内容

目前的理解是：这样做 可以使修复BUG的时候不影响dev分支下的修改。

*******************
Feature 分支

对于开发新的功能 一般在新的分支上开发 然后再合并到主分支

如果分支的内容部合并到 master 分支，此时要删除该分支 必须用
git branch -D 分支名 来删除

*******************
查看远程库的信息，用git remote
git remote -v显示更详细的信息

master分支是主分支，因此要时刻与远程同步；
dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
推送哪些分支 由你决定

多人协作的工作模式通常是这样：
首先，可以试图用git push origin branch-name推送自己的修改；
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！
如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。

*******************
创建标签

git tag tagname (默认是对最新提交的版本打上标签)
git tag tagname commit_id (对指定版本打上标签)

git tag 查看所有标签
git show tagname 查看标签信息

git tag -a tagname -m '说明文字' commit_id 可以指定标签信息 
git tag -s tagname -m '说明文字' commit_id 可以用PGP签名标签

git tag -d tagname 删除本地标签
git push origin tagname 推送某个标签到远程
git push origin --tags  推送全部尚未推送到远程的本地标签

删除远程标签：
	1.先删除本地的标签 git tag -d tagname
	2.从远程删除 git push origin :refs/tags/tagname

*******************
关于github
看到好的开源项目 想修改 可以先把该项目 fork 下 这样就克隆该项目到自己的账号下了，
然后在自己的账号下 克隆到本地 ，修改 推送到自己账号下的仓库，然后给项目的作者发起一个
pull request 。对方如果接受 就可以让你的修改给他了。

*******************
自定义git

让Git显示颜色，会让命令输出看起来更醒目：
git config --global color.ui true

***********
1. 忽略某些文件时，需要编写.gitignore。
2. .gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理

***********
配置别名

如 git config --global alias.st status (配置status的别名为st)

则可以这样查看状态  git st

--global参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用

在用户根目录下找一个隐藏文件.gitconfig里面有所有别名配置，删掉即可删除对应的别名

***********
搭建Git服务器 

用到在看

********************************
假如你想要丢弃你所有的本地改动与提交，
可以到服务器上获取最新的版本并将你本地主分支指向到它：
git fetch origin
git reset --hard origin/master

要更新你的本地仓库至最新改动，执行：
git pull
以在你的工作目录中 获取（fetch） 并 合并（merge） 远端的改动。

********************************
Git的官方网站：http://git-scm.com

http://www.git-tower.com/blog/assets/2013-05-22-git-cheat-sheet/cheat-sheet-large01.png

git - 简易指南（还不错!!!!）
http://www.bootcss.com/p/git-guide/

一些.gitignore文件
https://github.com/github/gitignore

查看最近几条提交记录
git log -1 (最近一条记录)









