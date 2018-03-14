---
layout: post
title:  "使用fastlane一键打包iOS项目并上传到蒲公英平台"
date:   2017-12-10 下午8:41
categories: jekyll update
---

平时打包一般过程是先在xcode上打好ipa包，然后上传到蒲公英上，最后把二维码发到群里给大家下载，这个过程都需要有人在旁边等着，一步一步点，比较麻烦。这两天试验了一下，可以通过fastlane，直接在终端中敲一个命令就可以把包自动发布到蒲公英上了，发布成功后微信上就收到推送消息。本文介绍下这个过程。
## 1. 安装fastlane准备工作
fastlane是ruby写的，这里安装的前提是你的ruby环境是2.0版本以上。然后还要安装好xcode-select，也就是xcode的命令行工具，因为fastlane的脚本里面都是通过命令来打包签名项目的。


### 安装xcode-select命令如下

```
xcode-select --install
```
如果没有安装的话会弹出一个对话框提示你安装，如果已经安装的话会显示如下信息：

```
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ xcode-select --install
xcode-select: error: command line tools are already installed, use "Software Update" to install updates
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ 
```


### 安装ruby
可以参考这篇文章
[MAC机中安装RUBY环境](http://www.cnblogs.com/foxting/p/4520829.html)

```
注意这里查看使用的ruby版本命令是
ruby -v
而不是
rvm -v
以下是我的环境：
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ rvm -v
rvm 1.29.0 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io/]
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ ruby -v
ruby 2.4.0p0 (2016-12-24 revision 57164) [x86_64-darwin16]
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ 
```


### 关于cocoapods多说几句
这里使用了新的ruby环境以后，在新的环境上可能还要重新装pod，关于pod也有一些要注意的。安装cocoapods之前最好把之前的cocoapods卸载干净，先查看下已经安装的cocoapods组件，使用命令gem list，如下图

<center>
<img src="http://guohongwei719.github.io/images/20170505/1.png"/>
</center>

卸载如下图，后面写要卸载的组件名，不包括括号中的版本号哦

<center>
<img src="http://guohongwei719.github.io/images/20170505/2.png"/>
</center>

指定版本安装命令：

```
➜  ~ sudo gem install cocoapods --version 1.0.1
```

下图可以看出安装到的路径

<center>
<img src="http://guohongwei719.github.io/images/20170505/3.png"/>
</center>



## 2. 安装fastlane

```
命令如下
sudo gem install fastlane --verbose
如果报错的话
ERROR: While executing gem ... (Errno::EPERM) Operation not permitted - /usr/bin/commander
则使用如下命令
sudo gem install -n /usr/local/bin fastlane
```
可能持续的时间比较长，安装结束后可以查看版本，和安装路径

```
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ fastlane -v
fastlane installation at path:
/Users/guohongwei719/.rvm/gems/ruby-2.4.0@global/gems/fastlane-2.52.0/bin/fastlane
-----------------------------
fastlane 2.52.0
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ 
```
安装好fastlane以后，我们到项目根目录下面执行

`
fastlane init
`

这个感觉就跟git很像了。然后会输出很多信息，这些信息我写在后面了。有几个地方会需要你填一下，比如appId和密码之类，如下

```
➜  testFastlane fastlane init
[10:42:16]: For more information, check out https://docs.fastlane.tools/getting-started/ios/setup/#use-a-gemfile
[10:42:18]: Detected iOS/Mac project in current directory...
[10:42:18]: This setup will help you get up and running in no time.
[10:42:18]: fastlane will check what tools you're already using and set up
[10:42:18]: the tool automatically for you. Have fun! 
[10:42:18]: Created new folder './fastlane'.
[10:42:18]: $ xcodebuild -list -workspace ./zyzz.xcworkspace
[10:42:20]: $ xcodebuild -showBuildSettings -workspace ./zyzz.xcworkspace -scheme zyzz
[10:42:22]: Your Apple ID (e.g. fastlane@krausefx.com): admin@elab-plus.com
[10:43:17]: Verifying that app is available on the Apple Developer Portal and iTunes Connect...
[10:43:17]: Starting login with user 'admin@elab-plus.com'
-------------------------------------------------------------------------------------
Please provide your Apple Developer Program account credentials
The login information you enter will be stored in your macOS Keychain
You can also pass the password using the `FASTLANE_PASSWORD` environment variable
More information about it on GitHub: https://github.com/fastlane/fastlane/tree/master/credentials_manager
-------------------------------------------------------------------------------------
Password (for admin@elab-plus.com): ***********

+----------------+--------------------------------------+
|                    Detected Values                    |
+----------------+--------------------------------------+
| Apple ID       | admin@elab-plus.com                  |
| App Name       | zyzz                                 |
| App Identifier | com.elab.skyforest.chongqing         |
| Workspace      | /Users/guohongwei719/Desktop/testFa  |
|                | stlane/zyzz.xcworkspace              |
+----------------+--------------------------------------+

[10:44:03]: Please confirm the above values (y/n)
```
fastlane init执行完以后会在项目根目录下生成一个名叫fastlane的文件夹，如下图：


<center>
<img src="http://guohongwei719.github.io/images/20170505/4.png"/>
</center>



##3. 编写脚本
我们的脚本都写在Fastfile文件中，现在直接把脚本代码贴出来吧

```
# Customise this file, documentation can be found here:
# https://github.com/fastlane/fastlane/tree/master/fastlane/docs
# All available actions: https://docs.fastlane.tools/actions
# can also be listed using the `fastlane actions` command

# Change the syntax highlighting to Ruby
# All lines starting with a # are ignored when running `fastlane`

# If you want to automatically update fastlane if a new version is available:
# update_fastlane

# This is the minimum version number required.
# Update this, if you use features of a newer version
fastlane_version "2.52.0"

# 用到的
PROJECT_FILE_PATH = './zyzz.xcodeproj'
APP_NAME = 'skyforest'
SCHEME_NAME = 'zyzz'
IPA_PATH = '../build/'
PLIST_FILE_PATH = 'zyzz/Info.plist'

# 下面几个函数打包用到了
def set_info_plist_value(path,key,value)
    sh "/usr/libexec/PlistBuddy -c \"set :#{key} #{value}\" #{path}"
end
def set_channel_id(channelId)
    set_info_plist_value(
        ".././#{PLIST_FILE_PATH}",
        'ChannelID',
        "#{channelId}"
    )
end
def prepare_version(options)
    #say 'version number:'
    #say options[:version]
    increment_version_number(
        version_number: options[:version],
        xcodeproj: PROJECT_FILE_PATH,
    )
    #say 'build number:'
    #say options[:build]
    increment_build_number(
        build_number: options[:build],
        xcodeproj: PROJECT_FILE_PATH,
    )
end
def download_provision(typePrefix,isAdHoc)
    # We manual download the provision
    return
end
def generate_ipa(typePrefix,options)
    #say 'generate ipa'
    fullVersion = options[:version] + '.' + options[:build]
    channelId = options[:channel_id]
    #指定打包所使用的输出方式，目前支持app-store, package, ad-hoc, enterprise, development, 和developer-id，即xcodebuild的method参数
    if typePrefix == 'AdHoc'
      gym(
        scheme: "#{SCHEME_NAME}",
        workspace: './zyzz.xcworkspace',
        export_method: 'ad-hoc', 
        output_directory: './build/'
        )
    elsif typePrefix == 'InHouse'
      gym(
        scheme: "#{SCHEME_NAME}",
        workspace: './zyzz.xcworkspace',
        export_method: 'enterprise', 
        output_directory: './build/'
        )
    end
    #sh "mv .././build/#{APP_NAME}.app.dSYM.zip .././build/#{APP_NAME}_#{fullVersion}_#{typePrefix}.app.dSYM.zip"
end
# 下面几个函数打包完成后用到了
def rename_ipa()
    sh "mv '../build/#{SCHEME_NAME}.ipa' '../build/#{APP_NAME}.ipa'"
end
def upload_pgy()
  say 'upload pgy'
  # 下面要使用到蒲公英上传命令，自己去蒲公英网站查询
  sh "curl -F 'file=@../build/#{APP_NAME}.ipa' -F 'uKey=My_uKey' -F '_api_key=My_api_key' http://www.pgyer.com/apiv1/app/upload"
  # sh "~/Desktop/qrsctl login #{QINIU_NAME} #{QINIU_PWD}"
  # sh "~/Desktop/qrsctl cdn/refresh https://oh6p40stc.qnssl.com/zyzz.ipa"
  # sh "~/Desktop/qrsctl put -c elab #{SCHEME_NAME}.ipa ../build/#{SCHEME_NAME}.ipa"
end

def clean_derivedData()
  sh "rm -fr #{IPA_PATH}*"
end

default_platform :ios
platform :ios do
  before_all do
    # ENV["SLACK_URL"] = "https://hooks.slack.com/services/..."
    clean_derivedData()
    # pod_install()
    # git()
    APP_NAME =  "#{SCHEME_NAME}_#{Time.new.to_i}"    
  end

  desc "AdHoc"
  lane:AdHoc do |options|
    typePrefix = 'AdHoc'
    set_channel_id(typePrefix)
    prepare_version(options)
    download_provision(typePrefix,true)
    # update_app_identifier("#{OTHER_IDENTIFIER}")
    # update_provision(typePrefix)
    generate_ipa(typePrefix,options)
  end

  # You can define as many lanes as you want

  after_all do |lane|
    rename_ipa()
    upload_pgy()
    clean_derivedData()

    # This block is called, only if the executed lane was successful

    # slack(
    #   message: "Successfully deployed new App Update."
    # )
  end

  error do |lane, exception|
    # slack(
    #   message: exception.message,
    #   success: false
    # )
  end
end

 # More information about multiple platforms in fastlane: https://github.com/fastlane/fastlane/blob/master/fastlane/docs/Platforms.md
 # All available actions: https://docs.fastlane.tools/actions

 # fastlane reports which actions are used. No personal data is recorded. 
 # Learn more at https://github.com/fastlane/fastlane#metrics

```


这个脚本中有一些注意的地方如下：
1.  在after_all里面会执行upload_pgy()，这个方法里面会使用蒲公英提供的命令来上传包，蒲公英的命令可以参考这里获取[蒲公英上传命令](https://www.pgyer.com/doc/view/upload_one_command)；
2. 这里面从platform :ios do这里开始执行，流程简化如下

```
# 打包前
clean_derivedData()
APP_NAME =  "#{SCHEME_NAME}_#{Time.new.to_i}" 

# 打包中
typePrefix = 'AdHoc'
set_channel_id(typePrefix)
prepare_version(options)
download_provision(typePrefix,true)
generate_ipa(typePrefix,options)

# 打包后
rename_ipa()
upload_pgy()
clean_derivedData()
```
3 后面打包我们只需要执行命令fastlane AdHoc version:1.3.3 build:200即可，也可以用shell脚本再封装一层，这样只需要执行sh build.sh 1.3.3 200即可，是不是感觉更简单一点呢！shell脚本如下，此脚本命名为build.sh

```
#!/bin/sh

#
# usage:
# > sh build.sh 1.0.0 100
#

versionNumber=$1
buildNumber=$2

#basicLanes="AdHoc AppStore Develop InHouse"
basicLanes="AdHoc"
for laneName in $basicLanes
do
    fastlane $laneName version:$versionNumber build:$buildNumber
done
```


## 4. 开始打包
脚本准备好以后可以回到项目根目录下直接执行命令开始打包了

```
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ fastlane AdHoc version:1.3.3 build:200
```
如果用shell脚本包过一层，则命令如下

```
➜  sky-forest-ios git:(zyzz_1.3.3) ✗ sh build.sh 1.3.3 200
```
打包过程中注意：
1. 可能会出错，说fastlane版本太低，这时候需要升级版本，根据提示命令升级即可；
2. 能打包并上传成功的前提是项目本身也是可以运行的；
3. 我的版本是2.52.0，项目里面还要在配置好Build Settings里面的Versioning，不光是要配置好General里面的Version和Build号，如下

<center>
<img src="http://guohongwei719.github.io/images/20170505/5.png"/>
</center>最后就可以去吃饭了，打包好后可以收到蒲公英的微信推送：

<center>
<img src="http://guohongwei719.github.io/images/20170505/6.png"/>
</center>

