---
title: 'Xcode 10 中关于 Error: Multiple commands produce'
date: 2021-02-17 00:45:43
tags: [ios,react-native]
published: true
hideInList: false
feature: 
isTop: false
---
本文摘自[简书](https://www.jianshu.com/p/fdb1421f3c8b)

更新`Xcode 10`，自然开始第一时间运行老项目，踩坑填坑

### 这边记录下基本出现的问题

> `Error: Multiple commands produce`

Xcode 10 与本问题相关的一个更新点。

>Build System
Again, Xcode 10 uses a new build system. The new build system provides improved reliability and build performance, and it catches project configuration problems that the legacy build system does not.
The legacy build system is still available in Xcode 10. To use the legacy build system, select it in the File > Project/Workspace Settings sheet. Projects configured to use the legacy build system will display an orange hammer icon in the Activity View.

简单而言，`Xcode10`使用了一个的新创建系统，比之前的提供更好的可靠性与创建性能，而且可以获取项目配置问题（默认设置新创建系统）

在苹果文档中，提及`Xcode10`中的关于旧项目`New Build System`更改适配中提及到以下两点

* The new build system has stricter checks for cycles between elements in the build in order to prevent unnecessary rebuilds.

* It is an error for any individual file in the build to be produced by more than one build command. For example, if two targets each declare the same output file from a shell script phase, factor out the declaration of the output file into a single target.

`New Build System`会对构建中的元素循环进行严格的检查，避免不必要的重建，这个也是错误出现的原因。

### 错误发生的两种普遍形式：

#### 1. `info.plist`

> 错误信息

```
Multiple commands produce '/Users/tjsoft/Library/Developer/Xcode/DerivedData/TYKYTwoLearnOneDo-amcfzswumxdotlgjwdslugmywxch/Build/Products/Debug-iphoneos/TYKYTwoLearnOneDo.app/Info.plist':
1) Target 'TYKYTwoLearnOneDo' (project 'TYKYTwoLearnOneDo') has copy command from '/Users/tjsoft/TYKY /党建/黔纪/QianJiDangJian_iOS/TYKYTwoLearnOneDo/Info.plist' to '/Users/tjsoft/Library/Developer/Xcode/DerivedData/TYKYTwoLearnOneDo-amcfzswumxdotlgjwdslugmywxch/Build/Products/Debug-iphoneos/TYKYTwoLearnOneDo.app/Info.plist'
2) Target 'TYKYTwoLearnOneDo' (project 'TYKYTwoLearnOneDo') has process command with output '/Users/tjsoft/Library/Developer/Xcode/DerivedData/TYKYTwoLearnOneDo-amcfzswumxdotlgjwdslugmywxch/Build/Products/Debug-iphoneos/TYKYTwoLearnOneDo.app/Info.plist'
```

#### 2. `Copy Pods Resources`

> 错误信息

```
Multiple commands produce '/Users/tjsoft/Library/Developer/Xcode/DerivedData/TYKYTwoLearnOneDo-amcfzswumxdotlgjwdslugmywxch/Build/Products/Debug-iphoneos/TYKYTwoLearnOneDo.app':
1) Target 'TYKYTwoLearnOneDo' has create directory command with output '/Users/tjsoft/Library/Developer/Xcode/DerivedData/TYKYTwoLearnOneDo-amcfzswumxdotlgjwdslugmywxch/Build/Products/Debug-iphoneos/TYKYTwoLearnOneDo.app'
2) That command depends on command in Target 'TYKYTwoLearnOneDo': script phase “[CP] Copy Pods Resources”
```


### 解决方法可以有两种:

#### 1. 一种是不使用`New Build System`，在`File` > `Project/Workspace Settings`中的`Share Project/Workspace Settings `里`build system `将`New Build System(Default)`切换成`Legacy build system`。

![](https://panyu97py.github.io/post-images/1613494079991.png)

#### 其次是根据出错信息，在新创建系统模式下，去除多余的引用重建。

##### 1. Info.plist

在 `target` -> `Build phase` > `Copy Bundle Resource` 中找到`info.plist`，移除

![](https://panyu97py.github.io/post-images/1613494034862.png)


##### 2. Copy Pods Resources

在 `target` -> `Build phase` -> `Copy Pods Resources` -> `Output Files`，移除`${TARGET_BUILD_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}`

![](https://panyu97py.github.io/post-images/1613494090242.png)
