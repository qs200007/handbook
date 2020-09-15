# Let's say : Git!

**参考：**

<https://www.bilibili.com/video/BV1bs411N7ny?p=6>

<https://www.cnblogs.com/liuzhenbo/p/11077974.html>

## 1 安装git

命令行输入：

```
sudo apt-get install git
```

在GitHub上注册账号。

在命令行输：

```
git config --global user.name "qs200007"
git config --global user.email "qiuzeming@nibs.ac.cn"
git config --list
```

把本地的ssh公钥发给GitHub的setting。<https://github.com/settings/keys>

输入用于测试：

```
ssh -T git@github.com
```

克隆GitHub上的项目到本地：

```
git clone git@github.com:qs200007/handbook.git
```

在本地，你可以：

```
git add
git commit -m "version1.0    add README.md"
git reset --soft HEAD~
git reset --mixed HEAD   #default
git reset --hard HEAD~10
git checkout
git status
git log
```

关于分支（branch）：

```
git branch feature
git checkout feature
git add feature.txt
git commit -m "add feature"
git checkout master
git log --decorate --oneline --graph --all
git merge feature
git branch -d freature
```





把东西放到GitHub：

```
git push
```

