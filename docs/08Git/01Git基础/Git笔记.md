推荐视频：
图形界面用法参考（基础）：https://www.bilibili.com/video/av10475153
命令用法参考：https://www.bilibili.com/video/av24736323

# 常用命令

### 看隐藏文件

ls -lA

### 帮助文档

git help reset

### 设置用户

```
git config --global user.name
git config user.email
12
```

### git status

看状态，看有没有没提交的

### modified commited

工作区（写代码） 暂存区（临时存储）
本地库（历史版本）

### 最常用命令

```
git init
git add 111.txt
（git rm --cached 111.txt）
get commit -m  "修改备注" [file.md]
1234
```

### 版本查看

```
看版本记录，有用户名，时间等信息。
空格向下翻页，b上翻页，q退出
git log

`简洁显示`
git log --pretty=oneline
git log --oneline
git reflog
显示了步数，
123456789
```

![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190223205300.png)

### 版本更改

```
HEAD指向当前的版本
1：基于索引值（看不懂的那个值）
git reflog
git reset --hard 9a9cbc0（长短索引都可以）
2：使用^符号：只能往后退，数^的个数表示步数
git reset --hard HEAD^^^
3：使用~符号，后面加上数字
git reset --hard HEAD~3
12345678
```

> reset命令3个参数对比

–soft：仅仅只在本地库移动指针

–mixed：在本地库移动HEAD指针，重置暂存区

–hard：在本地库移动HEAD指针，重置暂存区，重置工作区

### 删除

```
rm 111.txt
git add 111.txt
get commit -m  "修改备注"
123
```

进行到一半撤回
删除文件并找回
git reset hard 【指针位置】

- 删除操作依据提交到本地库：指针位置指向历史记录
- 删除操作尚未提交到本地库：指针位置使用HEAD

### 比较差异（暂存区与本地库比较）

git diff [文件名]
不带文件名比较多个文件
git diff HEAD 111.txt
git diff HEAD^ 111.txt
减号表示删除的，加好表示增加的
添加到暂存区后再比较就没区别了

![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190223203441.png)

# 分支

![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190223225347.png)

```
查看分支
git branch -v

新建分支
git branch 分支名

切换分支
git checkout 分支名

合并master分支
1：切换到接受修改的分支master
2：git merge 副分支名
123456789101112
```

合并分支冲突
![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190223230453.png)
修改后
![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190223230548.png)
git add 111.txt
git commit -m “修改好了”
不能带文件名了
![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190223230826.png)

# 哈希

1：不管输入数据的数据量多大，输入同一个哈希算法，得到的加密结果长度固定。
2：哈希算法确定，输入数据确定，输出数据能够保证不变
3：哈希算法确定，输入数据有变化，输出数据一定有变化，而且通常变化很大。
4：哈希算法不可逆
Git底层采用的是SHA-1算法。
哈希算法可以被用来验证文件

集中式版本控制工具的文件管理机制
以文件变更列表的方式存储信息。这类系统将它们保存的信息看作是一组基本文件和每个文件随时间逐步累积的差异。
git保存版本的机制：
Git把数据看作是小型文件系统的一组快照。每次提交更新时Git都会对当前的全部文件制作一个快照并保存这个快照的索引。为了搞笑，如果文件没有修改，Git不再重新存储该文件，而是只保留一个链接指向之前存储的文件。所以Git的工作方式可以称之为快照流。
![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190224104336.png)

# Git和github交互

![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190224110842.png)
初始化本地库
创建远程库，不必非得同名
看远程库的地址
![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190224111813.png)

```
git remote -v

git remote add origin http://github.com/atguigu2018ybuq/kuming.git
( git remote rm origin)

git push [远程库地址别名] 推送的分支
git push origin 推送的分支
弹出了登录窗口

123456789
```

![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190224195317.png)

### clone

```
git clone 如图所示的地址http://github.com/atguigu2018ybuq/kuming.git
这步会自动创建.git隐藏文件（初始化本地库），而且git remote -v那里也会变化（创建origin远程地址别名）
12
```

![img](https://raw.githubusercontent.com/FermHan/tuchuang/master/20190224195629.png)

pull=fetch+merge

### fetch

git fetch [远程库地址别名] [远程分支名]
git fetch origin master

### merge覆盖本地的内容

git merge origin/master

### push

git push [远程库地址别名] 推送的分支
先push的人能push，后push 的先pull。然后会提示不一样的内容
修改后，git add… git commit …
error :failed to push some refs

### 权限fork

步骤： fork 修改 pull request 审核 merge
先在自己的里面改好push到github，
总的来说就是先在命令模式下修改，然后在图形模式下申请融合，点push request，点new pull request ，点create pull request，填写消息

## SSH