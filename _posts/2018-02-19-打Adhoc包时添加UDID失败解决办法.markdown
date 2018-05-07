---
layout: post
title:  "打Adhoc包时添加UDID失败解决办法"
date:   2018-02-19 下午7:01
categories: jekyll update
---

平时我们都是打包好传到蒲公英上给测试测，今天有同事说要让我再加几个设备进来，但是还是安装不上，在开发者中心看到的是有75个设备，但是包传到蒲公英上还是只有71个。如下图
![](https://upload-images.jianshu.io/upload_images/548341-4258ab479340d3f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可见新增的设备并没有加进去。后来有重新生成了新的 mobileprovision，但是还是有问题，可见新生成的mobileprovision文件也并没有安装成功。
后来想到是否可以把本地的mobileprovision 都删除，然后重新安装应该可以了吧，然后就试了试，果然成功了，步骤如下：
### 首先，到 mobileprovision 目录下面，清空

```
cd ~/Library/MobileDevice/Provisioning\ Profiles
```

![](https://upload-images.jianshu.io/upload_images/548341-3f5a972ffa4fb35c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到本地已经装了很多mobileprovision，所以可能是这些文件太多了互相影响吧，直接都删除掉

```
rm *.mobileprovision
```

### 添加 mobileprovision
然后重新去下载Profile就可以了
![](https://upload-images.jianshu.io/upload_images/548341-8d815d7d4d547f2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


最后打出的包就看得到新加的设备了，还不行就重启电脑试试了。

![](https://upload-images.jianshu.io/upload_images/548341-c00cf44c58297873.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
