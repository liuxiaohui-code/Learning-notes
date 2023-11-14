# 安装

## linux 安装

```shell
# 首先，试着在终端里面输入git，查看是否已安装，若出现下列内容，则说明没有安装git
git

# Ubuntu系统使用：
sudo apt-get install git

# CentOS使用：
sudo yum install git -y

# git 源码编译
git clone https://github.com/git/git.git
./configure
make
sudo make install

# 二进制文件下载
wget https://www.kernel.org/pub/software/scm/git/git-2.8.3.tar.gz
tar -zxvf git-2.22.0.tar.gz
cd git-2.8.3
make configure
# 配置目录
./configure --prefix=/usr/git
make profix=/usr/git
make install

# 加入环境变量
echo "export PATH=$PATH:/usr/local/git/bin" >> /etc/profile
source /etc/profile
```

## windows 安装

前往官网下载对应客户端：https://git-scm.com/downloads 安装 Git open in new window 下载完成后默认安装即可，（默认会将 git 添加到系统环境变量）

# git 项目结构

## 1.工作区

​ 电脑里能看到的目录

## 2.暂存区

​ `git add` 把文件添加进去，实际上就是把文件修改添加到暂存区；`git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支。

## 3.版本库

​ 隐藏目录`.git`，Git 的版本库。

​ Git 的版本库里存了很多东西，其中最重要的就是称为 stage（或者叫 index）的暂存区，还有 Git 为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

# git 命令参数

```Shell
# 版本查看
git --version
# 命令帮助
git --help
git --help --all
git --help -a
# 设置配置属性,将会替换git config的值
git -c <key>=<name>
# 设置git可执行文件路径,也可以设置环境变量GIT_EXEC_PATH
git --exec-path[=<path>]

# 升级
git update # 2.17.1版本之前
git update-git-for-windows #2.17.1之后的版本
```

# 版本库

## 初始化

```shell
# 创建空文件夹，cd 到文件夹下，Windows 系统的路径最好不要有中文
# 把这个目录变成 Git 可管理的仓库，生成`.git`目录
git init
```

## 添加文件到暂存区

```shell
# 添加单个文件
git add <file>
# 添加多个文件
git add file1 file2 ...
# 添加全部已修改文件
git add .
```

## 提交暂存区修改

```shell
# 将 git add 的文件修改提交到本地版本库
git commit -m "说明"
```

## 版本回退

```shell
# 回退到上个版本
git reset --hard HEAD
# 版本号可以不写全，写前几位即可
git reset --hard 版本号

`HEAD`表示当前版本
上一个版本就是`HEAD^`，
上上一个版本就是`HEAD^^`，
当然往上 100 个版本写 100 个`^`比较容易数不过来，所以写成`HEAD~100`。
```

## 撤销修改

```shell
# 丢弃工作区的修改，即让这个文件回到最近一次`git commit`或`git add`时的状态
git checkout -- <file_name>
git restore <file>
# 丢弃工作区的修改所有文件的修改
git checkout -- .
git restore .
# 把暂存区的修改撤销掉，重新放回工作区。
git reset HEAD <file_name>
git restore --staged <file>
# 丢弃暂存区所有文件的修改
git reset HEAD .
git restore --staged .

# 不删除工作区的修改，仅撤销commit
git reset --soft <commit_id>
```

## 删除文件

```shell
# 删除本地 git
git rm <file>
# 提交删除操作
git commit -m "message"
# -n显示将要删除的文件和目录
# -d 递归删除
# -f 强制删除
git clean -ndf <文件或目录>
```

## 查看状态与日志

```shell
# 查看到仓库当前状态，包括那些文件修改了，那些文件被添加了
git status
# 查看该文件改动的内容
git diff <文件名>
# 查看工作区和版本库里面最新版本
git diff HEAD -- <文件名>
# 查看冲突文件内容
git diff
# 查看最近提交日志
git log
# 查看简化提交历史
git log --pretty=oneline
# 查看分支合并图
git log --graph
# 查看命令历史
git reflag
```

## 误删恢复

git 中把 commit 删了后，并不是真正的删除，而是变成了悬空对象（dangling commit）。

```shell
# 找回git add过但是已经不存在文件中的内容 .git/lost-found/other
git fsck --lost-found
# 使用git stash或者sourcetree贮藏了工作现场，然后被误删除了这个stash
git fsck --unreachable
# 查看提交的详细
git show <id>
# 恢复提交
git merege <id>
# 只能恢复unreachable commit 开头的记录，unreach blob是不能用git statsh apply+<sha>来恢复的
git stash apply <你找到的那条记录的key>

# 删除 .git\objects 中无法被访问的hash文件
git prune
# 同步远程,但不会删除本地文件
git remote prune origin
# 在提取之前，删除远程服务器上不再存在的任何远程跟踪引用
git fetch --prune
```

# 分支管理

## 分支查看

```Shell
# 查看所有分支,包含远程分支
git branch -a
# 查看本地分支
git branch
```

## 创建分支

```Shell
# 创建分支
git branch <分支名>
# 以某一次提交创建分支
git branch <分支名> <提交hash>
# 创建并切换到分支
git checkout -b <分支名>
git switch -c <分支名>
```

## 切换分支

```shell
# 切换分支
git checkout <分支名>
# 切换分支 git 2.23
git switch <分支名>

```

## 删除分支

```Shell
# 删除分支
git branch -d <分支名>
# 强制删除一个分支
git branch -D <分支名>
# 已删除分支恢复
# 1. 查找已删除分支的hash值
git reflog
# 2. 创建hash分支
git branch <分支名> <提交hash>
```

## 分支合并

```shell
# 合并不存在冲突的分支到当前分支
git merge <其他分支>

# git 合并分支默认使用Fast forward模式,删除分支后，会丢掉分支信息
# --no-ff 禁用Fast forward模式,Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息
git merge --no-ff -m "dev2 merged with mo-ff" dev2

# fatal: refusing to merge unrelated histories
git merge origin/master --allow-unrelated-histories

# git merge可以合并多个分支，即该命令后面可以跟多个分支的名字，都是将这些分支的变更合并到当前分支。
# 把远程分支master在本地的副本以及本地分支master合并到当前分支A
git merge origin master
# 把远程分支master在本地的副本合并到当前分支A
git merge origin/master

# 把bug提交的修改“复制”到当前分支，避免重复劳动。
git cherry-pick <commit_id>
```

## 冲突合并

当产生合并冲突时，该部分会以<<<<<<<, =======和 >>>>>>>表示。
在=======之前的部分是当前分支这边的情况，
在=======之后的部分是传入分支的情况。

```shell
# 1. 尝试合并,此时没有冲突的文件会被添加到暂存区
git merge
# 2. 查看冲突文件查看差异,并手动解决冲突文件
git diff
git diff <文件名>
# 3. 添加已解决冲突的文件到暂存区,并提交
git add <文件1>  && git commit -m "合并分支"

# 不合并冲突
git merge --abort

# 查看每个分支的差异。
git log --merge -p <path> #将会显示HEAD版本和MERGE_HEAD版本的差异。
#查看合并前的版本。
git show :1:文件名 #显示共同祖先的版本，
git show :2:文件名 #显示当前分支的HEAD版本，
git show :3:文件名 #显示对方分支的MERGE_HEAD版本。
```

## 变基

```shell
# 变基,这些命令会把你的”mywork“分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到”.git/rebase“目录中),然后把”mywork“分支更新 到最新的”origin“分支，最后把保存的这些补丁应用到”mywork“分支上。

git rebase <branch> # 分支合并，将
git rebase origin # 合并远程分支
# 放弃变基,分支会回到rebase开始前的状态
git rebase --abort
# 解决冲突后继续变基
git add <冲突文件> # 将解决冲突的文件添加到缓存区,不需要提交
git rebase --continue
```

# 工作现场

作用：系统出现 bug 时，用于保存当前工作现场，之后切换到 bug，签出 bug 分支，修复 bug，合并到主分支，删除 bug 分支，最后返回之前分支恢复工作现场；
stash 保存的方式为堆栈，使用 pop 先进后出；

```shell
# 保存工作现场
# 保存: 暂存区修改\Git 跟踪的但并未添加到暂存区的修改
# 不保存: 新文件\忽略的文件\
git stash
git stash save <"提示信息">
# 保存新文件
git stash -u
git stash --include-untracked
# 保存所有修改
git stash -a
git stash --all
# 工作现场列表
git stash list
# 工作现场恢复
git stash apply # 恢复全部暂存状态,但不删除暂存内容
git stash apply <stash@{0}> # 恢复指定暂存状态，但不删除暂存内容
git stash pop # 恢复的同时删除最顶层的 stash
# 工作现场删除
git stash drop <name>
# 显示工作现场内容
# -p 或 --patch 可以查看特定 stash 的全部差异
git stash show [-p] [--patch]
# 从 stash 创建分支
git stash branch
```

# 标签管理

标签是一次提交的快照

## 标签查看

```shell
# 显示所有标签
git tag
# 查看标签信息
git show <tagname>
```

## 标签创建

```shell
# 为当前版本创建标签
git tag <标签名>
# 为指定版本创建标签
git tag <标签名> <commit_id>
# 创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字
git tag -a v0.1 -m "version 0.1 released" 1094adb
```

## 标签删除

```shell
# 删除
git tag -d <tagname>
# 删除远程标签
git push origin :refs/tags/<tagname>
```

## 标签推送

```shell
# 推送某个标签到远程
git push origin <本地标签名>
# 推送全部未推送过的本地标签
git push origin --tags
```

# 远程仓库

## 创建远程仓库

```Shell
# git bash
# 生成密钥
# -t <加密算法> 没有指定则默认生成用于SSH-2的RSA密钥
# -C <注释>
ssh-keygen -t rsa -C "youremail@example.com"
# 输入密语字符串,空表示没有密语
# 输入两次口令密码,空表示没有口令
# 寻找密匙位置 c盘>用户>自己的用户名>.ssh
id_rsa      #私钥（不可泄漏）
id_rsa.pub  #公钥
# github > Account settings > SSH and GPG Keys > New SSH Key
title: 任意内容
key: id_rsa.pub
# 验证 有问题可以删除密钥重新生成
ssh -T git@github.com

# 关联远程仓库,其中origin是默认的远程仓库名
git remote add origin <ssh链接或http链接>
# 删除远程仓库
git remote rm origin
# 查看远程仓库链接
git remote -v

# 修改远程主机名 origin
git remote rename origin testname
# 更换远程仓库地址，URL为新地址。
git remote set-url origin <url>
# 删除远程库
git remote rm origin
```

## 克隆

```shell
# 克隆远程仓库到本地
# -b <分支名>  选择克隆的分支;默认为master
git clone [-b 分支名] <远程仓库地址.git>
```

## 拉取更新

```shell
# 拉取更新
git pull
```

## 推送

```shell
# 推送提交到远程仓库相关联的分支
git push
# 推送提交到远程的master分支
git push origin master
# -u参数是将本地master分支与远程仓库master分支关联起来，一般用于第一次推送代码到远程库
git push -u origin master
# 推送本地分支到远程
git push --set-upstream origin <分支名>
# 删除远程分支
git push <远程仓库名> --delete <远程分支名>
# 推送本地分支到远程仓库并在远程仓库创建新分支
git push <远程仓库名> <本地分支名>:<远程分支名>
#将本地分支与远程仓库关联
git branch --set-upstream-to <branch-name> origin/<branch-name>
```

# git config

```shell
#查看git配置信息
git config --list
```

## 修改 git 仓库提交人员信息

```shell
#配置用户名和邮箱：
git config --global user.name "username"
git config --global user.email "useremail@126.com"
```

## 缓存密码

```shell
#清除配置中纪录的用户名和密码，下次提交代码时会让重新输入账号密码：
git config --system --unset credential.helper
#执行命令之后，再次pull或push时会缓存输入的用户名和密码：
git config --global credential.helper store
#清除git缓存中的用户名的密码
git credential-manager uninstall
```

## 让 Git 显示颜色

```sh
# 让Git显示颜色
git config --global color.ui true
```

## .gitignore

在 Git 工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git 就会自动忽略这些文件。

不需要从头写`.gitignore`文件，GitHub 已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如 Java 编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

或者你发现，可能是`.gitignore`写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查.

把指定文件排除在`.gitignore`规则外的写法就是`!`+文件名，所以，只需把例外文件添加进去即可。

## 配置命令别名

- `git config --global alias.<别名> 原命令`
- 配置 Git 的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
- log 示例 `git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`

## 配置文件

​ 每个仓库的 Git 配置文件都放在`.git/config`文件中；

​ 当前用户的 Git 配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中；

# 附录

## 资料

1. [中文文档](https://github.com/apachecn/git-doc-zh/issues/1)
2. [英文文档](file:///D:/tools/Git/mingw64/share/doc/git-doc/git.html)
3. [git 教程](https://www.yiibai.com/git)

## 异常

### 1. Unlink of file ‘…’ failed. Should I try again?

原因是你工作目录有某些文件正在被程序使用，这个程序多半是 Idea,VS 或者 eclipse,当然也可能是其他程序
可能你在操作 git 时工程没有关闭
解决方案不是简单的选择 y 或者 n,而是关闭 IDE，让 IDE 把这些文件释放掉
