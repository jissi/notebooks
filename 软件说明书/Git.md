### Git

<font size=4>分布式版本控制系统，能够记录每次修改过的文本文件</font>

[TOC]

---



### 集中式版本控制系统与分布式版本控制系统的区别

* 集中式需要连接到远程服务器才能工作，就像图书馆，取出来使用后再放回去。
* 分布式没有中央服务器，每一台电脑都是自己的服务器，每个人的电脑都是一个完整的代码仓库

### 安装GIT并注册

```shell
sudo pacman -S git

# 注册
git config --global user.name "jissi"
git config --global user.email "xxx@xx.com"
# 使用 --global参数会将所有仓库都使用该信息
```



### 创建版本仓库（repository）

* repository是一个目录，目录内的文件被git管理，每个文本文件的修改都可以跟踪

* 创建repository

  ```shell
  mkdir learnGit
  cd learnGit
  git init  # 将该目录变成git仓库
  ```

  ```learnGit/.git/ ```目录是用来管理仓库的，不要修改该目录文件、
  
* 添加文本到仓库

  ```shell
  cd ~/learnGit
  echo "git test" >> test.txt
  git add test.txt # 添加文件到仓库
  # 可以再add 多个文件，然后一起提交
  git commit -m "说明" # 提交 -m 后接说明信息
  ```

### 常规使用

* 修改文件并查看修改

  ```shell
  #修改test.txt的内容
  ...
  git status		#查看仓库状态，那些文件被修改了
  git diff test.txt	#查看文件的具体改动
  git add test.txt	#提交修改
  git status			#提示要提交的修改包括test.txt
  git commit -m "第一次修改"
  git status 			#提示是否还有要提交的文件
  ```

* 版本回滚

  ```shell
  git log		#查看改动记录 --pretty=ontline 只显示最近一次
  # commit id是提交版本号
  #使用HEAD 表示当前版本，HEAD^ 表示上一个版本 HEAD^^上上个
  git reset --hard HEAD^ ##回滚到上一个版本
  git log		#记录已经修改，无法直接再返回到回滚前的版本
  ```

  ```shell
  # 在能够看到commit id的情况下，回到回滚前的版本的方法
  git reset --hard commitID的前几个字 #git会自动补全
  ```

  ```shell
  # 窗口被关闭，看不到回滚前的commit id ，版本回到回滚前的方法
  git reflog		#查看执行过的命令记录，含有commit id
  ```

  <font color="green" size=3>git的HEAD是指向当前版本的指针，回退版本就是移动指针</font>

### 工作区与暂存区

* 工作区 (Working Directory)

  ```shell
  能够看到的目录 如最开始新建的learnGit目录就是工作区
  ```

* 版本库 (repository)

  ```shell
  工作区内的隐藏目录  .git 是Git的版本库
  ```

  版本库里存放了```stage/index```的暂存区，还有git自动创建的分支```master```，以及指向master的指针```HEAD```

  ```shell
  git add		把文件加入stage
  git commit	把stage的所有内容提交到当前分支
  ```

### git管理的是修改，而不是文件本身

* **每次使用 git add file  是把本次修改存入暂存区，git cmmit 提交的是暂存区的修改**

* 撤销修改

  ```shell
  
  1. 修改后还没有放到暂存区————回到于版本库一致的状态
  2. 已添加到暂存区，又修改————回到刚放到暂存区时的状态
  git checkout -- test.txt #撤销本次修改，
  # 回到最近一次 git commit 或者 git add 时的状态
  # -- 参数不能省略
  3.撤销 git add
  git reset HEAD test.txt #撤销位于暂存区，放回工作区
  
  ```

* 删除文件

  ```shell
  rm test.txt	#直接删除文件
  #git知道删除了文件，工作区与版本库不一致
  git status	#提示文件被删除
  git rm test.txt	#确实要从版本库中删除文件
  git commit -m "删除了test.txt" #提交到分支
  git checkout -- test	#误删，回到最新版本的文件
  ```

### 远程仓库

<font size=4>Git是分布式版本控制系统，同一个Git仓库可以分布到不同的机器</font>

* github提供git仓库托管

  ```shell
  1.#先注册github账户
  2.#创建SSH Key
    #主目录下 .ssh目录 ：id_rsa(私钥) 和 id_rsa.pub(公钥)  
    #如果没有：
    ssh-keygen -t rsa -C "xxx@xxx.com"
  3.#登录github,添加ssh key 填写公钥内容
  ```

  github需要识别是否是本人提交的推送，只有私钥才可以给对应公钥提交推送

  ！！！ 别人可以看到你的github

* 添加远程仓库

  ```shell
  1.#在github新建仓库
  2.#本地仓库与远程仓库关联
    git remote add origin git@github.com:jissi/GitRepository.git
    # 关联完成后，远程库的名称就是 origin
  3.#把本地库的所有内容推送到远程库
    git push -u origin master  #把当前分支master推送到远程库
    #第一次使用远程库是空的，使用 -u 把本地master分支内容推送到远程新的master分支并关联
    #第一次使用会有警告，要求确认
   4. git remote # 显示远程仓库信息 ， -v 显示详细信息 
  ```

  ```shell
  git push origin master	#把本地master最新的修改推送到github
  # 也可以推送其他分支
  git pull 				# 从远程仓库抓取当前分支
  # pull 提示本地分支没有与remote的分支关联
  git branch --set-upstream-to=origin/dev dev	#将本地dev分支与远程关联
  ```

* 从远程克隆仓库到本地

  ```shell
  git clone git@github.com:jissi/clone.git
  ```

### 分支管理

<font size=4>可以在不同分支上开发不同的功能，最后将分支合并。团队合作时，既能保证自己的文件安全，又不影响其他人工作</font>

<font  size=4>每个分支可以理解为不同的平行宇宙，但是可以合并</font>

* 创建与合并分支

  默认的master分支是主分支，HEAD指向当前分支，分支指向提交，每次提交master前移一步，随着不断提交，master分支线越来越长

  ```shell
  git branch dev		#仅创建分支
  git branch			#显示所有分支，当前分支带有*
  git branch -d dev	#删除dev分支
  git checkout -b dev	#新建dev分支 -b 表创建并切换
  git switch -c dev	#创建并切换分支
  git checkout master	#切换到master分支
  git switch master	#切换到master分支
  git merge dev		#将dev分支合并到当前分支
  ```

* 分支冲突

  <a name="c"></a>
  
  ```shell
  在两个分支上对同一文件做不同的修改合并时就会发生冲突
  修改文件为最终样式，再次提交合并
	git log --graph	--pretty=oneline --abbrev-commit#查看分支合并图
  ```
  
* 分支管理策略

  ```shell
  # 将master作为主分支来发布最新版（稳定）
  # 工作时在其他分支工作（不稳定）
  # 合并分支时，git默认使用 fast forward 模式，该模式下删除分支后会丢掉分支信息
  git merge --no-ff -m "提示信息"	#本次合并要创建commit，所以使用 -m
  # 使用 --no-ff 使用普通模式合并，可以查出来做过合并，fast forward模式的合并查不到
  
  ```

* 暂时还不能提交的分支的保存

  <font size=3>当前分支上的文件没修改完不方便提交，又接到位于其他分支的需要立即修改的文件</font>

  ```shell
  git stash		#存储工作现场
  git status		#查看不到刚刚的文件
  # 在master下新建分支，处理急需处理的文件，处理完在将其合并到master
  git stash list	#列出存储的工作现场
  git stash apply	#仅恢复工作区
  git stash drop	#删除存储的工作区
  git stash pop	#恢复工作区并删除存储的工作区
  ```

  <font size=3>要立即修改的文件也位于本分支（dev也是从master分出来的）</font>

  ```shell
  # 同样的修改，只需把提交的修改copy到本分支
  git cherry-pick	commitID的前几位	#复制提交到本分支
  ```

* 开发新功能最好新建分支feature

  ```shell
  # feature分支上的功能开发完毕，但是不再需要，需要就地删除
  git branch -D feature	#使用 -D 强制删除
  # 使用 -d 会提示当前分支未合并
  ```

* 多人协作

  ```shell
  # 多人协作，别人先将他的修改提交到github的dev
  # 自己再推送自己的dev分支，报错，dev分支冲突
  # git branch --set-upstream-to=origin/dev dev 将本地dev与远程dev关联
  git pull #将别人的推送拉取到本地
  ```

  解决[冲突](#c)

* Rebase

  ```git log –graph –pretty=oneline abbrev-commit```使用 HEAD->master 和 origin/master分别标识了本地和远程的最新版本

  ```shell
  git rebase	#把分支的提交历史整理成一条直线，但是本地的分支提交被修改了
  ```

### 使用标签

* <font size=4>使用标签来标记版本号（使用v2.0 来替代 commit afaf）</font>

  ```shell
  git tag			#查看所有标签，以字母顺序显示
  git tag v2.0	#给当前版本打标签，默认最新的commit
  git tag -a v2.1 -m "2.1版本"	# -a指定标签名，-m 指定说明
  git tag v1.0 ab241	#给指定的commit打标签
  git show 1.0	#查看指定版本
  git tag -d v2.1	#删除标签
  git push origin v2.0	#将标签推送到远程
  git push origin --tags	#推送所有标签到远程
  git push origin :refs/tags/v2.1	#将标签删除推送到远程
  ```

  

### Github Fork

* 在github上复制别人的仓库到自己的账户  fork

  ```shell
  先将别人的仓库fork到自己的账户（这样才有权修改）
  克隆到本地，并操作
  向已经fork的仓库push
  在github上发起 pull request 就可以向官方提交验证
  ```

### 国内的 码云 托管

* 使用方法与github类似

  ```shell
  git remote add origin git@gitee.com:jissi/repository.git
  ```

### 自定义git配置

* 配置文件位于：```.git/config```

* 配置user.name 和 user.email

* global参数表示全局配置

* 显示颜色

  ```shell
  git config --global color.ui true
  ```

* 忽略特殊文件(如不能提交的隐私文件) 

  ```shell
  # git工作区根目录下的 .gitignore文件记载需要忽略的文件
  添加 *.py *.doc等需要忽略的文件
  提交 .gitignore
  ```

* 命令别名

  ```shell
  git config --global alias st status	#为status命令设置别名 st
  修改 .git/config 文件[alias] 删除别名
  ```

  