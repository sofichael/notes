#!/bin/sh
# This file is to be used by git repo on server
# It should be a file called hooks/post-receive in the bare git repo

# x表示是打印每一个运行的命令；e表示有任何错误就退出
#set e

time=`date`

#判断是不是远端仓库
IS_BARE=$(git rev-parse --is-bare-repository)
if [ -z "$IS_BARE" ]; then
    echo >&2 "fatal: post-receive: IS_NOT_BARE"
    exit 1
fi

#判断是否是要部署代码
SUBJECT=$(git log -l --pretty=format:"%s")
IS_PULL=$(echo "$SUBJECT" | grep "\[deploy\]")
if [ -z "$IS_PULL"]  ; then
    echo  "tips:push msg is not deploy"
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