git 如何在本地同步远程分支和tag https://blog.csdn.net/wei371522/article/details/83186077

ubuntu 添加多个ssh公钥和私钥 https://www.jianshu.com/p/fe215c52c534

# 问题场景

git 同步本地分支到远程指定分支

```sh
git push origin msl-dev:stephen_dev
```

origin ：远程仓库地址

msl-dev ：本地分支

stephen_dev：为远程分支

问题场景

git 下拉远程仓库指定分支到本地分支

```
git fetch
git branch -a
# 现快速的代上面的分支，你可以直接切换到那个分支
git checkout origin/feature
# 但是，如果你想在那个分支工作的话，你就需要创建一个本地分支
git checkout -b feature origin/feature
```



问题场景

git 下拉远程仓库指定分支的指定文件到本地分支的指定文件


``` sh
# 查看分支
git branch -a

# 新建dev分支
git branch dev

# 切换dev分支
git checkout dev

# 本地仓库和远程仓库同步信息
git fetch

# 将远程分支的新代码同步到当前分支上,
git checkout origin/dev /to/path/
```





# 问题场景：

同事B下拉同事A远程仓库代码，并添加自己所开发的模块，想同步代码到同事A的远程仓库

此时出现一下情况

```sh
 $ git push -u origin master 
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@gitlab.xxx/common.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```



### 分析：

两人同时fetch了一个分支。 第一个人修改后提交，第二个人提交就失败。

### 解决方法：

1.强制推送

```sh
git push -f
```

2.正常解决

```sh
 git fetch origin
 git merge origin/master
 # 人工看本地和远程的不同，取舍，建议用pycharm Git-Repository-merges changes 可视化看不同之处
 git add *
 git commit -m 'fix confilt'
 git push -u origin master
```





# 问题场景1：
同事A创建了本地分支branchA并push到了远程->同事B在本地拉取(git fetch)了和远程branchA同步的本地分支branchA->同事A开发完成将远程分支branchA删除（远程仓库已经不存在分支branchA）->同事B用git fetch同步远端分支，git branch -r发现本地仍然记录有branchA的远程分支

分析：远端有新增分支，git fetch可以同步到新的分支到本地，但是远端有删除分支，直接"git fetch"是不能将远程已经不存在的branch等在本地删除的

解决方法：
git fetch --prune #这样就可以实现在本地删除远程已经不存在的分支
或者简略：
git fetch -p
git fetch -p origin
git remote prune origin

branch常用的命令：
git branch -a #查看本地和远程所有的分支
git branch -r #查看所有远程分支
git branch #查看所有本地分支
git branch -d -r origin/branchA #删除远程分支

其他较常用的命令
git fetch #将本地分支与远程保持同步
git checkout -b 本地分支名x origin/远程分支名x #拉取远程分支并同时创建对应的本地分支

```sh
error: Your local changes to the following files would be overwritten by checkout:
	.idea/BigProject.iml
	.idea/misc.xml
	.idea/workspace.xml
Please commit your changes or stash them before you switch branches.
Aborting

```



问题场景2：
本地分支提示：Git Your branch is ahead of ‘origin/master’ by X commits，你想让本地直接和远程保持同步，想让不再提示这个讨厌信息，那么如果你本地的commit确保不想要，可以如下操作：
解决方法：
1）git reset --hard origin/master

或者还有一个将本地代码与服务器代码更新一致的语句
2）git branch -u origin/master

如果想直接回退版本让远程和本地代码保持一致，那就确保本地代码没问题之后强制推到远程
git push -f origin master

2.git 如何同步本地tag与远程tag
问题场景：
同事A在本地创建tagA并push同步到了远程->同事B在本地拉取了远程tagA(git fetch)->同事A工作需要将远程标签tagA删除->同事B用git fetch同步远端信息，git tag后发现本地仍然记录有tagA

分析：对于远程repository中已经删除了的tag，即使使用git fetch --prune，甚至"git fetch --tags"确保下载所有tags，也不会让其在本地也将其删除的。而且，似乎git目前也没有提供一个直接的命令和参数选项可以删除本地的在远程已经不存在的tag（我目前是没找到有关这类tag问题的git命令~~，有知道的同学可以告知我下，互相进步）。
解决方法：

git tag -l | xargs git tag -d #删除所有本地分支
git fetch origin --prune #从远程拉取所有信息

#查询远程tags的命令如下：
git ls-remote --tags origin

tag常用git命令：
git tag #列出所有tag
git tag -l v1.* #列出符合条件的tag（筛选作用）
git tag #创建轻量tag（无-m标注信息）
git tag -a -m ‘first version’ #创建含标注tag
git tag -a f1bb97a(commit id) #为之前提交打tag

git push origin --tags #推送所有本地tag到远程
git push origin #推送指定本地tag到远程

git tag -d #删除本地指定tag
git push origin :refs/tags/ #删除远程指定tag

git fetch origin #拉取远程指定tag
git show #显示指定tag详细信息

参考阅读
http://smilejay.com/2013/04/git-sync-tag-and-branch-with-remote/
https://blog.zengrong.net/post/1746.html





# 一、新建代码库

```
## 在当前目录新建一个Git代码库
$ git init

## 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

## 下载一个项目和它的整个代码历史
$ git clone [url]
```

# 二、配置

Git的设置文件为.gitconfig，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

```
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```

# 三、增加/删除文件

```
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

# 四、代码提交

```
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```

# 五、分支

```
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 新建一个分支，指向指定commit
$ git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

# 八、远程同步

```
# 下载远程仓库的所有变动
$ git fetch [remote]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```

# 九、撤销

```
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```

# 十、其他

```sh
# 生成一个可供发布的压缩包
$ git archive
```