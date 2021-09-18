# 文字测试

```git
git init // 初始化一个仓库
git add -A // 添加所有文件到暂存区，也就是交给由git管理着
git commit -m "myblogs first commit" // 提交到git仓库，-m后面是注释
git remote add origin https://github.com/Corefo/myblogs.git
git push -u origin master // 推送到远程myblogs仓库
>.gitignore只能忽略那些原来没有被 track 的文件，如果某些文件已经被纳入了版本管理中，则修改 .gitignore 是无效的。
解决方法是先把本地缓存删除，然后再提交。
git rm -r --cached .
git add .
git commit -m 'We really don't want Git to track this anymore!'
```

# 公式测试

$$ x^{y^z}=(1+{\rm e}^x)^{-2xy^w} $$

# pdf测试

```pdf
    ./pdf/IJLEO_57988.pdf
```

# 图片测试


![图片测试](../pictures/cat.png)
