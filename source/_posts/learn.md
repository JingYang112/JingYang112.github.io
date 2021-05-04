---
title: hexo部署到github上
categories: 前端
tags:
  - 前端
  - hexo
summary: hexo部署到github上步骤
author: yangjing
password: yangjing112.halo
---

1. 在github上创建同名仓库username.github.io，我的username是JingYang112

   ![image-20210503115120769](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210503115120769.png)

2. 设置仓库地址

```yaml
deploy:
  type: git
  repo: git@github.com:JingYang112/JingYang112.github.io.git
  baranch: master
```

3. 通过cmd命令行进入blog目录，输入npm install hexo-deployer-git –save，并回车，安装相关的部署功能包。
4. hexo g,预览静态文件

3. 输入hexo d，将本地的博客文件发布到GitHub中

   

``` **命令****描述** hexo s 本地启动服务器 hexo g 部署之前预先生成静态文件 hexo deploy 文件生成后立即部署网站 hexo clean 清除缓存文件 和已生成的静态文件。```



> ==错误总结==:

* git更新远程库，提示"Everything up-to-date"

将文件添加到仓库

```bash
git add *
```

将文件提交到仓库

```bash
git commit -m '你对修改的描述'
```

然后我们将内容推送到远程库就没有问题了。

```
git push origin master
```

* git clone出现错误 fatal: unable to access ‘https://github.com/...‘的解决办法

解决方法：
1.查看git配置

```bash
git config --global -l
```

2.检查环境变量

```bash
env|grep -i proxy  
```

# git常用命令

* git init ,将项目交给git托管

* ![image-20210503120419466](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210503120419466.png)

* 光联远程仓库 

  ```
  git remote add origin 仓库地址
  ```

* 提交本地仓库所有文件

```
git push -u origin master
```

删除仓库关联

```
git remote rm origin
```

修改后提交

```
git add .
git commit -m "xxx"
git push -u origin master
```