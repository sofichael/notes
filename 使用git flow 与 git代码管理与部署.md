#使用git flow 与 git代码管理与部署

**git客户端安装使用不做介绍**
**git flow的安装与介绍 参考:[https://github.com/nvie/gitflow/wiki/Installation](https://github.com/nvie/gitflow/wiki/Installation)**

######1.git flow使用参考   [http://www.ituring.com.cn/article/56870](http://www.ituring.com.cn/article/56870)  重点理解几个分支作用 (所有开发人员约定，必须遵守的规则)

```js
主分支分为 master分支和 development 分支
	- master分支上存放的应该是随时可供在生产环境中部署的代码。同时，每一次更新，最好添加对应的版本号标签(TAG)
	- develop分支是保存当前最新开发成果的分支。通过了所有的测试后，并且代码已经足够稳定时，就可以将所有的开发成果合并回master分支了
```
```js
辅助分支为feature分支和release分支和hotfix分支
	用于开发新功能时所使用的feature分支
		[1]可以从develop分支发起feature分支
		[2]代码必须合并回develop分支(当开发完新功能时结束该分支时自动切回develop分支)
		[3]feature分支的命名可以使用除master，develop，release-*，hotfix-*之外的任何名称
	用于辅助版本发布的release分支
		[1]可以从develop分支派生
		[2]必须合并回develop分支和master分支
		[3]分支命名惯例：release-*
	用于修正生产代码中的缺陷的hotfix分支
		[1]可以从master分支派生
		[2]必须合并回master分支和develop分支
		[3]分支命名惯例：hotfix-*
```
######使用 git flow 例子: [http://www.cnblogs.com/wubaiqing/archive/2011/12/18/2271724.html](http://www.cnblogs.com/wubaiqing/archive/2011/12/18/2271724.html)

```js
eg:普通bug处理
/*开始创建新的feature需求分支,目的修改段代码罗辑结构*/
$ git flow feature start handlecodelogic /*这时项目会自动切换 feature/handlecodelogic分支*/
/*处理代码 更改部分代码后 ....*/
$ git commit -a -m "handlecodelogic done"
$ git flow feature finish editimage /*完成开发分支合并develop(自动)*/
$ git push origin develop /*发布到远程开发分支(也可以先merge到master分支再发布到远程分支)*/
```

```js
eg:紧急bug处理
$ git pull origin master /*(更新master分支 master分支为最新)*/
$ git checkout master /*切换到master分支*/
$ git flow hotfix start hotfix-fixbug /*生成一个hotfix分支 名字为hotfix-fixbug*/
/*通知相关得工程师和测试人员hotfix分支名称 hotfix-fixbug*/
$ git pull origin hotfix/hotfix-fixbug /*最终测试完成后拉回分支最新代码*/
/*最终修改和测试完成后，结束hotfix-fixbug 以供发布*/
$ git flow hotfix finish hotfix-fixbug
$ git push origin master /*发布最终的master分支*/
```


######2.git服务端的搭建注意事项 参考:[http://www.liaoxuefeng.com](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)

```js
$ sudo git init --bare gitflow.git  /*初始一个bare仓库服务器端*/
$ sudo chown -R git:git gitflow.git /*把ower改为git(可以是项目相关名称)*/
/*post-receive文件位置在 gitflow.git/hooks/ 文件夹下是bare远程仓库，不是在 .git/hooks/下 (一般为新建文件，该文件下有其他以.sample结尾的文件)*/
$ sudo chown +x post-receive /*对post-receive做权限修改 (可执行，并且修改为git用户权限)*/

```
######post-receive内容

	修改/home/wwwflowtest用户权限为git，hooks下script有权限操作

```js
#!/bin/sh
# This file is to be used by git repo on server
# It should be a file called hooks/post-receive in the bare git repo
# x表示是打印每一个运行的命令；e表示有任何错误就退出

        #set -xe
        #set -e
        time=`date`
        #判断是不是远端仓库
        IS_BARE=$(git rev-parse --is-bare-repository)
        if [ -z "$IS_BARE" ]; then
            echo >&2 "==========tips=========="
            echo >&2 "fatal: post-receive: IS_NOT_BARE"
            exit 1
        fi

        #判断是否要部署代码
        #SUBJECT=$(git log -1 --pretty=format:"%s")
        #IS_DEPLOY=$(echo "$SUBJECT" | grep "deploy")
        IS_DEPLOY=$(git log -1 --pretty=format:"%s" | grep "\[deploy\]")
        if [ -z "$IS_DEPLOY" ]  ; then
            echo >&2  "==========tips=========="
            echo >&2  "not deploy code to production"
            exit 1
        fi
        unset GIT_DIR
        DeployPath="/home/wwwflowtest"
        echo "==============================================="
        cd $DeployPath
        echo "deploying the gitflow test"
        #git pull origin master
        git fetch --all
        git reset --hard origin/master
        echo "web server pull master code at time: $time."
        echo "================================================"
```