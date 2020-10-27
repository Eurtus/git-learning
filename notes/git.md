### Git操作

#### Git底层命令

##### Git 安装

> 		推荐[官网](https://www.git-src.com)        或者自己找资源

##### 基本配置

- 安装后查看命令： ` git --version `

- 用户名：`git/config --global user.name [name]`

- 用户邮箱：`git/config --global user.email [e-mail]`

- 查看配置：`git --list`

  ---

  >  因为Git是要用Git Bash来操作比较方便，所以要学习一些Linux常用命令
  
  ---

##### Linux基本命令

- `clear`	清屏·

- `echo "string"`	向控制台输出信息

- `ll`	将当前目录下的文件及子文件子目录目录铺列到控制台

- `find`	将指定文件夹下的文件目录和子孙文件及子孙目录铺列到控制台

- `find -type f`	将指定目录下的指定文件铺列到控制台

- `rm`	删除文件

- `mv`	重命名文件

- `cat`	查看文件

- `vim`    打开vim编辑器

  ---



##### 区域

###### **工作区**

- 初始化仓库：`git init`

###### **暂存区**

- 查看暂存区：`git ls-files -s`

###### **版本区**

---



##### 对象

###### **git对象**

> Git的核心部分是一个简单的键值对数据库。你可以向数据库插入任意类型的内容，它会返回一个键值，通过该键值可以在任意时刻再次检索该内容。

- 构建git对象

  - 向数据库写入内容，并返回相应键值。 命令：`echo “string” | hash-object -w --stdin`

    >  ` -w`表示写入数据库，`--stdin`表示从标准输入读入 ,可以是==文件名==
- 查看git对象
  - 	根据键值查看相应内容。 命令：`git cat-file -p [key]`

    > `-p`查看文件内容
    >
    > `-t`是查看git对象的类型  		
    >
    > git对象的类型为`blob`
    
    

<font color=blue>Question: </font> 

> 1. 记住每一个文件所对应的键值并不现实
>
> 2. 在git中，文件并没有被保存，只保存了文件内容
>
> 3. 当前操作都是在本地数据库进行，没有涉及到缓冲区
>
> ​    <font color=red>**解决方案：树对象**</font>



###### **树对象**

> 数对象，它能解决文件名保存的问题，也允许我们将多个文件组织到一起。Git以一种类似于Unix中的目录项，数据对象（git对象）则对应文件内容。一个树对象包含了一条或多条记录（每条记录含有一个指向git对象或者子树对象的键值，以及相应的模式、类型、文件名信息）一个树对象也可以包含另一个树对象。

- 建立数对象
  - 建立一个树对象，并添加到暂存区  
  
    > 命令：`git update-index --add --cacheinfo [FileType] [key] [name]`
    >
    > `--add`:   第一次应该添加add
    >
    > `--cacheinfo`​： 因为要添加的数据在​Git数据库中，而不是当前目录下，所以需要cache info
    >
    > 文件模式[file type]  ：         
    >
    > ​				`100644`: 普通文件
    >
    > ​				`100755`:可执行文件
    >
    > ​				`120000`:符号链接
    
  - 查看暂存区：`git ls-files -s`
  
  
  
- 添加树对象
  
  - 将暂存区中的树对象添加到版本库中  
  
    > 命令：`git write-tree [key]`
    
    
  
- 修改树对象
  
  - 将第一个树对象加入到第二个树对象，使其成为新的树对象（也可以将树对象读入到暂存区）
  
    > 命令：`git read-tree --prefix=back [tree object key]`
  
  <font color=blue>Question: </font> 
  
  > 树对象分别代表了不同项目的快照，然而问题依旧，想要利用这些快照，就必须记住这三个对象的键值。并且在协同工作室也不知道是谁保存了这些快照，在什么时候存的，以及为什么要保存这些快照。而以上这些，就是提交对象所保存的基本信息。
  
  

###### **提交对象**

- 创建提交对象

>提交对象有父子关系，需要链接起来

  >创建第一个提交对象 命令：`echo "your discription" | git commit-tree [tree object key]`
  >
  >后序提交对象 命令：`echo "your discription" | git commit-tree [tree object key] -p [last commit object key]`
  >
  >

- 查看树对象

  > 命令：`git cat-file -p [commit object key]`


<font color=red> git commit-tree不仅生成提交对象，而且会将树对象提交到本地库中</font>

```php+HTML
总结：树对象为版本快照，提交对象为版本
```



---

#### 高层命令

##### 基本流程(CRUD)

- 初始化新仓库：

  ```bash
  git init 
  ```

###### 增

- 将工作区中的文件存入版本库，再从库中读到暂存区:

  ```bash
  git add ./
  ```

- 将暂存区中的树对象提交到版本库，并生成相应的提交对象:

  ```bash
  git commit 
  -m "discription"
  -a 将跟踪文件跳过暂存区，直接提交到版本库
  ```

###### 查

- 检查当前文件状态：

  ```bash
  git status
  ```

- 查看文件状态（比`git status`更加详细）：

  ```bash
  git diff 	查看哪些修改还没被暂存
  --staged	查看哪些被暂存了，还没提交
  ```
  
- 查看提交的历史记录：

  ```bash
  git log --pretty=oneline
  git log --oneline
  git reflog 		---查看所有操作
  ```
###### 改

  > 工作区文件可以任意修改

- 文件改名（工作目录中的文件改名，并将修改添加到暂存区）

  ```bash
  git mv [old-file] [new-file]
  ```
###### 删
- 文件删除（删除工作目录中相对应的文件，并将修改添加到暂存区）

- ```bash
  git rm [file-name]
  ```



  

  

##### 分支操作

>几乎所有的版本控制系统都以某种形式支持分支。使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线。Git的分支模型是轻量高效的，这也是特色之一。

###### 创建分支

```bash
git branch ---查看分支列表
git branch [name] ---创建一个分支
git branch [name] [commit-hash] ---创建一个分支，并指向指定提交对象
```

###### 切换分支

>  改动三个地方
>
> ​		HEAD
>
> ​		暂存区
>
> ​		工作目录
>
> 坑：在切换分支时，如果当前分支上有未暂存的修改（第一次）或者 有未提交的暂存（第一次）分值可以切换成功，但是 可能会污染其他分支
>
> 最佳操作：<font color="red">每次切换分支前，当前分支一定是干净的（已提交状态）</font>

```bash
git checkout [name]		---切换到指定分支
git checkout -b [name]	 	---创建一个新分支并切换到该分支 
```

###### 删除分支

```bash
git branch -D [name]  ---强制删除分支
```

###### 查看分支

```bash
git btanch -v   ---查看当前分支的最后一次提交对象
git log --oneline --decorate --graph --all	---查看所有分支历史
```
###### 合并分支

```bash
git merge [name]		---当前分支合并指定分支（快进合并）
```

###### 冲突

```
打开冲突文件，修改，然后 git add ./   完成
```

###### Git存储

> 有时，你在项目的一部分上已经工作一段时间后所有东西进入了混乱的状态，而这是你想要切换到另一个分支做一些别的事情。你并不想为了过会儿回到这一点而为坐了一般的工作创建一次提交。

```bash
git stash		---将未完成的修改保存到一个栈上
git stash apply		---应用存储（应用栈顶元素）
git stash list		---查看存储
git stash drop		---加上将要移除的储藏名字移除它
git stash pop		---应用储藏，然后丢掉它
```

###### Git操作配别名

```bash
Git config --global alias.[new-name] [from-name]
```

##### 撤销和重置

###### 工作区

>如何撤回自己在工作目录中的修改
>
>```bash
>git checkout --[file-name]
>```

###### 暂存区

> 如何撤回自己的暂存

```bash
 git reset HEAD [file-name]
```

###### 版本库

> 如何撤回自己的提交

```bash
git commit --amend		---修改注释
```





#### Git&GitHub

##### 建立连接

- 注册GitHub账号

- 下载Git

- 在gitbash中操作如下命令(# 后为注释)
  
  > GitHub用SSH等服务进行远程仓库通信
  
  1.配置Git基本的信息

```bash
#GitConfig 
git config --global user.name "your name"
#配置用户名
git config --global user.email "your email"
#配置用户邮箱（邮箱和GitHub注册邮箱一致）
git config --list 
#查看配置		
```

​		2.获取SSH key

```bash
#Get SSH key
ssh-keygen -t rsa -C "your email"
#输入这条指令后第一个交互会让你输入存储key的路径，回车即为默认
#第二个和第三个为密码和再输入密码确认
#成功后会得到两个文件 （id_rsa 私钥）	（id_rsa.pub 公钥）
```

​		3.GitHub设置

> 1.登陆GitHub后进入Settings
>
> 2.在Setting列表中选中SSH and GPG keys
>
> 3.新建一个SSH key
>
> 4.在tietle中可自行填写描述，key中填写公钥（即id_ras.pub中的内容）
>
> add SSH key 后可以在邮箱收到成功信息



​		4.建立连接

> ```bash
> ssh -T git@github.com
> #建立连接
> ```



