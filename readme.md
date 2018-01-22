## **关于 git 大小写问题的解决办法**

### **写在前面**

最近，在项目部署的时候，总是遇到一些很灵异的问题，比如在 merge 没有报错的情况下，文件没有更新， 特殊的文件突然被还原至之前的某个版本……最后发现问题所在，是之前的一次 **文件名大小写** 引起的问题。解决过程其实还是挺糟心的，深刻体会到好的规范比好的技术其实更重要。同时也觉得，有必要记录一下，谨防下次掉进去。

### **问题复现:**

1. 新建一个 **a.js** 文件(大小写不敏感的状态下)，并提交
2. 修改本地 **a.js** 变为 **A.js**，文件内容无变更，无法提交
3. 执行`git config core.ignorecase false`，修改 大小写敏感 规则，然后提交，查看结果，此时会存在 大小写 同时存在的文件
4. 此时某种机缘下，再次执行 `git config core.ignorecase true`，大小写不敏感，
5. 此时执行 `git push` ， 即把最新的更新都更新到了 **a.js** 中
6. 此时再修改 大小写敏感规则为敏感， 执行 `git pull` ，并不会拿到最新的更新。比如自己想要的是第一次修改后的 A.js ，但是服务器有一个没有更新的 **A.js** 和 有更新的 **a.js**,而你只能拿到前者，所以就会遇到各种各样的坑……


### **解决办法：**

执行`git config --global core.ignorecase false`，全局设置 **大小写敏感** 。

#### **1. 文件变更比较少的情况**

直接使用以下命令重命名文件，在 git 中不要直接修改文件名，最好的办法是使用下面的方式，

```
git mv -f [你想要删掉的文件] [你想要留下的文件]
git mv -f a.js A.js

等同于：

git rm a.js
git add A.js

```

这个命令的目的就是删除不需要的大小写同名文件，修改后 `git push` 提交变更即可。

**tips:** 

因为 git 默认大小写不敏感，所以最好添加项目配置文件,设置 **大小写敏感**。

```
touch .gitconfig
git config core.ignorecase false
```

#### **2. 变更比较多，并且拥有分支较高权限**

* 在 github 删除该分支
* 本地执行 `git rm -r --cached` . (注意后面‘点号’)
* 然后重新 `git push`，就ok了

此法不太好，有点暴力，容易出问题，但适用于 变更发生于近期的情况。

至此，终于填了一个不该踩的坑。




