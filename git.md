## 1、Git 基本操作


Git 的工作就是创建和保存你项目的快照及与之后的快照进行对比。

Git 常用的是以下 6 个命令：**git clone**、**git push**、**git add** 、**git commit**、**git checkout**、**git pull**，后面我们会详细介绍。

![git-command](git.assets/git-command.jpg)

**说明：**

* workspace：工作区
* staging area：暂存区/缓存区
* local repository：版本库或本地仓库
* remote repository：远程仓库

一个简单的操作步骤：

```
$ git init 
$ git add . 
$ git commit 
```

* git init         - 初始化仓库。
* git add .      - 添加文件到暂存区。
* git commit  - 将暂存区内容添加到仓库中。

### 1.1. 创建仓库命令

下表列出了 git 创建仓库的命令：

| 命令      | 说明                                   |
| --------- | -------------------------------------- |
| git init  | 初始化仓库                             |
| git clone | 拷贝一份远程仓库，也就是下载一个项目。 |

### 1.2. 提交与修改

Git 的工作就是创建和保存你的项目的快照及与之后的快照进行对比。

下表列出了有关创建与提交你的项目的快照的命令：

| 命令       | 说明                                     |
| ---------- | ---------------------------------------- |
| git  add   | 添加文件到仓库                           |
| git status | 查看仓库当前的状态，显示有变更的文件。   |
| git diff   | 比较文件的不同，即暂存区和工作区的差异。 |
| git commit | 提交暂存区到本地仓库。                   |
| git reset  | 回退版本。                               |
| git rm     | 删除工作区文件。                         |
| git mv     | 移动或重命名工作区文件。                 |

### 1.3. 提交日志

| 命令             | 说明                                 |
| ---------------- | ------------------------------------ |
| git log          | 查看历史提交记录                     |
| git blame <file> | 以列表形式查看指定文件的历史修改记录 |

### 1.4. 远程操作

| 命令       | 说明               |
| ---------- | ------------------ |
| git remote | 远程仓库操作       |
| git fetch  | 从远程获取代码库   |
| git pull   | 下载远程代码并合并 |
| git push   | 上传远程代码并合并 |

参考：

> https://www.runoob.com/git/git-basic-operations.html

### *NOTE:*

## 2、git 通过 ssh 连接 github

```
https://github.com/...
git@github.com:...
```

这两个地址展示的是同一个项目，但是这两个地址之间有什么联系呢？
前者是https url 直接有效网址打开，但是用户每次通过git提交的时候都要输入用户名和密码，有没有简单的一点的办法，一次配置，永久使用呢？当然，所以有了第二种地址，也就是SSH URL，那如何配置就是本文要分享的内容。

> GitHub配置SSH Key的目的是为了帮助我们在通过git提交代码是，不需要繁琐的验证过程，简化操作流程。

### 2.1. 设置 git 的 user name 和 email

如果你是第一次使用，或者还没有配置过的话需要操作一下命令，自行替换相应字段。

```
git config --global user.name "yourname"
git config --global user.email "your@email.com"
```

> 说明：git config --list 查看当前Git环境所有配置，还可以配置一些命令别名之类的。
>
> ​           git config --global xxx : 用户级配置（~/.gitconfig）
>
> ​           git config xxx：当前项目配置文件有效（）

### 2.2. 检查是否存在 SSH Key

```
cd ~/.ssh
ll
```

如果存在 id_rsa 和 id_rsa.pub 文件，说明已经有 SSH Key。如果没有则执行下面的命令：

```
ssh-keygen -t rsa -C "your@gmail.com"
```

> 邮箱与 git he github 没有太大关系，随便填。

### 2.3. 获取 SSH Key

```
cat id_rsa.pub
```

拷贝密钥 ssh-rsa 开头

### 2.4. GitHub 添加 SSH Key

* GitHub点击用户头像，选择setting

* 新建一个SSH Key

* 取个名字，把之前拷贝的秘钥复制进去，添加就好啦。

### 2.5. 验证和修改

测试是否成功配置SSH Key

```
ssh -T git@github.com
```

结果：Hi yangdiankun! You've successfully authenticated, but GitHub does not provide shell access 表示成功。

之前已经是https的链接，现在想要用SSH提交怎么办？
直接修改项目目录下 `.git`文件夹下的`config`文件，将地址修改一下就好了。

### 2.6. 创建本地仓库并连接 github

* 创建需要管理的文件目录，如下：

```
mkdir git_learn
cd git_learn
echo "# test" >> README.md
```

* 初始化本地库并提交到本地库

```
git init
git add README.md
git commit -m "first commit"
git branch -M main
```

* 连接 github 仓库并提交到远程库

```
git remote add origin git@github.com:yangdiankun/test.git
git push -u origin main
```

### *NOTE:*

## 3、Git clone、pull、fetch、push用法及区别

### 3.1. git clone

git clone顾名思义就是将其他仓库克隆到本地，**包括被clone仓库的版本变化**。

因为是clone来的，所以.git文件夹里存放着与远程仓库一模一样的版本库记录。clone操作是一个从无到有的**克隆**操作，再次强调不需要`git init`初始化。

**git clone的用法：**

```
git clone <版本库url>
```

这样就会在本地生成一个目录，该目录与远程仓库同名。

如果本地目录不想与远程仓库同名怎么办？将目录名作为`git clone`命令的第二个参数：

```
git clone <版本库url> <本地目录名>
```

### 3.2. git pull

git pull是拉取远程分支更新到本地仓库的操作。事实上，git pull是相当于从远程仓库获取最新版本，然后再与本地分支merge（合并）。

即：`git pull = git fetch + git merge`

> 注：git fetch不会进行合并，执行后需要手动执行git merge合并，而git pull拉取远程分之后直接与本地分支进行合并。更准确地说，git pull是使用给定的参数运行git fetch，并调用git merge将检索到的分支头合并到当前分支中。

**git pull的用法：**

```
git pull <远程主机名> <远程分支名>:<本地分支名>
```

**举例：将远程主机origin的master分支拉取过来，与本地的branchtest分支合并。**

```
git pull origin master:banchtest
```

如果将冒号和后面的branchtest去掉，则表示将远程origin仓库的master分支拉取下来与本地**当前分支**合并。

以上的git pull操作如果用git fetch来表示：

```
git fetch origin master:banchtest
git merge banchtest
```

> 相比起来，`git fetch`更安全也更符合实际要求，因为可以在merge前，我们可以查看更新情况，根据实际情况再决定是否合并。

### 3.3. git fetch 更新远程代码到本地仓库

理解 fetch 的关键，是理解 FETCH_HEAD，FETCH_HEAD指的是：某个branch在服务器上的最新状态。这个列表保存在 .Git/FETCH_HEAD 文件中，其中每一行对应于远程服务器的一个分支。
当前分支指向的FETCH_HEAD, 就是这个文件第一行对应的那个分支。
一般来说, 存在两种情况：

- 如果没有显式的指定远程分支, 则远程分支的master将作为默认的FETCH_HEAD
- 如果指定了远程分支, 就将这个远程分支作为FETCH_HEAD

**git fetch 更新本地仓库的两种用法：**

 方法 1：

```
git fetch origin master            #从远程的origin仓库的master分支下载代码到本地的origin master
git log -p master..origin/master  #比较本地的仓库和远程仓库的区别
git merge origin/master            #把远程下载下来的代码合并到本地仓库，远程的和本地的合并
```

方法 2：

```
git fetch origin master:temp            #从远程的origin仓库的master分支下载代码到本地并新创建一个分支temp
git diff temp                           #比较master分支和temp分支的不同
git merge temp                          #合并temp分支到master分支
git branch -d temp                      #删除temp
```

### 3.4. git push

**git push** 命用于从将本地的分支版本上传到远程并合并。

命令格式如下：

```
git push <远程主机名> <本地分支名>:<远程分支名>
```

如果本地分支名与远程分支名相同，则可以省略冒号：

```
git push <远程主机名> <本地分支名>
```

**举例：以下命令将本地的 master 分支推送到 origin 主机的 master 分支。**

```
git push origin master
```

相当于：

```
git push origin master:master
```

如果本地版本与远程版本有差异，但又要强制推送可以使用 --force 参数：

```
git push --force origin master
```

删除主机的分支可以使用 --delete 参数，以下命令表示删除 origin 主机的 master 分支：

```
git push origin --delete master
```

### *NOTE*:

## 4、修改 commit 提交注释

amend:修正修订的意思，那么`git commit --amend`这个命令就可以这么理解：对**之前**的commit 提交进行修改。事实上确实如此，不仅可以修改提交的内容，还可以修改commit 信息。
 **注意：这里的之前指最近的commit，而且没有push到远程**。

修改提交的内容分为2种情况：

1. 提交了代码之后,又有新的改动，不想创建两个commit
2. 发现一个地方改错了，下次提交时不想保留上一次的记录

这时就可以使用`git commit --amend`命令把新的内容添加到之前的commit里面，这个命令没有添加新的提交，而是用新提交取代了原始提交。

### 4.1. 还没将代码push到远程仓库，还在本地的仓库中

#### 4.1.1. 修改最后一次注释

如果只想修改最后一次注释（就是最新的一次提交）

* step1：执行以下命令：

```
git commit --amend
```

* step2: 出现有注释的界面（你的注释应该显示在第一行）， 输入`i`进入修改模式，修改好注释后，按`Esc`键 退出编辑模式，输入`:wq`保存并退出。ok，修改完成。

> step1~step2可简写为 `git commit --amend -m "要修改的内容"`

#### 4.1.2. 修改之前的某次注释

* step1：输入以下命令

```
git rebase -i HEAD~2
```

最后的数字2指的是显示到倒数第几次比如这个输入的2就会显示倒数的两次注释（最上面两行）

* step2： 想修改哪条注释就把哪条注释前面的`pick`换成`edit`。方法就是上面说的编辑方式：`i`->编辑，把`pick`换成`edit`->`Esc`->`:wq`

* step3： 接下来的步骤Terminal会提示，然后输入以下命令：

```
git commit --amend
```

* step4：修改注释，保存并退出后，输入：

```
git rebase --continue
```

其实这个原理我的理解就是先版本回退到你想修改的某次版本，然后修改当前的commit注释，然后再回到本地最新的版本。

#### 4.1.3. 修改之前的某几次注释

修改多次的注释其实步骤和上面的一样，不同点在于：

* step1： 同上。
* step2：你可以将**多个**想修改的commit注释前面的`pick`换成`edit`
* step3： **依次修改**你的注释（顺序是从旧到新），Terminal基本都会提示你接下来的操作，每修改一个注释都要重复上面的3和4步，直到修改完你所选择的所有注释。

### 4.2. 已经将代码push到远程仓库

* step1：首先，你把最新的版本从远程仓库先pull下来，修改的方法都如上:

```
git pull origin main:main
```

* step2：最后修改完成后，强制push到远程仓库：

```
git push --force origin main
```

>**注：很重要的一点是，你最好保证在你强制push之前没有人提交代码，如果在你push之前有人提交了新的代码到远程仓库，然后你又强制push，那么会被你的强制更新覆盖！！！**

* step3：最后，可以检查一下远程的提交记录~~ 

### *NOTE:*

1. `git commit --amend`会把暂存区的文件自动加入，可以使用-a把工作区的文件也一起加入。
2. 可以使用`git commit --amend -m "提交描述"` 修改comment。

## 5、Git工作区、暂存区和版本库

### 5.1. 基本概念

**工作区：**就是电脑里能看到的目录。

**暂存区：**英文叫stage或index，一般存放在.git目录下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

**版本库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![git区](D:\WorkSpace\git_hub\git_learn\leran_note\img\git区.jpg)

* 图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage/index），标记为 "master" 的是 master 分支所代表的目录树。

- 图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。
- 图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。
- 当对工作区修改（或新增）的文件执行 **git add** 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。
- 当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。
- 当执行 **git reset HEAD** 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。
- 当执行 **git rm --cached <file>** 命令时，会直接从暂存区删除文件，工作区则不做出改变。
- 当执行 **git checkout .** 或者 **git checkout  <file>** 命令时，会用暂存区全部或指定的文件替换工作区的文件。*这个操作很危险，会清除工作区中未添加到暂存区中的改动。*
- 当执行 **git checkout HEAD .** 或者 **git checkout HEAD <file>** 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。*这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。*

1.2. 

```
git tag -a v1.0 -m "修订版本"
git 
```

```
origin/HEAD就像一个指针，表示默认分支，你的例子中它指向origin/master，即origin/master是默认分支。

删掉也是可以的：
git remote set-head origin -d

```

```
// 删除本地分支
git branch -d localBranchName

// 删除远程分支
git push origin --delete remoteBranchName
```

### *NOTE:*



## 6、Git config 命令

### 6.1. 配置文件的存储位置

这些变量可以被存储在三个不同的位置：

1. `/etc/gitconfig` 文件：包含了适用于系统所有用户和所有库的值。如果你传递参数选项’—system’ 给 git config，它将明确的读和写这个文件。 

2. `~/.gitconfig` 文件 ：具体到你的用户。你可以通过传递—global 选项使Git 读或写这个特定的文件。

3. 位于git目录的config文件 (也就是 `.git/config`) ：无论当前在用的库是什么，特定指向该单一的库。每个级别重写前一个级别的值。因此，在`.git/config`中的值覆盖了在`/etc/gitconfig`中的同一个值。

* **git config --global  alias 重命名**

```
git config --global alias.dog 'git log --decorate --oneline --graph --all'
```

* **git config --global user 配置用户信息**

```
git config --global user.name "ydk"
git config --global user.email "111"
```

git config --global 


7、回退操作（reset）

7.1. 本地库回退到暂存区(local repository -> index)



7.2. 本地库回退到工作区(local repository -> work space)



7.3. 缓存区回退到工作区(index-> work space)

1. `git reset HEAD filename`回退文件，将文件从暂存区回退到工作区，也可以使用 `git reset filename`。

*note*：不修改文件内容，相当于把文件move到工作区

1. `git reset HEAD^` ：回退版本，一个^表示一个版本，可以多个，另外也可以使用 `git reset HEAD～n`这种形式。

*note*：回退到到指定版本，包括内容修改、文件的添加。

## *注意事项*

* 执行 `git reset --hard <版本>` 后会清空***工作区***和***缓存区***
master 