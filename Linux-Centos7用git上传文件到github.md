---
title: Linux-Centos7用git上传文件到github
tags:
  - Linux
  - git
categories:
  - 技术Tech
date: 2019-08-16 13:50:50
---
#操作步骤:
## 1. Centos7安装git
``` bash
yum install git -y
```
## 2. config git账户信息
``` bash
git config --global user.name ***用户名***   //绑定用户名
git config --global user.email ***邮箱***   //绑定邮箱
git config --list    //查看git配置
```
## 3. 服务器生成SSH公钥
``` bash
ssh-keygen -t rsa -C "your_email@youremail.com"   // 如果不想设置密码,全部enter.
```
## 4. 查看public key
``` bash
cat /root/.ssh/id_rsa.pub
```
## 5. 浏览器打开github,点开账户setting,左侧找到ssh和GPG keys栏,点开,选择SSH keys, 点击New SSH key绿色按钮, 粘贴步骤4得到的id_rsa.pub内容.自己命名. 此处我命名为cPouta-Centos7
## 6. 上传本地文件到git
``` bash
cd /media/volume2 //cd到你想要的文件夹
git init //起始git文件夹
git clone git://github.com/github-fish/fish-gatk-somatic-snvs-indels  //克隆我GitHub上的目标仓库
nano README.md //创建一个md文件
git add README.md //添加md文件到暂存区
git commit -m "代码提交信息" //添加代码提交信息, 此时你的改动已经提交到了HEAD, 但是还不在远端仓库.
```
如果你想撤销操作, 使用
``` bash
git reset
```
查看状态
``` bash
git status
```

推送到远端仓库
``` bash
git remote add origin https://github.com/github-fish/fish-gatk-somatic-snvs-indels.git
```
``` bash
git push -u origin master
```
接下来会询问你的github username和password. 按要求, 输入即可. 此时, 你已经可以在浏览器上查看自己的github仓库, 新的README.md文件已经 $

以后使用, 先把要上传的文件放在本地仓库中
``` bash
git add <file name>
git commit -m "提交评论"
git status
git push https://github.com/github-fish/fish-gatk-somatic-snvs-indels.git
```
