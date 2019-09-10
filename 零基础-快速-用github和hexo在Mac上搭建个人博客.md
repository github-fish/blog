---
title: '零基础,快速,用github和hexo在Mac上搭建个人博客'
date: 2019-08-13 20:39:28
tags: 个人博客
categories: 技术Tech
---
## 准备工作: 联网的Mac一台.

## 操作步骤

### 1. 注册github账号一个. 需要有一个邮箱, 即可免费注册. [Github](https://github.com/join?source=header)

### 2. 安装Git和Node.js. 如果电脑里已有, 可以跳过, 在terminal输入以下命令, 确认.

``` bash
$ git
$ node -v
```
如果没有出现git相关命令和Node版本信息, 说明该电脑里没有安装, 按照下述程序安装.

[Git GUI下载地址](https://sourceforge.net/projects/git-osx-installer/)和[Git安装指导文档](https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)

[Nodejs GUI下载地址](https://nodejs.org/en/)

### 3. Github账户建立新仓库和github.io域名.
登入github,右上角有个加号+, 点击选择New repository,创建新仓库.
新仓库的名字必须是:owner名.github.io. 比如我的GitHub账号名字是github-fish那么我的新建仓库必须叫做github-fish.github.io
选择Public,完成创建.

### 4. 安装hexo
``` bash
$ sudo npm install hexo-cli -g
```
Tips: 在Mac terminal 切换到admin user使用sudo commands的方法是
``` bash
$ su admin  # 使用你的admin账号名字, 我的就是admin.
```
然后会要求你输入password, 输入后enter, 就可以成功使用admin账户了. 

#### 用hexo初始化你的博客
``` bash
$ sudo hexo init username.github.io
```
#### 安装并修改一个主题
``` bash
$ cd username.github.io
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
找到username.github.io/_config.yml文件, 修改基础配置. 注意,配置项的:后面有空格.
title: github-fish    //博客的名字
author: Fish  //你的名字
language: zh-Hans    //语言 中文 , en 则是英文
deploy:
type: git    //Github发布
repo: https://github.com/username/username.github.io.git    // 刚创建的Github仓库链接也可以选择ssh

#### 写一篇文章 
博客的文章都会放在:username.github.io/source/_posts文件下.有两个方法, 第一个:
可以cd到username.github.io/source/_posts文件夹, 用你最喜欢的txt editor写你的第一篇博客. 比如:
``` bash
$ cd username.github.io/source/_posts
$ sudo nano MyFirstBlog.md
```
第二个: 直接运行以下命令
``` bash
$ sudo hexo new "MyFirstBlog" 
```
然后, 你就可以在username.github.io/source/_posts文件夹里发现一个MyFirstBlog.md文件. 打开显示如下:
---
title: MyFirstBlog
date: 2019-08-13 9:21:40       
tags: 博客标签
---
#### 发布前测试
``` bash
$ sudo hexo s
```
会出现提示, 一般都是浏览器中输入 https://localhost:4000 进行访问. 看好了效果后, 记得关闭, 方法是: Ctrl+C. 关闭成功显示Bye. Good Bye. Catch you later等等.
注意:如果忘记关闭, 下一次测试会报错, 解决办法有两个, 写在文章末端.  
此处默认一切正常, 进入下一步.

#### 安装hexo-deployer-git自动部署发布工具
``` bash
$ npm install hexo-deployer-git --save             
```

#### 发布
``` bash
$ sudo hexo clean //清除db.json缓存文件和已生成的静态文件public
$ sudo hexo g //生成缓存和静态文件
$ sudo hexo d //部署到服务器            
```
以上可以用一句命令行完成
``` bash
$ sudo hexo clean && hexo g && hexo d
```

#### Git config的问题
可能会遇到, 根据terminal提示完成就可. 一般是
$ git config --global user.name "github-fish" //我的github username是github-fish.
$ git config --global user.email github-fish@example.com //属于你github的登陆邮箱.

#### 登陆你的博客, 查看效果吧 github-fish.github.io //此处使用你自己新建的仓库名字即可. username.github.io

### 常见问题与解决办法

#### 1. 提示“FATAL Port 4000 has been used. Try other port instead.”
说明4000端口已经被用了. 方法一, 换个端口测试, 比如5000
``` bash
$ sudo hexo s -p 5000 
```
预览完毕后, 记得ctrl+c关闭. 不然5000也不能用了😂

方法二:
查看被占用的4000端口, 可以的话, kill占用进程, 释放它.
Mac下查看端口的进程占用命令:
``` bash
$ lsof -i:4000 //4000是端口名字 
```
结果出来后, 看到PID对应的那一列, 比如是1590. 这个PID 1590就是占用端口4000的进程号. kill它. 
``` bash
$ kill 1590  
```
现在, 端口4000就被释放了. 可以用来预览博客了. 
