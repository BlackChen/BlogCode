---
title: GitHub Actions自动部署Hexo博客小结
date: 2020-12-14 11:39:39
tags: 
    - Hexo
    - CICD
    - Next
    - Travis CI
    - GitHub Actions 
---
## 一、关于CICD
传送门:[什么是CI/CD/CD](https://blog.csdn.net/weixin_44903147/article/details/96291588)

## 二、Hexo  
### 2.1 博客搭建 
- [Hexo官方文档](https://hexo.io/zh-cn/docs/)
- [搭建总结](https://segmentfault.com/a/1190000017986794)

### 2.2 博客主题 
- [NexT 主题配置 官方](http://theme-next.iissnan.com/theme-settings.html)
- [Hexo-Next 主题博客个性化配置](https://blog.csdn.net/as480133937/article/details/100138838/)

## 三、自动部署
### 3.1 GitHub Actions部署博客 
[使用GitHub Actions自动部署](https://blog.csdn.net/xinruodingshui/article/details/105499161)
注意:Deploy key添加到个人或者博客源码的 settings 里面都可以

### 3.2 Travis CI部署博客
- [Hexo＋Travis-ci＋Github构建自动化博客](https://abelyang.blog.csdn.net/article/details/53574002?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-8.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-8.control)
- [GitHub Token + Travis CI 自动部署项目应用](https://anran758.github.io/blog/2020/06/08/github-travis-build/)
- [GitHub SSH + Travis CI 自动部署项目应用](https://segmentfault.com/a/1190000004667156)

## 四、使用中遇到的问题
### 4.1 Hexo 使用 Github Actions 自动发布至 Github Pages 时,生成的 html 页面空白,没有css/js等文件
- 原因: 由于主题是 git clone 下来的,主题目录下生成了 .git 目录,导致和 hexo 根目录下 .git 冲突了,commit 时没有把主题 push 上去导致的。
部署的博客发布

- 解决: 删除本地主题中的.git文件夹,上传到远程仓库
   
### 4.2 解决hexo博客网页无法使用图片问题
```
npm install hexo-asset-image --save	# 安装hexo插件
```
```
# 这里还要注意修改_config.yml中的一个点
……
post_asset_folder: true
……
```
```
hexo new test		# 生成新的网页
```
位置在 test/source/_posts下，会同时生成test目录和test.md 网页，将需要的图片放置到test目录下，test.md就可以正常解析了，注意：要在图片路径直接写成同目录下即可。
![image](https://img2018.cnblogs.com/blog/1641240/201907/1641240-20190727161318355-1195792976.png)

### 4.3 解决hexo博客乱码
[好不容易解决无法访问的问题，结果打开网站是乱码？？？](https://www.dazhuanlan.com/2020/01/29/5e315f744541e/)

### 4.4 hexo部署后，CNAME会被自动删除
解决: [将需要上传至github的内容放在source文件夹，例如CNAME、favicon.ico、images等](https://www.zhihu.com/question/28814437?sort=created)


参考文档：
- [使用 GitHub Actions 实现 Hexo 博客自动部署](https://www.cnblogs.com/deppwang/p/12326906.html)
- [gitee+hexo搭建个人博客](https://www.cnblogs.com/somata/p/11255547.html)
- [Hexo 使用 Github Actions 自动发布至 Github Pages 时，生成的 html 页面空白](https://www.v2ex.com/t/728048)