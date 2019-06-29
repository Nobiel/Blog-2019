# 使用github的fork功能

最近小幅修改了一下博客的主题，想到可以利用github的fork功能建立一个pinghsu主题的修改版仓库，于是便学习了一下fork的用法。

## 建立修改版主题仓库

由于修改后的主题已经上传到typecho中，并且之前fork过pinghsu的主题，现在只需在typecho的/usr/theme/pinghsu目录下建立一个本地仓库，然后再强制推送到从原主题fork过来的github远程仓库即可。

```bash
#初始化
git init
#暂存修改
git add .
#提交修改
git commit -m "commit message"
#添加远程主机
git remote add modified https://YOUR_USERNAME@github.com/YOUR_USERNAME/YOUR_FORK.git
git remote add original https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
#可以查看已添加的远程主机列表
git remote -v
#强制推送修改（直接覆盖远程仓库）
git push modified --force
```

**注意：如果使用HTTPS方式连接，必须在github.com前面加上“YOUR_USERNAME@”，否则只能读取远程仓库而无法修改，执行`git push`操作将出现`fatal: HTTP request failed`错误。**

![fork并且更新GitHub仓库](https://ws1.sinaimg.cn/large/6273c3b7ly1fgg58xvoa5j20fp09vglr.jpg)

## 与原主题保持更新

建立好自己的fork主题仓库后，当原主题更新时需要同步更新自己的修改版主题仓库。

```bash
#获取原仓库更新
git fetch original
#切换到本地分支
git checkout master
#合并修改到本地分支
git merge original/master
#推送修改到远程仓库
git push modified
```

如果合并修改时遇到错误，解决冲突后再重新提交、推送修改即可。

![同步GitHub fork](https://ws1.sinaimg.cn/large/6273c3b7ly1fgg5c8upsxj20bo08wmx7.jpg)

参考资源：[Git 分支 - 分支的新建与合并](https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)

*文中图例来源于参考文献3*

## References

1. [Syncing a fork](https://help.github.com/articles/syncing-a-fork/)
2. [Pushing to Git returning Error Code 403 fatal: HTTP request failed](https://stackoverflow.com/questions/7438313/pushing-to-git-returning-error-code-403-fatal-http-request-failed)
3. [在Github和Git上fork之简单指南](https://linux.cn/article-4292-1-rss.html)
4. [本地git版本覆盖远程的方法](http://jerry-chen.iteye.com/blog/2059976)