---
title: CocoaPods使用过程中遇到的坑
date: 2016-09-12 16:17:23
tags:
---
## 一.安装

## 二.使用

### 1.Setting up CocoaPods master repo 卡着不动
  那是因为 淘宝的那个镜像(https://ruby.taobao.org/ )已经不可用了。所以我们现在用最新支持的ruby镜像(https://gems.ruby-china.org/)
  需要的命令行：

  $ gem sources -r https://rubygems.org/ （移除旧版本的镜像，如果你不知道你电脑上目前用的是什么镜像，可用  $ gem sources -l  来查看）
  $ gem sources -a https://gems.ruby-china.org/ （增加可用的镜像）
  $ gem sources -l  （用来检查使用替换镜像位置成功）

  当pod setup 进入Setting up CocoaPods master repo 等待的时候表示正在下载了，此时你可通过新开一个终端窗口，输入"cd ~/.cocoapods/"命令行跳到cocoapods文件夹内，执行"du -sh *" 查看正在下载的文件夹的大小*
### 2.[!] An error occurred while performing `git pull` on repo `master`. 和[!] /usr/bin/git pull --ff-only
  这个错误应该是说这个节点有问题。就又找资料，然后试试把cocoapods的节点删除，重新添加一个节点，或许就行，说干就干：
  原因： Cocoapods的分支不支持当前最新的Xcode版本

  解决办法: 删除master分支 重新建立新的分支

  sudo rm -fr ~/.cocoapods/repos/master

  然后再: pod setup

## 三.卸载
