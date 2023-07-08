# Git的用法


**如何在idea同步远端代码？**
1. 先fetch（即将远程代码下载到本地的一个Branch上：origin/master），再merge
2. 直接pull(= fetch + merge)

> merge的时候，有可能遇到冲突

**如何还原本地代码到上一个版本？**
1. 右键项目->Git-> Compare With Branch->选择origin/master
2. 将修改过得地方进行还原

**常见操作解释**
* fetch:拉取远程分支到本地
* merge:合并远程分支到当前分支
* rebase:改变分支的“基底”到最新的地方
* commit:将暂存区修改提交到当前分支
* add:将本地修改添加到暂存区
* reset:将当前分支回退到某个版本
* revert:将当前分支回退到某个版本，并提交为一个新的节点
* checkout:切换到某个分支
* stash:用于功能开发到一半时接到紧急需求，需要切换到别的分支，先开发紧急的需求。
> 因为若你工作区仍有未提交的代码，此时是不允许切换分支的。
> 此时，使用stash可以清空你的工作区，并将未提交的代码“缓存”起来。之后你就可以切换分支去开发其他需求。
> 当然，开发完其他需求后，记得切换回到这个分支，使用[]命令将代码从缓存区还原。
* cherry-pick:将A分支上的某个提交或某些提交移植到B分支上


## 参考资料

git book:[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)

## 使用场景

#### 初入项目，使用git第一步：生成git本地仓库

**方法1：直接生成**
```bash
#先创建一个文件夹（例如D:/Test）
#进入Test文件夹后执行以下命令
git init
```

**方法2：从远程仓库克隆**
```bash
# 用clone，直接拷贝下来
    
    git clone http://xxx.xx.xx/a/b.git

#clone会将远程仓库几乎所有数据都下载到本地仓库（包括所有分支、标签）。
#现在，假设远程仓库只有一个主分支master。clone成功后，本地会生成两个分支：

#001结果：
#0. 工作台：C
#0. 暂存区：C
#1. master          A -> B -> C（HEAD指针在此）
#2. origin/master   A -> B -> C

#A、B、C表示远程仓库有3次提交记录。
```

#### 保持本地代码最新：通过fetch、merge从远程仓库获得最新的代码

**拉取（fetch）远程仓库的修改**
```bash
#本地已经有代码，想拉取远程最新的代码：
#用fetch
    
    git fetch origin

#002结果：
#0. 工作台：C
#0. 暂存区：C
#1. master          A -> B -> C（HEAD指针在此）
#2. origin/master   A -> B -> C -> D（来自远程新的提交）
```

**合并（merge）远程代码到本地**

```bash
    
    git checkout master 
    git merge origin/master

#003结果：
#0. 工作台：D
#0. 暂存区：D
#1. master          A -> B -> C -> D（HEAD指针在此）
#2. origin/master   A -> B -> C -> D
```



#### 开始自己的开发：创建文件、暂存（add）文件、提交（commit）
```bash
#你修改了某个文件的内容

#004结果：
#0. 工作台：E(被修改的文件，状态变为：MODIFIED）
#0. 暂存区：D
#1. master          A -> B -> C -> D（HEAD指针在此）
#2. origin/master   A -> B -> C -> D
```

```bash
#暂存（add）文件，并使其纳入版本管理。
    
    git add text.txt

#005结果：
#0. 工作台：E（所有文件状态：UNMODIFIED）
#0. 暂存区：E(ADD后，将工作台的修改放到了暂存区，等待commit）
#1. master          A -> B -> C -> D（HEAD指针在此）
#2. origin/master   A -> B -> C -> D
```

```bash
#提交（commit）该文件到当前分支（master）。
    
    git commit  

#006结果：
#0. 工作台：E（所有文件状态：UNMODIFIED）
#0. 暂存区：E
#1. master          A -> B -> C -> D -> E（HEAD指针在此）
#2. origin/master   A -> B -> C -> D
```

> 补充提交（amend）：
> 若提交后，发现自己漏了几个文件没有提交，或备注写错，可使用以下命令对最新的提交进行补充修改：git commit --amend
> 最终你只会有一个提交。

> 取消暂存（reset），将暂存区的提交恢复至工作区：

```bash
#接着#005结果

    git reset HEAD

#007结果：
#0. 工作台：E（所有文件状态：UNMODIFIED）
#0. 暂存区：D
#1. master          A -> B -> C -> D -> E（HEAD指针在此）
#2. origin/master   A -> B -> C -> D

```



> 取消提交（reset）：
> 通过reset取消提交，有3种方式： mix（默认）、soft、hard


```bash
#接着#005
#取消暂存



```



#### 上传我的代码到远端：push






## 进阶场景

#### 多个运行环境、多个对应的分支

假设项目需要发布到以下这些环境，很自然地，就会为各个环境新建一个分支：
* prod（生产环境）
* uat（用户测试环境）
* sit（集成测试环境）

**分支管理的方式**

方式1：（合分支、发版本由组长来负责）
* 先切换到sit分支（checkout）
* 代码开发
* 开发完毕，先拉取远程代码（pull），然后提交（commit）本地代码并推送（push）
* 部署到sit环境，测试
* 测试通过，开发组长将sit分支代码一起合到uat
* 部署到uat环境，测试
* 测试通过，开发组长将uat分支代码一起合到prod
* 部署到prod环境，版本发布结束，进行下一轮迭代

方式2：（合分支、发版本由开发人来负责）
* 先从sit分支创建一个新的分支dev-dongyz(checkout -b)
* 在新分支进行开发
* 开发完毕，提交dev-dongyz分支代码（commit）
* ------以下是sit环境下的合代码及部署流程
* 切换到sit分支（checkout）,先拉取远程代码（pull），保证代码最新
* 将刚刚在dev-dongyz的提交移植到sit分支（cherry-pick）
* 将sit分支上的新提交推送（push）
* 部署到sit环境，测试
* ------以下是uat环境下的合代码及部署流程
* 切换到uat分支（checkout）,先拉取远程代码（pull），保证代码最新
* 将刚刚在dev-dongyz的提交移植到uat分支（cherry-pick）
* 将uat分支上的新提交推送（push）
* 部署到uat环境，测试
* ------以下是prod环境下的合代码及部署流程
* 切换到prod分支（checkout）,先拉取远程代码（pull），保证代码最新
* 将刚刚在dev-dongyz的提交移植到prod分支（cherry-pick）
* 将prod分支上的新提交推送（push）
* 部署到prod环境，测试

> 推荐使用方式2，即每有一个新需求，就创建一个新分支，并在上面开发。




#### 常见概念

* 提交对象（commit object）

    每次进行提交操作时，会生成一个提交对象，该对象含有以下内容：
    0. 自己的指针
    1. 指向树对象的指针
    2. 作者的姓名、邮箱
    3. 父提交对象的指针

> 每次提交之后，或者在提交历史中见到的commit ID，就是指向提交对象的指针。

* blob对象：暂存文件内容快照

    会为每个需要提交的文件都生成一个blob对象，作为文件快照。该对象含有以下内容：
    0. 自己的指针
    1. 内容快照（即修改了什么内容）

* 树对象

    记录着目录结构、以及本次暂存的所有文件的快照指针（即blob对象）。该对象含有以下内容：
    0. 自己的指针
    1. 指向blob对象的指针


* 怎么查看版本号(commit Id)？

```bash
# 方式一：git log
# 返回的是 commit + 版本号 + 作者 + 日期 + 提交备注
#git log的用法：
# 空格 下一页
# b 上一页
# q 退出查看log
$ git log
commit 8261704a19c28252cabc881903dcdaee32487279 (HEAD -> feature-dyz)
Author: fun_zil <854257920@qq.com>
Date:   Tue Feb 22 11:00:51 2022 +0800
    d.txt

#方式二： git log pretty=oneline
# 只返回版本号 + 提交备注
$ git log pretty=oneline
8261704a19c28252cabc881903dcdaee32487279 (HEAD -> feature-dyz) d.txt
e998f39b012920c9e9016738b204c721c026fa88 Revert "c"
bedb779fbc27ac7d9e882ea1a4daa01be2b0db13 123123444
f53a806024ccc20b88114b1b5013de6892cc5a85 123123
df4aa442166d41e79ee366186ab8f5c6726f8124 c
7f230f4a5e0ba024e69ce2e3ec5bc5f04e0290ac b
52369591e17a94e1e14dda377b25b7e55b6f76fe a

#方式三： git reflog
#返回版本号 + HEAD@(离最新提交版本的距离) + 提交备注
$ git reflog
8261704 (HEAD -> feature-dyz) HEAD@{0}: commit: d.txt
e998f39 HEAD@{1}: revert: Revert "c"
bedb779 HEAD@{2}: checkout: moving from develop to feature-dyz
9551871 (origin/develop, develop) HEAD@{3}: merge origin/develop: Fast-forward
80770d2 HEAD@{4}: checkout: moving from feature-dyz to develop
bedb779 HEAD@{5}: commit: 123123444
f53a806 HEAD@{6}: commit: 123123
```


* revert：git revert [版本号]，将某个历史版本的修改撤销，并commit一个新的版本

* reset： git reset --hard [版本号]，将当前分支回退到某一个历史版本，并删除
```bash
#reset前：
#HEAD在D
A->B->C->D

#执行git reset --hard B
#HEAD指针回退到B，C、D两个版本被删除
A->B

#若想将远程库也同步本地库，回退到历史版本，需要添加-f
git push -f origin master
```



* rebase：git rebase develop，假设当前在feature分支，执行git rebase develop后，会将develop分支的代码与feature分支的代码合并。合并方式如下：
```bash
#rebase合并前
A->B->C->D（develop分支）
          |
          E->F（feature分支）
 #rebase合并之后（就像直接在develop提交了一样，不会出现分叉）
 A->B->C->D->E`-> F`（feature分支）
```



* fetch：



* merge：



* reflog：git reflog，提交记录、提交历史

