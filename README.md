# LC的私人maven仓库

```
ahviplc/maven-repository
https://github.com/ahviplc/maven-repository

liuyueyi/maven-repository: 小灰灰私人maven仓库
https://github.com/liuyueyi/maven-repository

如何借助GitHub搭建属于自己的maven仓库 - 灰信网（软件开发博客聚合）
https://www.freesion.com/article/7799452380/
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

