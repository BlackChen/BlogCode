---
layout: hello
title: Xcode 使用汇总
date: 2020-12-11 15:34:59
tags: Xcode
top: 1
---

##功能使用
- 1、[Xcode 无线真机调试](https://mp.weixin.qq.com/s/V9ZY62r_4xe7GLiwnEA76Q)

- 2、[Xcode Server持续集成，持续部署](https://www.jianshu.com/p/167fb1dbe489)

- 3、Xcode真机/模拟器调试启动页卡住，真机一两分钟，模拟器十多秒
Edit Scheme --> Run、Test --> Info --> Executable，Test看个人需求
![AAF60D1A8607849E5E18E7C93E854B8B.jpg](https://upload-images.jianshu.io/upload_images/1603601-e8f5f2b0c3c222e5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
去掉图中选项。
`* 但这样的后果是你的断点没得效果啦，哈哈。慎用！！！* `
辅助选项：[Xcode12真机/模拟器运行项目非常慢的解决方式](https://www.jianshu.com/p/0cce3ae0c2ec)

## 问题解决
- [Xcode12打开旧工程编译失败](https://www.jianshu.com/p/d54fd467c540)