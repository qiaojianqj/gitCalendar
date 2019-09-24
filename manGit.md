## Git基础

1. 什么是版本控制系统（VCS）？

   记录文件的变化状态，以便查阅特定节点的文件和对特定节点文件进行修改。

2. 集中化的版本控制系统

   例如SVN，Perforce等，以C/S的方式进行工作，服务器部署在远端，记录了所有的版本历史和状态，客户端可以从服务器拉取某个特定节点的快照，并作修改和提交。最大的特点是：客户端只拉取到特定节点的快照，不包含整个项目的版本历史和状态。

3. 分布式的版本控制系统

   例如Git，Mercurial等，同样以C/S的方式工作，但是客户端拉取到的不是对某个节点的快照，而是对整个项目的完整的克隆，包含了整个项目的版本历史和状态。最大的特点：方便灵活，由于每个客户端拉取到的都是完整的代码备份，因此可以多人分小组进行协作，指定不同的工作流。

4. Git的工作原理

   Git只关心文件数据的整体是否发生变化，不保存文件的差异。Git 更像是把变化的文件作快照后，记录在一个微型的文件系统中。每次提交更新时，它会纵览一遍所有文件的指纹信息（SHA）并对文件作一快照，然后保存一个指向这次快照的索引。为提高性能，若文件没有变化，Git 不会再次保存，而只对上次保存的快照作一链接。

   Git基于分支的管理方式，近乎所有操作都是在本地进行，只有提交合并拉取等少量操作需要和远端服务器进行交互。

   Git项目中文件的三种基本状态：Git工作区状态：对文件进行修改，还未保存；Git暂存区：将文件的修改保存在一个中间态（git add）；Git本地仓库区：将修改永久保存到Git仓库中来（git commit）。

## Git安装

1. 源码编译安装
2. 安装包安装

## Git配置

每个Git项目文件夹下都有一个.git目录，它记录了Git项目的文件状态。

使用Git前，需要对Git进行配置，命令git config专门用来配置或读取相应的工作环境变量。

Git会依次读取三个地方的环境变量文件：

1. /etc/gitconfig： 系统级配置，对所有用户生效，通过git config --system读取和配置
2. ~/.gitconfig：用户级配置，只对该用户生效，通过git config --global读取和配置
3. .git/config：项目级配置，只对当前Git项目生效，直接vim ./git/config读取和修改配置

每一个级别的配置都会覆盖上层的相同配置。

## Git help

git help cmd （eg：git help config）

git cmd --help （eg：git  config  --help）

man git

## Git项目获取

1. 在本地工作目录下新建git仓库

   git init

2. 从远端现有仓库克隆

   git clone http://url/test.git

   git clone git://url/test.git

## 忽略部分文件

编写.gitignore文件，并加入Git仓库

## git 命令自动补全

环境CentOS：

1. 将git源码目录下文件contrib/completion/git-completion.bash 拷贝到本机 /etc/bash_completion.d/

2. 编辑~/.bashrc，加入：

   ~~~
   if [ -f /etc/bash_completion.d/git-completion.bash ]; then
           . /etc/bash_completion.d/git-completion.bash
   fi
   ~~~

3. 重新source ~/.bashrc

## 常用命令

1. git add，添加修改到暂存区，再git commit -m “comment” 提交到本地仓库。git add -i 进入交互式暂存模式。

2. git commit -a -m “comment” 可以跳过git add，直接提交修改到本地仓库，新增的文件还是untracked状态仍然需要先git add。

3. 文件重命名：git mv old new

4. 查看提交历史：git log

5. ##### 修改最近一次提交：git commit --amend（比如：先git commit，发现还有未提交文件，git add 忘提交文件，再次执行git commit --amend，会覆盖上次的commit，git log中只会记录最近这次的commit。注意不能修改已经push的commit，会导致再次push冲突）

6. 取消保存到暂存区的文件：git reset HEAD file，此操作会将git add的文件还原回修改待添加的状态

7. 放弃对文件的修改：git checkout -- file，此操作会将已经修改待添加的文件的修改删除，慎用

8. 放弃对已经保存到暂存区的文件的修改：git reset --hard commitNo （commitNo指reset到哪个提交点，默认是HEAD所在提交点）

9. git fetch：拉取远程仓库的分支信息和tag信息

10. 打标签：分为：轻量级标签（只是个引用，指向某个提交点），含附注的标签（独立的一个标签对象）

    ~~~
    查看所有标签：git tag
    新建轻量级标签 git tag v1.0
    新建含附注标签 git tag -a v1.0 -m “coment”
    针对某个commit添加标签：git tag -a v1.2 ef35ae2 -m “comment”
    将标签信息push到远端仓库：git push origin --tags
    切换到某个tag：git checkout v1.0
    拉取某个tag：git pull origin :remotes/origin/v1.0
    ~~~

11. git checkout commitNo / git checkout tagName，会导致HEAD执行具体的某次commit或某个tag，此时HEAD处于游离状态（detached），此时基于HEAD的修改会提交到一个新开的匿名分支，一旦切换到其他分支，此detached分支即不可见。解决办法：

    > 1. git reflog 可以查看之前到所有提交记录
    > 2. 找到之前基于HEAD detached分支的最新提交commitNo
    > 3. git checkout commitNo，切换到之前基于HEAD detached分支的最新提交commitNo
    > 4. 基于此commit新建一个分支：git checkout -b newDetached
    > 5. 切换回正常分支如master：git checkout master
    > 6. 执行分支合并即可找回之前基于HEAD detached分支的修改：git merge newDetached

12. 分支基本操作：

    ~~~
    1. 基于当前commit创建新分支：git checkout -b branchxx
    2. 切换到分支master：git checkout master
    3. 在新分支上第一次push同步reference：git push --set-upstream origin branchxx
    4. 在分支master下合并branchxx分支：git merge branchxx
    5. 删除分支：git branch -d branchxx
    6. 同步远程服务器上的数据到本地：git fetch origin
    
    7. 添加多个远程服务器分支：git remote add githubCalendar git@github.com:qiaojianqj/gitCalendar.git
    8. 做新的修改并提交：git cm -a -m "remote add gitCalendar"
    9. 推送本地master分支到远程gitCalendar服务器上对应master分支，
    语法：git push [远程名] [本地分支]:[远程分支]
    默认推送当前所在分支：
    git push githubCalendar （git push githubCalendar master:master）
    10. 查看本地仓库.git/config文件，有两个remote：origin、githubCalendar，每次推送的时候可以指定remote服务器选择推送到哪个远程服务器，默认推送到origin：
    git push (git push origin)
    git push githubCalendar
    11. 删除远程分支githubCalendar-dev：push的时候如果省略 [本地分支]，那就等于是在说“在这里提取空白然后把它变成[远程分支]，也就删除了远程分支：
    11.1 新建远程分支dev：git push githubCalendar master:dev
    11.2 删除远程分支dev：git push githubCalendar :dev
    ~~~

13. 分支的变基-rebase

    > 1. 变基的应用
    >
    > 一般我们使用变基的目的，是想要得到一个能在远程分支上干净应用的补丁 — 比如某些项目你不是维护者，但想帮点忙的话，最好用变基：先在自己的一个本地分支里进行开发，当准备向主项目提交补丁的时候，根据最新的 `origin/master` 进行一次变基操作然后再提交，这样维护者就不需要做任何整合工作（译注：实际上是把解决分支补丁同最新主干代码之间冲突的责任，化转为由提交补丁的人来解决。），只需根据你提供的仓库地址作一次快进合并，或者直接采纳你提交的补丁。
    >
    > 2. 变基不适用的地方
    >
    > 如果把变基当成一种在推送之前清理提交历史的手段，而且仅仅变基那些尚未公开的提交对象，就没问题。如果变基那些已经公开的提交对象，并且已经有人基于这些提交对象开展了后续开发工作的话，就不适用，会使得提交历史重复不清
    >
    > 3. 变基rebase和合并merge的区别
    >
    > 变基是清理分支提交历史，合并会保留分支的提交历史
    >
    > 4. 举例
    >
    > ~~~
    > 0. 前提：当前master分支
    > 1. 新建分支dev并作提交：
    > git checkout -b dev
    > git cm -a -m “comment”
    > 2. 切回master分支并作提交
    > git checkout master
    > git cm -a -m “comment”
    > 3. 此时两个分支分叉了，将dev分支的修改干净的合并到master分支：
    > git checkout dev
    > git rebase master
    > git checkout master
    > git merge dev 
    > 4. 此时dev分支的所有提交历史都没有了，但是dev的所有修改都合并到了masger分支
    > ~~~
    
14. cherry-pick

    > 在某个特性分支上进行开发，进行了多次提交，此时只想将特性分支上的某个提交合并进主分支，即可使用cherry-pick
    >
    > 在主分支上：git cherry-pick commitNo 

15. reflog

    > reflow 引用日志：一份记录最近几个月你的 HEAD 和分支引用的日志
    >
    > git reflog

16. stash

  经常有这样的事情发生，当你正在进行项目中某一部分的工作，里面的东西处于一个比较杂乱的状态，而你想转到其他分支上进行一些工作。问题是，你不想提交进行了一半的工作，否则以后你无法回到这个工作点。解决这个问题的办法就是git stash命令
  
  > git stash
  >
  > git stash list
  >
  > git stash apply stash@{0}
  >
  > git stash branch stashBranchName (基于储藏工作时的所处的提交创建一个新分支)
  
17. 重写提交历史（基于本地提交，push到远端仓库的提交不宜再进行重写，容易引起混乱）

   > 重写最近一次的提交历史：git commit --amend
   >
   > 交互式重写最近n次的提交历史（合并，删除，调整次序）：git rebase -i HEAD~n
   >
   > 全局性更改提交者名称和电子邮件地址：（注意将修改push到仓库，会读取git config的配置，所以最好保持此处的name he email和git config的一致）
   >
   > ~~~
   > git filter-branch --commit-filter '
   >     if [ "$GIT_AUTHOR_EMAIL" = "gaga@GaGadeBook.lan" ];
   >     then
   >             GIT_AUTHOR_NAME="localGa";
   >             GIT_AUTHOR_EMAIL="localGa@docker.com";
   >             git commit-tree "$@";
   >     else
   >             git commit-tree "$@";
   >     fi' HEAD
   > ~~~
18. 