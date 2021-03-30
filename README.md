# LC的私人maven仓库

```
ahviplc/maven-repository
https://github.com/ahviplc/maven-repository

参考下面,学习搭建
liuyueyi/maven-repository: 小灰灰私人maven仓库
https://github.com/liuyueyi/maven-repository

如何借助GitHub搭建属于自己的maven仓库 - 灰信网（软件开发博客聚合）
https://www.freesion.com/article/7799452380/
```

# 说明

## I. 使用手册

> 使用的简单说明

xml 配置如下

**添加仓库**

如果要区分snapshot和release的话，如下配置

```xml
<repositories>
    <repository>
        <id>ahviplc-maven-repo-snap</id>
        <url>https://raw.githubusercontent.com/ahviplc/maven-repository/snapshot/repository</url>
    </repository>
    <repository>
        <id>ahviplc-maven-repo-release</id>
        <url>https://raw.githubusercontent.com/ahviplc/maven-repository/release/repository</url>
    </repository>
</repositories>
```

### 原始

如果不care的话，直接添加下面的即可

> 原始链接

```xml
<repositories>
    <repository>
        <id>ahviplc-maven-repo</id>
        <url>https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository</url>
    </repository>
</repositories>
```

### fastgit-废弃

或者使用 fastgit

> fastgit 现在有问题 不推荐使用 已废弃

```xml
<!--添加repositories 其fastgit 现在有问题 不推荐使用 已废弃-->
<repositories>
    <repository>
        <id>ahviplc-maven-repo</id>
        <url>https://raw.fastgit.org/ahviplc/maven-repository/master/repository</url>
    </repository>
</repositories>
```

### ghproxy

> fastgit基本废弃-还没官方地址好用 推荐使用 ghproxy 速度更快,更稳定 - 【https://ghproxy.com/】

```xml
<repository>
<id>ahviplc-maven-repo</id>
<!-- ghproxy【https://ghproxy.com/】速度更快 -->
<url>https://ghproxy.com/https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository</url>
</repository>
```

### bajins

> 或者使用 bajins 的github镜像 - 【https://github.bajins.com/】

```xml
<repository>
<id>ahviplc-maven-repo</id>
<!-- bajins【https://github.bajins.com/】速度也不错 -->
<url>https://github.bajins.com/ahviplc/maven-repository/raw/master/repository</url>
</repository>
```

**引入依赖**

按需引入即可


## II. 从零开始搭建过程

### 1. github仓库建立

新建一个repository的前提是有github帐号，默认看到本文的是有帐号的

首先是在github上新建一个仓库，命令随意，如我新建项目为

- [https://github.com/ahviplc/maven-repository](https://github.com/ahviplc/maven-repository)


### 2. 配置本地仓库

本地指定一个目录，新建文件夹 `maven-repository`, 如我的本地配置如下

```sh
## 进入目录
cd D:\DevelopSoftKu\Git\GitKu

## 新建目录
mkdir maven-repository; cd maven-repository

## 新建repository目录
# 这个目录下面就是存放我们deploy的项目相关信息
# 也就是说我们项目deploy指定的目录，就是这里
mkdir repository

## 新增一个readme文档
# 保持良好的习惯，每个项目都有一个说明文档
touch README.md
```

**这个目录结构为什么是这样的？**

我们直接看maven配置中默认的目录结构，同样拷贝一份出来而已


### 3. 仓库关联

将本地的仓库和远程的github仓库关联起来，执行的命令也比较简单了

```sh
git add .
git commit -m 'first comit'
git remote add origin https://github.com/ahviplc/maven-repository.git
git push -u origin master
```

接着就是进行分支管理了

- 约定将项目中的snapshot版，deploy到仓库的 snapshot分支上
- 约定将项目中的release版，deploy到仓库的 release分支上
- master分支管理所有的版本

所以需要新创建两个分支

```sh
## 创建snapshot分支
git checkout -b snapshot 
git push origin snapshot
# 也可以使用 git branch snapshot , 我通常用上面哪个，创建并切换分支

## 创建release分支
git checkout -b release
git push origin release
```

### 4. 项目deploy

项目的deploy，就需要主动的指定一下deploy的地址了，所以我们的deploy命令如下

```sh
## deploy项目到本地仓库
mvn clean deploy -Dmaven.test.skip  -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository
```

上面的命令就比较常见了，主要需要注意的是file后面的参数，根据自己前面设置的本地仓库目录来进行替换


### 5. deploy脚本

每次进行上面一大串的命令，不太好记，特别是不同的版本deploy到不同的分支上，主动去切换分支并上传，也挺麻烦，所以就有必要写一个deploy的脚本了

由于shell实在是不太会写，所以下面的脚本只能以凑合能用来说了

```sh
#!/bin/bash

if [ $# != 1 ];then
  echo 'deploy argument [snapshot(s for short) | release(r for short) ] needed!'
  exit 0
fi

## deploy参数，snapshot 表示快照包，简写为s， release表示正式包，简写为r
arg=$1

DEPLOY_PATH=D:\DevelopSoftKu\Git\GitKu\maven-repository
CURRENT_PATH=`pwd`

deployFunc(){
  br=$1
  ## 快照包发布
  cd $DEPLOY_PATH
  ## 切换对应分支
  git checkout $br
  cd $CURRENT_PATH
  # 开始deploy
  mvn clean deploy -Dmaven.test.skip  -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository

  # deploy 完成,提交
  cd $DEPLOY_PATH
  git add -am 'deploy'
  git push origin $br

  # 合并master分支
  git checkout master
  git merge $br
  git commit -am 'merge'
  git push origin master
  cd $CURRENT_PATH
}

if [ $arg = 'snapshot' ] || [ $arg = 's' ];then
  ## 快照包发布
  deployFunc snapshot
elif [ $arg = 'release' ] || [ $arg = 'r' ];then
  ## 正式包发布
  deployFunc release
else
  echo 'argument should be snapshot(s for short) or release(r for short). like: `sh deploy.sh snapshot` or `sh deploy.sh s`'
fi
```


将上面的脚本，考本到项目的根目录下，然后执行

```sh
chmod +x deploy.sh

## 发布快照包
./deploy.sh s
# sh deploy.sh snapshot 也可以

## 发布正式包
./deploy.sh r
```

基于此，整个步骤完成


## III. 使用

上面仓库的基本搭建算是ok了，然后就是使用了，maven的pom文件应该怎么配置呢？

首先是添加仓库地址

**添加仓库**

### 原始链接

如果要区分snapshot和release的话，如下配置

```xml
<repositories>
    <repository>
        <id>ahviplc-maven-repo-snap</id>
        <url>https://raw.githubusercontent.com/ahviplc/maven-repository/snapshot/repository</url>
    </repository>
    <repository>
        <id>ahviplc-maven-repo-release</id>
        <url>https://raw.githubusercontent.com/ahviplc/maven-repository/release/repository</url>
    </repository>
</repositories>
```

如果不care的话，直接添加下面的即可

> 这是原始链接

```xml
<repositories>
    <repository>
        <id>ahviplc-maven-repo</id>
        <url>https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository</url>
    </repository>
</repositories>
```
pom
> https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.pom

jar
> https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.jar

### fastgit-废弃

或者添加如下,速度更快

> fastgit 现在有问题 不推荐使用 已废弃

```xml
<!--添加repositories 使用fastgit 速度更快 可解决Could not transfer artifact问题-->
<repositories>
    <repository>
        <id>ahviplc-maven-repo</id>
        <url>https://raw.fastgit.org/ahviplc/maven-repository/master/repository</url>
    </repository>
</repositories>
```
pom
> https://raw.fastgit.org/ahviplc/maven-repository/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.pom

jar
> https://raw.fastgit.org/ahviplc/maven-repository/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.jar

### ghproxy

> fastgit基本废弃-还没官方地址好用 推荐使用 ghproxy 速度更快,更稳定 - 【https://ghproxy.com/】

```xml
<repository>
<id>ahviplc-maven-repo</id>
<!-- ghproxy【https://ghproxy.com/】速度更快 -->
<url>https://ghproxy.com/https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository</url>
</repository>
```

pom
> https://ghproxy.com/https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.pom

jar
> https://ghproxy.com/https://raw.githubusercontent.com/ahviplc/maven-repository/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.jar

### bajins

> 或者使用 bajins 的github镜像 - 【https://github.bajins.com/】

```xml
<repository>
<id>ahviplc-maven-repo</id>
<!-- bajins【https://github.bajins.com/】速度也不错 -->
<url>https://github.bajins.com/ahviplc/maven-repository/raw/master/repository</url>
</repository>
```

pom
> https://github.bajins.com/ahviplc/maven-repository/raw/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.pom

jar
> https://github.bajins.com/ahviplc/maven-repository/raw/master/repository/com/lc/JustToolc/0.2.0/JustToolc-0.2.0.jar

## 引入依赖

> 如依赖我的JustToolc包，就可以添加下面的依赖配置

```xml
<dependency>
  <groupId>com.lc</groupId>
  <artifactId>JustToolc</artifactId>
  <version>0.2.0</version>
</dependency>
```

仓库配置完毕之后，直接引入依赖即可，如依赖我的JustToolc包，就可以添加下面的依赖配置

```xml
<dependency>
  <groupId>com.lc</groupId>
  <artifactId>JustToolc</artifactId>
  <version>0.2.0</version>
</dependency>
```
# git操作

> create a new repository on the command line | push an existing repository from the command line

```
echo "# maven-repository" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/ahviplc/maven-repository.git
git push -u origin main
---------------------------------------
git remote add origin https://github.com/ahviplc/maven-repository.git
git branch -M main
git push -u origin main
```

