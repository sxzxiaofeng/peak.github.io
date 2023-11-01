---
title: git命令(从菜鸟到老鸟)
date: 2022-08-04 15:49:53
tags: Git
type: "tags"
---
- git的配置
  - 第一次安装git需要配置用户名和邮箱
  - `git config --global user.name 'xxx'`
  - `git config --global user.email 'xxx@xxx.com'`
- git的三区
  - 工作区
    - 开发代码的地方
    - 新建/修改/删除文件出现再工作区
  - 暂存区
    - 暂时保存代码的地方
  - 版本区
    - 代码版本控制的地方
- 指令
  - `git init` 初始化一个空的git仓库（只要做一次，第二次会报错）
  - `git add a.js` 将位于工作区的a.js文件添加到暂存区
   - `git add ./ */ -A` 将工作区全部文件添加到暂存区
  - `git commit -m '注释'` 将暂存区全部代码添加到版本区进行版本控制
  - `git status` 查看当前文件的状态
    - 红色：说明文件位于工作区
    - 绿色：说明文件位于暂存区
    - 没有颜色/显示：说明文件位于版本区
  - git diff 对比（工作区和暂存区）文件差异
    - git diff --cached 暂存区和版本区
    - git diff master 工作区和版本区
- 版本回退
  - git log 查看版本历史记录
  - git reflog 查看简版的历史纪录
  - git reset --hard HEAD^ 回退到上一个版本
  - git reset --hard commit_id 回退指定commit_id的版本
- 分支操作
  - git checkout master 切换到master分支
  - git branch dev 新建dev分支
  - git checkout -b dev 新建并切换到dev分支（将当前分支内容复制到dev分支上）
  - git branch 查看当前有哪些分支
  - git branch -r 查看当前所有分支
  - git merge dev 在当前分支合并dev分支内容

##### 相关需求场景的操作
- **需求1**：远程github已经创建一个仓库，需要将本地项目托管到远程github仓库中  
  - git init     
  - git add .  
  - git commit -m "first commit" 
  - **git remote add origin** https://github.com/sxzxiaofeng/test.git 将本地仓库和远程仓库关联起来   
  - git remote rm(remove) origin* 删除关联的远程仓库  
  - **git push -u origin master** 将本地仓库master分支的内容推送到远程仓库去    首次需要加上-u,后面就不需要了~
  
- **需求2**：远程有仓库并且有内容，下载到本地来使用  
- **git clone** https://github.com/sxzxiaofeng/test.git  
- 自动关联了，不需要关联, 克隆下来的仓库默认只有master分支。希望获取dev分支内容   
- **git fetch origin dev1:dev2**    
- 拉取远程仓库dev1分支的内容到本地的dev2分支上

 - **需求3**：本地仓库有内容，远程有更新，本地拉取更新的内容   
- **git pull origin master** 拉取远程仓库的master分支合并到本地master分支上
   **注意**： 不管将来push、pull操作，都得先进行本地版本控制   
  - git add .    
  - git commit -m 'xxx'



##### 团队开发git操作流程

  - 1.克隆仓库到本地 git clone https://github.com/sxzxiaofeng/test.git 

  - 2.建立连接 git remote add origin https://github.com/sxzxiaofeng/test.git 

  - 3.创建并切换分支 git checkout -b dev

  - 4.将工作区文件提交到暂存区git add *

  - 5.将暂存区代码添加到版本区进行版本控制 git commit -m '注释'

  - 6.和远程仓库建立连接,并把dev分支上的代码推送到远端 
  - git push --set-upstream origin dev (第一次连接需要输入密码)，以后是git push origin dev

#### git操作 

    git  pull —rebase origin master  把当前提交的commit移到分支的最顶端
    git revert b3ce1c2e(版本号)  反转后重新提交，去除掉当前commit
    使用 git revert <commit> 可以撤销指定的提交， 要撤销一串提交可以用 <commit1>..<commit2> 语法。 
    注意这是一个前开后闭区间，即不包括 commit1，但包括 commit2。
    git revert --no-commit f7742cd..551c408 
    git commit -a -m 'This reverts commit 7e345c9 and 551c408'

    git  cherry-pick b3ce1c2e   新建分支，把当前commit挑拣到新的分支，只提交自己需要的commit

    如果你只想修改最后一次注释（就是最新的一次提交），那好办：
    git commit --amend
    git commit --amend -m 'xxxx' // 覆盖上次commit信息

    出现有注释的界面（你的注释应该显示在第一行）， 输入i进入修改模式，修改好注释后，按Esc键 退出编辑模式，
    输入:wq保存并退出。ok，修改完成。

    查看提交历史图形
    git log --graph --pretty=oneline --abbrev-commit(简写commit版本好)

    使用--no-ff 参数禁用快速合并模式
    合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并
    就看不出来曾经做过合并。
    git merge  --no-ff  -m "merge with no-ff"  dev

- Git pull提示需求输密码
  - Enter passphrase for key ：
  - 解决方案： ssh-add -k /Users/m/.ssh/id_rsa      //后面这个是你放置私钥的目录



