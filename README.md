# LC的私人maven仓库

```
ahviplc/maven-repository
https://github.com/ahviplc/maven-repository

参考下面,学习搭建
liuyueyi/maven-repository: 小灰灰私人maven仓库
https://github.com/liuyueyi/maven-repository

目前这个私有库里,只有如下这个项目,需要在这个项目里,执行下面的一行maven命令或者脚本-最好配置阿里云maven中央仓库镜像
GitHub - ahviplc/JustToolc: ❤JustToolc > Java Tools For U (You) ❤
https://github.com/ahviplc/JustToolc

在自己的项目使用了,如下项目
GitHub - ahviplc/lc-es-api: lc-es-api ElasticSearch7.6以上的API的操作 还有其他框架集成
https://github.com/ahviplc/lc-es-api

lc-es-api: lc-es-api ElasticSearch7.6以上的API的操作
https://gitee.com/ahviplc/lc-es-api

如何借助GitHub搭建属于自己的maven仓库 - 灰信网（软件开发博客聚合）
https://www.freesion.com/article/7799452380/

理解maven命令package、install、deploy的联系与区别_zhaojianting的博客-CSDN博客 - 解释的不错 - 看了这个总结出下面的说明.
https://blog.csdn.net/zhaojianting/article/details/80324533

阿里云maven仓库服务 guide
https://maven.aliyun.com/mvn/guide

阿里云maven仓库服务 view
https://maven.aliyun.com/mvn/view
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

> fastgit 现在有问题 不推荐使用 已废弃 时好时坏

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
cd D:\DevelopSoftKu\Git\GitKu # win下不是这样的 【先 D： => 再 cd DevelopSoftKu\Git\GitKu】

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

> 进入本地指定一个目录,此文件夹 `maven-repository`  然后执行如下命令 进行git托管

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

### 4. 项目deploy发布和生成文档和源码命令 

> 最常用的打包命令有mvn package、mvn install、deploy，这三个命令都可完成打jar包或war（当然也可以是其它形式的包）的功能，但这三个命令还是有区别的，具体说明如下:

```markdown
mvn clean package 依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)等７个阶段。
mvn clean install 依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install等8个阶段。
mvn clean deploy 依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install、deploy等９个阶段。
由上面的分析可知主要区别如下，

package 命令完成了项目编译、单元测试、打包功能，但没有把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库
install 命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库，但没有布署到远程maven私服仓库
deploy 命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库
```
项目的清理命令

>mvn clean

项目的package打包命令-只是打包

>mvn package

项目的install打包 部署到本地命令

> mvn install

项目的deploy打包 部署到本地命令 布署到远程maven私服仓库

> mvn deploy

maven生成javadoc文档 

> maven javadoc:javadoc

maven生成source.jar

> mvn source:jar

maven清理安装并生成source.jar

> mvn clean install source:jar -Dmaven.test.skip=true -Dmaven.javadoc.skip=false

同时也需要主动的指定一下install或者deploy的地址，所以我们的install或deploy发布生成jar包同时生成javadoc文档和source源码的命令如下：

>  maven-repository的maven一行命令生成,实战下来,这里我们不能只是使用install将其部署到本地,这里具体位置可以配置的,是在maven的settings.xml的
>
>  <localRepository>C:\Users\Administrator\.m2\repository</localRepository>
>
>  这样 构建之后 就会得到如下目录存放构建的jar包等
>
>  【C:\Users\Administrator\.m2\repository\com\lc\JustToolc】.
>
>  我们需使用deploy,布署到远程maven私服仓库本地地址
>
>  【D:\DevelopSoftKu\Git\GitKu\maven-repository\repository\com\lc\JustToolc】.
>
>  然后我们可以手动 git commit 和 git push 推送到github的remote仓库.

```sh
# install生成jar包同时生成javadoc文档和source源码的命令
mvn -T 1C clean source:jar javadoc:javadoc deploy -Dmaven.test.skip=true -Dmaven.javadoc.skip=false -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository
```

```markdown
上面的一行命令代表:清理安装并生成source.jar 生成javadoc 跳过test的junit单元测试 不跳过javadoc文档生成
-Dmaven.test.skip=true 跳过test的junit单元测试
-Dmaven.javadoc.skip=false 不跳过javadoc文档生成
-DaltDeploymentRepository 主动的指定一下install或者deploy的本地地址
其他参数解释说明:
–T1:      线程数,可以并行地构建那些相互间没有依赖关系的模块,充分利用多核CPU资源。
–C:       如果校验不成功 ,那么表示失败
clean:    清理上一次构建生成的文件
-X:       debug级别 输出信息
```

> win下 快速建立目录命令: 
>
> 1. 进入d盘 命令:> D:
> 2. mkdir DevelopSoftKu\Git\GitKu\mavenrepository\repository

单独deploy版本 -使用这个的话 - 生成jar包但无文档无源码

```sh
# deploy项目到本地仓库 - 生成jar包但无文档无源码
mvn clean deploy -Dmaven.test.skip -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository
```

上面的命令就比较常见了，主要需要注意的是file后面的参数，根据自己前面设置的本地仓库目录来进行替换


### 5. deploy脚本

每次进行上面一大串的命令，不太好记，特别是不同的版本deploy到不同的分支上，主动去切换分支并上传，也挺麻烦，所以就有必要写一个deploy的脚本了

由于shell实在是不太会写，所以下面的脚本只能以凑合能用来说了

> deploy脚本 - 不带文档和源码版本

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
  # 开始deploy-生成jar包但无文档无源码
  mvn clean deploy -Dmaven.test.skip -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository

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

> deploy脚本 - 带文档和源码版本 - 推荐使用 - 包含文档,源码的生成

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
  ## 开始deploy生成jar包同时生成javadoc文档和source源码的命令
  mvn -T 1C clean source:jar javadoc:javadoc deploy -Dmaven.test.skip=true -Dmaven.javadoc.skip=false -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository

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
>  总而言之

### 6. 一行maven命令走天下

> mvn -T 1C clean source:jar javadoc:javadoc deploy -Dmaven.test.skip=true -Dmaven.javadoc.skip=false -DaltDeploymentRepository=self-mvn-repo::default::file:D:\DevelopSoftKu\Git\GitKu\maven-repository\repository

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

> fastgit 现在有问题 不推荐使用 已废弃 时好时坏

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
# 扩展-git操作

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
# 扩展-阿里云 仓库服务 maven 配置指南
> 为了加快maven的构建速度.

> 打开 maven 的配置文件（ windows 机器一般在 maven 安装目录的 conf/settings.xml ），在<mirrors></mirrors>标签中添加 mirror 子节点:

```xml
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

> 如果想使用其它代理仓库，可在<repositories></repositories>节点中加入对应的仓库使用地址。以使用 spring 代理仓为例：

```xml
<repository>
  <id>spring</id>
  <url>https://maven.aliyun.com/repository/spring</url>
  <releases>
    <enabled>true</enabled>
  </releases>
  <snapshots>
    <enabled>true</enabled>
  </snapshots>
</repository>
```

> 在你的 pom.xml 文件<denpendencies></denpendencies>节点中加入你要引用的文件信息：

```xml
<dependency>
  <groupId>[GROUP_ID]</groupId>
  <artifactId>[ARTIFACT_ID]</artifactId>
  <version>[VERSION]</version>
</dependency>
```

> 执行拉取命令：
```sh
mvn install
```

## maven 的 settings.xml 配置样例

```xml
<?xml version="1.0" encoding="UTF-8"?>

<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
          http://maven.apache.org/xsd/settings-1.0.0.xsd">

    <!--阿里云Maven中央仓库-仓库服务-https://maven.aliyun.com/mvn/guide-->

    <!--本地仓库目录，注意此处目录应该与上面的设置Local Repository一致-->
    <!--<localRepository>H:/maven/repository</localRepository>-->
    <localRepository>C:\Users\Administrator\.m2\repository</localRepository>
    <mirrors>
        <mirror>
            <!--该镜像的id-->
            <id>aliyun-maven</id>
            <!--该镜像用来取代的远程仓库，*的意思就是（根据mirrorOf和repository的id）匹配所有的库（repository）-->
            <!--匹配aliyun-maven-repo,aliyun-maven-repo是pom.xml中自定义repository仓库的id-->
            <!--其实按道理,pom.xml里配置的aliyun-maven-repo已经是阿里云中央仓库了,转到这边,再用阿里云中央仓库链接取代其链接,多此一举,这里只是为了更加理解其含义,做的配置-->
            <!--排除ahviplc-maven-repo，ahviplc-maven-repo是pom.xml中自定义repository仓库的id-->
            <mirrorOf>*,!ahviplc-maven-repo</mirrorOf>
            <name>阿里云公共仓库-public</name>
            <!--该镜像的仓库地址，这里是用的阿里的仓库【https://maven.aliyun.com/repository/public】【http://maven.aliyun.com/nexus/content/groups/public】【C:\Users\Administrator\.m2\settings.xml】-->
            <url>https://maven.aliyun.com/repository/public</url>
        </mirror>

        <mirror>
            <!--该镜像的id-->
            <id>nexus-aliyun</id>
            <!--该镜像用来取代的远程仓库，central是中央仓库的id-->
            <!--排除ahviplc-maven-repo，ahviplc-maven-repo是pom.xml中自定义repository仓库的id-->
            <mirrorOf>central,!ahviplc-maven-repo</mirrorOf>
            <name>Nexus aliyun</name>
            <!--该镜像的仓库地址，这里是用的阿里的仓库【https://maven.aliyun.com/repository/public】【http://maven.aliyun.com/nexus/content/groups/public】【C:\Users\Administrator\.m2\settings.xml】-->
            <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
    </mirrors>
</settings>
```

