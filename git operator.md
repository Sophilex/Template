git 操作

## 远程代码覆盖本地

```
git fetch --all && git reset --hard origin/master && git pull

```

以下是注释：

```
git fetch --all	#取回远程库的所有修改；
git reset --hard origin/master	#指向远程库origin的master
git pull	#把远程库拉取到本地库

```

## 克隆仓库到本地

https://blog.csdn.net/qq_43795047/article/details/126772044

## 将本地库与$github$上的库同步

首先将本地库与远程库相关联

```
git remote add template https://github.com/Sophilex/Template.git
git remote add 你给这个本地库取的名字 远程仓库的网址
```

将本地库推送上去

```
git push -u template mater
git push -u 本地库的名字 想要推送上去的分支的名字
```

过程中可能会报错，大概是因为代理的缘故，可以添加以下代码

```
git config --global --add remote.origin.proxy ""
```

## 更新代码到库

首先你已经在之前实现过上一步操作了

```
git add
git commit
# 前两步完成本地提交
gti push
# 将本地内容推送到远程库（因为之前已经建立过连接了，所以命令相对简洁）

```





![image-20231204114345356](https://s2.loli.net/2023/12/04/3JfwKA9hHSsVxi7.png)
