# Note_多渠道打包发布



--多渠道打包发布--


1：	合并review好的代码到master分支上，切换到master分支上
 
	buildConfigField "boolean", "WIFI_SLEEP_WAKEUP", "true"
        buildConfigField "boolean", "WIFI_SPOTIFY", "true"
	resConfigs "en", "de", "es", "fr", "zh-rCN", "zh-rTW", "ja-rJP"

	注意以上值得设定

2：	./gradlew clean

3.1：	./gradlew assembleDebug 内部测试版本

3.2：	./gradlew assembleRelease  打包所有正式版本  

3.2：	./gradlew assembleXiaomiRelease 打包某个渠道的正式版本 

	adb uninstall com.libratone
	adb install E:/Libratone/libratone_android/app/build/outputs/apk/app-website-release-2.6.3-for-test.apk


4.	把编译好的包，放到fir上  （apk+releasenote）    （测试版：websit_debug,正式版：websit_release）

5.	把所有编译好的包，放到http://192.168.20.251/android/ 上。
	
	1.cd e/xxxooo/xxxooo/libratone_android/app/build/outputs
	2.把打包好的文件夹拷贝到本地服务器上面去  scp -r ./apk sw2@192.168.20.251:/var/www/html/android/2.6.4/
	3.gitbash (linux)  登入ssh sw2@192.168.20.251 上，密码：2222；

		1.查看是否已经有了ssh密钥：cd ~/.ssh
		  如果没有密钥则不会有此文件夹，有则备份删除
		2.生存密钥：
		  $ ssh-keygen -t rsa -C “haiyan.xu.vip@gmail.com”
		  按3个回车，密码为空。

	4.cd/ 切到根目录   ls 通过目录找到 cd var/www/html/android 文件夹，文件夹里面就是所有历史版本文件夹；clear 清屏
	5.rm -rf 2.6.3   删除某个文件夹   pwd 当前所在文件位置  ctrl + d 退出 远程服务器

6.	编辑发版本的邮件

说明：内部版本只需要编一个版本：app-website-debug-2.6.2.apk
     外部版本变成多个版本(百度，360需要单独编译，360和腾讯版本需要做加固才能上传应用商店）   


建立编译环境(以git bash为例)

  release版本
  1）拷贝签名文件到自己的工程目录libratone_android(和自己的实际目录有关，例如我的目录是：E:\gitrelease\AndroidApp\libratone_android)
    libratone_v2.keystore

  2）添加环境变量：ANDROID_STORE_PASS=2DWgJNr2dRKVLPZE，在系统设置里面添加，注意不要流传该密码。

  版本修改：
  E:\gitrelease\AndroidApp\libratone_android\app\build.gradle中：
     versionCode 133
     versionName "1.1.1"
  按照版本依次递增

  开始编译：
  1）git bash命令行下，执行：
     ./gradlew clean  清楚之前的编译结果
     ./gradlew assembleDebug: 编译debug版本，Website版本会编译出来
     或者:
     ./gradlew assembleRelease 编译所有release版本
     ./gradlew assembleXiaomiRelease编译某个版本出来。
  2）结果生成在：E:\gitrelease\AndroidApp\libratone_android\app\build\outputs\apk


可能的问题以及解决：
报告找不到sdk：
 （1）添加: ANDROID_HOME 路径：E:\soft\android\sdk6\sdk
报告签名错误：
 （2）手动添加：export ANDROID_STORE_PASS=2DWgJNr2dRKVLPZE
      在windows下添加环境变量也可以，不用每次都要设置了。
     
       
  发布版本：
   1） 提交到Fir供测试组使用，把版本：app-website-release.apk 提交到fir，同时提供升级提示
        http://fir.im/apps/55c970eb748aac7731000032
       （登录账号：fir账号：    fir.im@libratone.com.cn    密码：YR90pMrA9yA2）

   2）360和百度版本正式版本比较特殊，需要单独编译
      360和百度不能共存，需要去掉彼此的库，修改代码后才能生成apk。

      编译360版本，取分支nobaiduupdate, 主要修改：去除百度库（BDAutoUpdateSDK_20150605_V1.2.0.jar），去除百度有关文件（androidmanifest.xml，SpeakerSoundSpaceActivity.java)
      编译百度版本，取分支no360,        主要修改：去除360库（UpdateLib_2.0.8.jar），去除360有关文件（androidmanifest.xml，SpeakerSoundSpaceActivity.java)

      具体操作是：
      （1）下载nobaiduupdate分支，取代码，和master代码合并，解决冲突，编译
            git 命令：
            git branch -a: 查看远端分支
            git checkout nobaiduupdate
            git pull
            git merge master
            解决冲突，编译步骤省略

      （1）下载no360分支，取代码，和master代码合并，解决冲突，编译
            git 命令：
            git branch -a: 查看远端分支
            git checkout no360
            git pull
            git merge master
            解决冲突，编译步骤省略

       说明：合并后的代码，暂时不要推到服务器，保留在本地就可以。

  3）编译出来的360版本，和腾讯的版本，如果需要上传，需要做加固处理，本地都有客户端可以加固
     账号：562222
 密码：W3UsEr7663
     签名密码：2DWgJNr2dRKVLPZE

          
  4）编译出来的版本，需要上传到内部备份服务器上保存：
     版本查看网页：http://192.168.20.251/android/

     apk复制到服务器的命令如下：（192.168.20.251是存放release版本的服务器）
        例如命令：
          scp -r ./XXX sw2@192.168.20.251:/var/www/html/android/XXX

        说明：（1）XXX是当前保存生成apk的本地路径
              （2）注意是：/var/www/html/android/XXX/
               (3)可以登录进入：
                     ssh sw2@192.168.20.251，
                     系统默认进入自己的home目录，可以切换到/var/www/html/android，管理上传的文件等。

  5）写邮件，发布版本说明，
       其中提供：
       （1）测试人员（内部人员访问）：http://fir.im/3gj4
       （2）王亮会访问：http://192.168.20.251/android/ 取得版本，上传到各个国内app store
       （3）google play版本由专人上传。目前账号持有人为rebecca, rick, vincent,auther.
       （4）版本主要改动，需要说明注意的事项
       （5）外发版本需要抄送给王亮。

说明：

1）当前语言支持5种语言，是在build.gradle 里面说明的：
  resConfigs "en", "de", "es", "fr", "zh-rCN", "zh-rTW"
将来会加入日文和韩文，需要修改这里

2）内部版本发版本的时候不需要全部编译，可以删除build.gradle里面除去website {}的其他apk，只要编译出website版本就可以
      productFlavors {
        all {
            flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name]
        }
        website {}
        store360 {}
        baidu {}
        wandoujia {}
        qq {}
        huawei {}
        xiaomi {}
        meizu {}
        lenovo {}
        googleplay {}
    }
3）常用gradlew命令：
./gradlew -v 版本号
./gradlew clean 清除项目app目录下的build文件夹
./gradlew build 检查依赖并编译打包
./gradlew assembleDebug 编译并打Debug包
./gradlew assembleRelease 编译并打Release的包
./gradlew tasks --all 查看所有的任务：

./gradlew assembleDebug --profle 生成报告，检查是否某些模块编译时间过长
报告生成路径：
file:///E:/gitrelease/AndroidApp/libratone_android/build/reports/profile/profile-2016-07-05-09-54-39.html


 
Android studio Gradle 构建说明
http://www.cnblogs.com/Bonker/p/5619458.html
GRADLE构建最佳实践
http://www.figotan.org/2016/04/01/gradle-on-android-best-practise/
深入理解Android之Gradle
http://blog.csdn.net/Innost/article/details/48228651
