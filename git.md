#GIT

[TOC]

##版本库

###1.搭建服务器、初始化版本库
1.1 [搭建服务器参考地址](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)
1.2 本地版本库
```bash
$ mkdir gitlearn && cd gitlearn
$ git init 
Initialized empty Git repository in ......
```
###2.添加、克隆远程仓库
```bash
$ git clone git@git.in.codoon.com:ad-pages/console-codoon-admin.git
```
```bash
$ git remote add origin git@git.in.codoon.com:ad-pages/console-codoon-admin.git
```
`第一次推送 -u 把本地的master分支和远程的master分支关联起来`
```bash
$ git push -u origin master #--set-upstream
$ git pull origin master
```
```bash
$ git remote -v #查看远程分支
$ git remote rename old new #修改远程仓库名称
$ git remote remove name #移除远程仓库
```

###3.分支
####3.1关于分支
```bash
$ git branch -a | -r #查看所有分支 | 远程分支
$ git checkout -b new_branch #创建分支并切换到新分支
$ git branch -m feature/old feature/new
$ git branch -d | -D branch #-d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
#删除远程分支
$ git push origin :heads/branch
$ git push origin :branch
```

`查看其他分支上的文件而无需切换到那个分支 也可以指定到一个临时文件`
```bash
 $ git show some-branch:some-file.js
 $ git show some-branch:some-file.js > temp
```
`查看另一个分支上文件与当前分支上文件的差异`
```cmd
 $ git diff some-branch some-file.js
```
####3.2分支的合并，解决冲突
>合并分支时，git会用`fast forward`模式，在这种模式下，删除分支后，会丢掉分支的提交信息
>解决冲突原则:以自己修改的分支为准，不确定的找到对应的开发一起确认
```bash
$ git log -p file #查看某个文件的提交记录

$ git merge --no-ff -m "merge with no-ff" dev
```
git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作
```bash
$ git stash
... #修复bug

#恢复之前工作区
$ git stash list
$ git stash apply #step 1
$ git stash drop #step 2
#或
$ git stash pop

#对于多次stash
$ git stash list
$ git stash apply stash@{0}
```
分支-变基  [参考](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

####3.3分支提交
```bash
$ git add .
$ git add file

$git commit -m "message"
$git commit -am "message"

```
`修复而非新建提交`

1.修复你的拼写错误
2.将修正过的文件暂存 \$ git add file 
3.运行 $ git commit –amend -m "message" 将会把最近一次的变更追加到你最新的提交。同时也会给你一个编辑提交信息的机会
4.提交
####3.4撤销操作、回退
![](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

![](http://www.liaoxuefeng.com/files/attachments/001384907720458e56751df1c474485b697575073c40ae9000/0)

![](http://www.liaoxuefeng.com/files/attachments/0013849077337835a877df2d26742b88dd7f56a6ace3ecf000/0)

#####3.4.1 *`工作区的修改 撤销修改`*
```bash
#没有执行add操作 
$ git checkout -- file #仓库文件
#或
$ rm file #新文件
```
#####3.4.2 *`已添加到暂存区 撤销修改`*
```bash
$ git reset HEAD file
$ git checkout -- file
```
#####3.4.3 *`已添加到版本库 撤销修改`*
```bash
#撤销工作区自上次commit以后的所有修改
$ git reset --hard HEAD | commit_hash
$ git reset --hard HEAD^ | ^^ | ~100
```
`错误回退，再回到未来版本`
```bash
#查看提交记录 找到某次的提交commit_hash
$ git reflog
$ git reset --hard commit_hash
```
#####3.4.3 *`已推送到远程仓库 版本回退`*
```bash
#如果还没有其他人pull过,第一个方法比较简单就是会退到合适的版本,然后push到远程:
$ git reset --hard commit_hash
$ git push -f origin master
```
```bash
#上面那种方法相当于删除了后面的提交,就是把HEAD向后移动了,所以在和其他人合并代码的时候很容易冲突,所以用revert可能是更好的一种方法,它是基于你要恢复的版本创建了一个新的提交
$ git revert commit_hash
$ git push -f origin master
```
####3.5 git-flow
[参考](https://github.com/nvie/gitflow)


###4. 标签
>git的标签是版本库的快照，但其实它就是指向某个commit的指针，跟分支很像，但是分支可以移动，标签不能移动
```bash
$ git tag #查看已有标签
$ git tag v1.0 #创建
#给某次commit打标签
$ git tag v1.1 commit_hash 
#用-a指定标签名，-m指定说明文字
$ git tag -a v1.1 -m "version 1.1 released" commit_hash
$ git show v1.0 #查看

$ git tag -d v0.1 #删除标签
$ git push origin v1.0 #推送标签到远程
$ git push origin --tags #一次性推送全部尚未推送到远程的本地标签
#标签已经推送到远程，删除
$ git tag -d v1.1
$ git push origin :refs/tags/v1.1
```
###5. 关于.gitconfig 、.gitignore

#####5.1 所有 Git 配置都保存在你的` .gitconfig` 文件中

| 别名 Alias | 命令 Command | 设置  |
| - | --- | --- |
| `git cm` | `git commit` | `git config --global alias.cm commit` |
| `git co` | `git checkout` | `git config --global alias.co checkout` |
| `git ac` | `git add . -A` `git commit` | `git config --global alias.ac '!git add -A && git commit'` |
| `git st` | `git status -sb` | `git config --global alias.st 'status -sb'` |
| `git tags` | `git tag -l` | `git config --global alias.tags 'tag -l'` |
| `git branches` | `git branch -a` | `git config --global alias.branches 'branch -a'` |
| `git cleanup` | `git branch --merged | grep -v '*' | xargs git branch -d` | `git config --global alias.cleanup "!git branch --merged | grep -v '*' | xargs git branch -d"` |
| `git remotes` | `git remote -v` | `git config --global alias.remotes 'remote -v'` |
| `git lg` | `git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --` | `git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --"` |

为了在键入 comit 调用 commit命令，只需启用自动纠错功能：
```bash
$ git config --global help.autocorrect 1
```

#####5.2 .gitignore  [忽略文件模板](https://github.com/github/gitignore)
`忽略版本库的文件、文件夹`
```bash
#1.查看取消某个文件(夹)可能影响到的文件列表预览 -n这个参数表示预览
$ git rm -r -n --cached [Directory or File]

#2.将文件(夹)移除版本控制
git rm -r --cached [Directory or File]

#3.添加到.gitignore文件中,提交
$git commit -m "移除[Directory or File]的版本控制"

#4.推送到远程服务器
git push origin master
```


###6. 学习参考资料
- https://git-scm.com/book/zh/v2
- http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
