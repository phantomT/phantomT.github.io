---
title: 搭建Hexo博客的精简步骤与使用Git同步博客项目文件
date: 2019-05-02 19:18:12
tags:
    - hexo
    - git
    - node.js
    - GitHub
    - Markdown
---
2018-02-03  
上学期末就有搭建一个个人博客的想法，寒假到来，终于付诸实施，选择了比较简单易行的hexo在GitHub Pages上建立博客，使用Markdown进行文章编写。（使用Windows 10系统搭建）
相关链接:
- [Hexo](https://hexo.io/)
- [GitHub Pages](https://pages.GitHub.com/)
- [Markdown](https://www.appinn.com/markdown/#autoescape)
- [node.js](https://nodejs.org/)
- [Git](https://git-scm.com/)
<!-- more -->

## 环境配置
[文中有些命令执行完会出现yes/no的选项，如果要选yes，一定要输入yes，否则可能出现失败]
### 安装与搭建
安装Git和Node.js，完成环境配置
#### 安装Git
[Git](https://git-scm.com/)
#### 安装Node.js
[node.js](https://nodejs.org/)
之后的命令基本都在Git Bash中进行输入

## 仓库部署
为了满足可以在不同的电脑上写文章的需求，我们需要将项目部署在GitHub上，由于Hexo会自动将public文件夹发布在master分支下以供展示，因此我们新建一个分支来保存项目文件，同时因为同时因为有配置.gitignore文件，无需担心node_modules等文件被手动发布到hexo分支下，达到文件分类存放的目的( •̀ ω •́ )y。
### 创建仓库
进入自己的GitHub，创建一个repo，名称一定要命名为:yourname.GitHub.io，其中yourname是你的GitHub的名称，按照这个规则创建才可以使用的哦
### 创建两个分支
- master: 用来存放自动发布的页面内容
- hexo: 这个分支用来存放项目内容
设置hexo为默认分支，因为我们需要进行手动管理的只有这个hexo里面的项目文件，master里的页面内容是由Hexo自动发布。
### 拷贝仓库到本地
在自己想要的文件夹中使用Git Bash将自己的仓库拷贝到本地
['yourname'是自己的GitHub的名字]
```
$ git clone git@GitHub.com:yourname/yourname.GitHub.io.git
```
**这一步如果无法完成请先和GitHub进行关联**

## 关联GitHub
### 设置Git的账户信息
回到Git Bash，配置GitHub账户信息
```
$ git config --global user.name "yourname"
$ git config --global user.email "youremail"
```
其中'yourname'你懂的
### 设置SSH
在Git Bash中输入:
```
$ ssh-keygen -t rsa -C "youremail@example.com"
```
生成SSH，其中'youremail'和'example'你懂的
然后再使用
```
$ cd ~/.ssh
$ cat id_rsa.pub
```
找到id_rsa.pub的内容
将上面获得的内容复制
打开GitHub 自己的Settings，选择'SSH and GPG keys'并添加一个'New SSH key'，title随意，key就放刚才的内容。
### 验证SSH
在Git Bash中验证是否添加成功:`$ ssh -T git@GitHub.com`
如果验证成功就可以进入下一步了

## 建立Hexo
### 安装Hexo
**[Hexo](https://hexo.io/)官网**
**Hexo所有操作以官网的文档为准，有关博客主题也推荐从官网选取。**

安装使用命令:
```
$ npm install -g hexo
```
安装完成后可以使用:`$ hexo -v`命令查看版本
接下来进行Hexo初始化
在本地的yourname.GitHub.io文件夹中进行操作（必须是空文件夹，具体如何实施就看自己的灵性了，我是先把能复制的文件移出去然后再移回来，注意windows下隐藏的git文件）
```
$ hexo init
$ npm install
$ npm install hexo-deployer-git
```
### 修改设置文件
修改_config.yml中的deploy参数，
```
deploy: 
    type: git
    repo: https://GitHub.com/yourname/yourname.GitHub.io.git
    branch: master
```
### 运行本地博客
然后回到Git Bash中进入blog的目录，执行以下命令:
```
$ hexo clean && hexo generate && hexo server
```
[若服务器出现问题可能需要安装`$ npm install hexo-server`]

接着打开浏览器输入:`http://localhost:4000`
就可以看到天使啦~
不过这还是本地的项目，我们还需要和GitHub进行关联并上传项目文件

## 发布博客并上传项目
### 上传项目
```
$ git add . 
$ git commit -m "..."
$ git push origin hexo
```
或其他等价代码，如:
```
$ git add . && git commit -m "..." && git push origin hexo
```
**在第一次上传时可能会出现GitHub的登录提示，登录完成即可上传**

### 发布博客
```
$ hexo clean
$ hexo generate
$ hexo deploy
```
或者:
```
$ hexo clean && hexo g -d
```
可以将博客发布，
发布之后在浏览器中输入:`http://yourname.GitHub.io`就可以看到自己的个人博客啦~

## 其他可能用到的命令
```
$ git branch hexo       #创建hexo分支
$ git checkout hexo     #切换到hexo分支
$ git status
```

## Hexo的常用命令
在hexo根目录进行
### 新建文章
```
$ hexo new post "yourtitle"
```
### 新建页面
```
$ hexo new page "yourname"
```
### 清楚生成文件与缓存
```
$ hexo clean
```
### 生成与部署
```
$ hexo g -d
```

## _config文件
博客根目录的" _config "文件与主题文件夹中的" _config "文件配置的内容不容，具体可以看注释，修改自己需要的部分。
**注意:_config文件中的英文冒号后一定要加空格，其他一些控制符号后也要加空格！！！具体设置时可以尝试以下= =**
至于其他主题什么的设置我就不讲了，那些可以在Hexo官方网站找到推荐的主题，各主题也有自己的配置方式，请参考各主题的文档。