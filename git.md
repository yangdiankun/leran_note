## 1、git 基本操作


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

#### 1.1. 创建仓库命令

下表列出了 git 创建仓库的命令：

| 命令      | 说明                                   |
| --------- | -------------------------------------- |
| git init  | 初始化仓库                             |
| git clone | 拷贝一份远程仓库，也就是下载一个项目。 |

#### 1.2. 提交与修改

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

#### 1.3. 提交日志

| 命令             | 说明                                 |
| ---------------- | ------------------------------------ |
| git log          | 查看历史提交记录                     |
| git blame <file> | 以列表形式查看指定文件的历史修改记录 |

#### 1.4. 远程操作

| 命令       | 说明               |
| ---------- | ------------------ |
| git remote | 远程仓库操作       |
| git fetch  | 从远程获取代码库   |
| git pull   | 下载远程代码并合并 |
| git push   | 上传远程代码并合并 |

参考：

> https://www.runoob.com/git/git-basic-operations.html

## 2、git 通过 ssh 连接 github

```
https://github.com/...
git@github.com:...
```

这两个地址展示的是同一个项目，但是这两个地址之间有什么联系呢？
前者是https url 直接有效网址打开，但是用户每次通过git提交的时候都要输入用户名和密码，有没有简单的一点的办法，一次配置，永久使用呢？当然，所以有了第二种地址，也就是SSH URL，那如何配置就是本文要分享的内容。

> GitHub配置SSH Key的目的是为了帮助我们在通过git提交代码是，不需要繁琐的验证过程，简化操作流程。

#### 2.1. 设置 git 的 user name 和 email

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

#### 2.2. 检查是否存在 SSH Key

```
cd ~/.ssh
ll
```

如果存在 id_rsa 和 id_rsa.pub 文件，说明已经有 SSH Key。如果没有则执行下面的命令：

```
ssh-keygen -t rsa -C "your@gmail.com"
```

> 邮箱与 git he girhub 没有太大关系，随便填。

#### 2.3. 获取 SSH Key

```
cat id_rsa.pub
```

拷贝密钥 ssh-rsa 开头

#### 2.4. GitHub 添加 SSH Key

* GitHub点击用户头像，选择setting

* 新建一个SSH Key

* 取个名字，把之前拷贝的秘钥复制进去，添加就好啦。

#### 2.5. 验证和修改

测试是否成功配置SSH Key

```
ssh -T git@github.com
```

结果：Hi yangdiankun! You've successfully authenticated, but GitHub does not provide shell access 表示成功。

之前已经是https的链接，现在想要用SSH提交怎么办？
直接修改项目目录下 `.git`文件夹下的`config`文件，将地址修改一下就好了。

#### 2.6. 创建本地仓库并连接 github

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

## 3、Git clone、pull、fetch用法及区别

#### 3.1. git clone

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

#### 3.2. git pull

git pull是拉取远程分支更新到本地仓库的操作。事实上，git pull是相当于从远程仓库获取最新版本，然后再与本地分支merge（合并）。

即：`git pull = git fetch + git merge`

> 注：git fetch不会进行合并，执行后需要手动执行git merge合并，而git pull拉取远程分之后直接与本地分支进行合并。更准确地说，git pull是使用给定的参数运行git fetch，并调用git merge将检索到的分支头合并到当前分支中。

**git pull的用法：**

```
git pull <远程主机名> <远程分支名>:<本地分支名>
```

举例：将远程主机origin的master分支拉取过来，与本地的branchtest分支合并。

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

#### 3.3. git fetch 更新远程代码到本地仓库

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