# Ubuntu 15.04开发Ionic之环境配置

@(环境配置)[重点]

-------------------

[TOC]

## 环境配置
- **nodejs** ：Cordova安装依赖nodejs(略)
- **java sdk** ：Ionic build需要java环境；
- **android sdk** ：Ionic build需要android环境；
- **ionic**

###1.java sdk安装配置
>1.Installing default JRE/JDK
>```shell
>sudo apt-get update
>sudo apt-get install default-jre
>sudo apt-get install default-jdk
>```
>2.Installing Oracle JDK (选择安装版本)
>```shell
>sudo apt-get install python-software-properties
>sudo add-apt-repository ppa:webupd8team/java
>sudo apt-get update
>
>sudo apt-get install oracle-java6-installer #Oracle JDK 6(旧版本)
>sudo apt-get install oracle-java7-installer #Oracle JDK 7(较新的稳定版本) 建议选７
>sudo apt-get install oracle-java8-installer #Oracle JDK 8(最新预览版本)
>```
>3.配置
>```shell
>sudo update-alternatives --config java
>//出现　直接回车选择默认值
>* 0 /usr/lib/jvm/java-7-oracle/jre/bin/java 1072 自动模式
>1 /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java 1071 手动模式
>2 /usr/lib/jvm/java-7-oracle/jre/bin/java 1072 手动模式

>sudo update-alternatives --config javac //同上操作
>```
>4.配置 JAVA_HOME 环境
>```shell
>sudo update-alternatives --config java　//查看配置路径 path为"/usr/lib/jvm/java-7-oracle"
>sudo vim /etc/environment
>//在文件最后加上保存退出
>JAVA_HOME="/usr/lib/jvm/java-7-oracle"
>//最后是配置生效
>source /etc/environment
>java -version //查看是否配置成功
>```
###2.android sdk安装配置
>1.在/opt下打开终端　(需要[翻墙](http://igge.gq/2015/07/16/11/)下载且速度不快)
>```shell
>wget http://dl.google.com/android/android-sdk_r23.0.2-linux.tgz -O android-sdk.tgz
>```
>完成后
>```shell
>mv "android-sdk-linux" "android-sdk"
>chown root:root "android-sdk" -R
>chmod 777 "android-sdk" -R
>```
>2.打开Android SDK Manager(download the SDK platform tools)
>```shell
>./opt/android-skd/tools/android
>```
>默认下载列表中的Tools API(下载了一天，网络不给力的后果)
>![enter image description here](http://1102047360.rsc.cdn77.org/wp-content/uploads/2014/05/Android-SDK-Manager-_008.png)
>下载完成后　编辑 ~/.bashrc　加入Android SDK tools path
>```shell
>vim ~/.bashrc
>```
>在最后加入
>```shell
>export PATH=${PATH}:/opt/android-sdk/tools
>export PATH=${PATH}:/opt/android-sdk/platform-tools
>```
>创建一个Android Virtual Device
>```shell
>android avd
>```
>![enter image description here](http://1102047360.rsc.cdn77.org/wp-content/uploads/2014/05/Create-new-Android-Virtual-Device-AVD-_016.png) 
>![enter image description here](http://1102047360.rsc.cdn77.org/wp-content/uploads/2014/05/5554Tablet_020.png)
>```shell
>sudo vim /etc/environment
>//在文件最后加上保存退出
>ANDROID_HOME="/opt/android-sdk"
>source /etc/environment
>```
###3.ionic 

>intall cordova && ionic [参考文档](http://ionicframework.com/docs/guide/)
>```shell
>npm install -g cordova ionic
>```
>Create the project cd /home/ubuntu/
>```shell
>ionic start myApp tabs
>```
>Configure Platforms
>```shell
>ionic platform add android
>ionic build android
>```
>`build 过程中错误解决方法`
>```shell
>[Error: Please install Android target: "android-22".

>Hint: Open the SDK manager by running: /opt/android-sdk/tools/android
>You will require:
>1. "SDK Platform" for android-22
>2. "Android SDK Platform-tools (latest)
>3. "Android SDK Build-tools" (latest)]
>```
>**cd到myApp修改下面目录中的target=android-22为target=android-23**
>**原因为执行Android SDK Manager 下载tools时最新版本为22**
>- myApp/platforms/android/project.properties 
>- myApp/platforms/android/CordovaLib/project.properties
>- myApp/platforms/android/AndroidManifest.xml
>
>```html
><uses-sdk android:minSdkVersion="16" android:targetSdkVersion="23"/>
>```
>
>**bulid 完成后apk　生成目录**
>/home//ubuntu/myApp/platforms/android/build/outputs/apk/android-debug.apk

###参考链接
-  [**ionic guide**](http://ionicframework.com/docs/guide/)
-  [**java sdk install**](http://segmentfault.com/a/1190000001703180)
-  [**android sdk install**](http://www.unixmen.com/install-android-sdk-ubuntu-14-04/)

