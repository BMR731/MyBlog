---
title: Git 简要的学习总结
date: 2016-05-15 21:39:01
tags: Git
categories: 其它
---


#本笔记摘录自【原文链接】(http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)<!--more-->

#创建版本库

    初始化一个Git仓库，使用git init命令。

    添加文件到Git仓库，分两步：

    第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；

    第二步，使用命令git commit -m “”，完成。


#时光机穿梭

    git status 查看仓库当前状态
    git diff <file> 查看文件变化

- 版本回退
- 
    git log 查看日志
    git log --pretty=oneline 只显示一行的日志
    git log --graph 可以看到分支合并图
    HEAD表示当前版本，HEAD^表示上一版本，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

    git reset --hard HEAD^ 回退到上一版本
    git reset --hard <commitID> 会退到指定的commit版本
    git reflog 记录你的每一次命令，以便回退到哪个版本

- 工作区和暂存区
- 
    工作区：你在电脑中正常的目录
    版本库: .git Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存 区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

    故，文件的三种存在位置有: 工作区，暂存区，，git分支
    转移的方式是： 工作区---> git add ---> 暂存区 ---> git 分支

- 管理修改
- 
    git版本控制系统管理的是修改，而不是文件

- 撤销修改
- 
    git checkout -- <file> 修改了工作区里的文件，用之丢弃工作区里的修改

    git reset HEAD <file> + git checkout -- <file> 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
- 删除文件 
-
    rm <file> 移除工作区文件
    git rm <file> 移除版本库中的文件
    注意着两种操作都会引起文件状态的不一致
    可用  git checkout -- <file> 等去修改

#远程仓库

- 添加远程库 
-
    1.先在github网站上建立一个仓库

    2.将本地的一个仓库与远程仓库链接起来，远程库的默认名称是origin
    git remote add origin https://github.com/BMR731/test.git  

    3，第一次推送代码
    git push -u origin master

    4.以后推送代码
    git push origin master

- 从远程库克隆
-
    git clone 命令来克隆远程库到本地
    Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

#分支管理

分支的使用意义：分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作

- 创建与合并分支
-
    master git中的主分支，指向提交
    HEAD 始终指向当前分支
    他们都是用的指针的概念

    git branch 查看当前分支,当前的分支前会有*号
    git checkout -b <dev> = git branch <dev> ; git checkout dev 创建并且切换到当前分支
    git merge <dev> 合并dev的分支到当前分支上(默认使用fastforward模式)
    git branch -d <dev> 删除分支

- 解决冲突
- 
    当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

-  分支管理策略
-  
    合并分支的默认模式是Fast Forward模式，此时删除掉分支会完全丢失合并信息
    如果强制禁止使用FF模式，则git会在merge时产生一个新的commit来凸显出分支的信息
    git merge --no--ff -m <description> <dev>

    业内约定：
    master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
    dev分支不稳定，干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
    你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

- Bug分支
- 
    修复bug时，我们会创建新的bug分支去修复，然后合并，删除；
    当手头的工作没有完成时，可以使用
    git stash 保存未提交的工作区
    git stash list 列出保存的现场
    git stash pop 恢复现场


- Feature分支
- 
    软件开发中，总有无穷无尽的新的功能要不断添加进来。
    添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。 

- 多人协作
- 
    当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。
    git remote 查看远程库信息
    git remote -v 详细信息
    并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

    master分支是主分支，因此要时刻与远程同步；

    dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

    bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

    feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。






