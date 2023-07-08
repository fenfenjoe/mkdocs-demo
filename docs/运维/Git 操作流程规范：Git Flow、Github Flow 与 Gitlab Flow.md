## Git工作流（Git Flow、Github Flow 与 Gitlab Flow）
[toc]
### 参考

【团队协作中的 Github flow 工作流程】[https://zhuanlan.zhihu.com/p/39148914](https://zhuanlan.zhihu.com/p/39148914)

【git rabase简介】[https://blog.csdn.net/hudashi/article/details/7664631/](https://blog.csdn.net/hudashi/article/details/7664631/)

### 简介
Git Flow（Vincent Driessen，2010年）

Github Flow（Github提出的工作流，2011年）

GitLab Flow（Gitlab提出的工作流，2014年）

#### 不同工作流之间的比较

### Git Flow

中央仓库会一直存在以下两个长期分支：

**master Branch（生产环境）**

**develop Branch（开发环境）**

协助分支：

**Feature Branch（功能分支）**

**Release Branch（预发布分支）**

**Hotfix Branch（修补bug分支）**

主要操作：

**【搭建阶段】（项目经理）**

* 创建本地仓库
```bash
mkdir [项目名] #此时创建了一个项目名的文件夹
cd [项目名]
git init #创建了一个本地仓库
```

* 搭建远程仓库（Github、Gitee等）
略

* 将本地仓库连接到远程仓库，并起一个别名叫origin
```bash
git remote add origin [远程仓库地址]
```

* 查看远程仓库
```bash
git remote -v
```

**【开发阶段】（开发人员）**

* 将项目从远程仓库拷贝到本地仓库
```bash
git clone [git项目地址] #会自动检出master分支

git checkout -b develop origin/develop #手动检出develop分支，并切换
```

* 从develop分支创建feature分支并切换（Feature Branch）
```bash
git checkout -b feature-xxx develop
```
* 提交代码
```bash
git add . #将当前目录下所有文件添加至暂存区（包括子目录）
或者 git add --all
git commit -m [message] #提交暂存区所有修改
```
* Feature分支与Develop分支的代码合并
```bash
#rebase：将本地仓库中的develop分支与当前分支（feature-xxx）合并
git checkout feature-xxx
git rebase develop
#
git checkout develop
git merge --no-ff feature-xxx

```
* 删除Feature分支
```bash
git branch -d feature-xxx
```
* 推送Develop分支到远端Develop分支
```bash
git push -u [远程仓库名] [本地分支名]
```

**【预发布阶段】**

* 合并Develop分支到Release分支
```bash
git checkout develop
git merge release
```

* 加上Release版本号（Tag）
```bash
git tag [tagName] #tagName示例：v1.0

git push origin #将标签推送到远端仓库
```

### Github Flow
***
开源项目，或者公司项目，一般有自己的Git仓库（我们称为主仓库、中央仓库）。假设为：

https://github.com/fe/github-flow

主仓库一般只有一个固定分支：

**master分支**

主分支，永远是可用的稳定版本，不能直接在该分支上开发


>当然，也可根据自己的需要，增加develop分支。（以下例子便加上了develop分支）

**develop分支**

开发主分支，所有新功能以这个分支来创建自己的开发分支，该分支只做合并操作，不能直接在该分支上开发

***
若我们想在项目上进行二次开发，或者贡献自己的代码，我们通常会 fork主仓库的代码到自己的仓库下，然后 Clone 到本地进行开发。


假设上面主仓库 fork 之后的项目地址为：

https://github.com/xxx/github-flow


Clone 到本地：
```bash
git clone git@github.com:fe/github-flow.git
```



***

Clone到本地后，可以查看到远端仓库是我们自己的仓库。
```bash
git remote -v

origin  git@github.com:xxx/github-flow.git (fetch)
origin  git@github.com:xxx/github-flow.git (push)
```

若我们需要提交代码到主仓库，还需要在远端仓库添加上主仓库的地址。我们为主仓库命名为upstream

```bsh
git remote add upstream git@github.com:fe/github-flow.git`
```

此时再看远端仓库，主仓库的地址已在其中。

```bash
git remote -v

origin  git@github.com:xxx/github-flow.git (fetch)
origin  git@github.com:xxx/github-flow.git (push)
upstream    git@github.com:fe/github-flow.git (fetch)
upstream    git@github.com:fe/github-flow.git (push)
```
***
而在本地仓库中，一般会根据不同的情况，创建以下分支：

**feature分支**

功能开发分支，在develop上创建分支，以自己开发功能模块命名，功能测试正常后合并到develop分支

**feature-fix分支**

功能bug修复分支，feature分支合并之后发现bug，在develop上创建分支修复，之后合并回develop分支。PS:feature分支在申请合并之后，未合并之前还是可以提交代码的，所以feature在合并之前还可以在原分支上继续修复bug

**hotfix分支**

紧急bug修改分支，在master分支上创建，修复完成后合并到 master

***


**当我们需要开发新功能，我们这样做**

基于develop分支创建一个临时分支 feat/feedback（feature的意思），
```bash
#创建新分支
git checkout -b feat/feedback develop
```

开发完成后，提交本地代码到本地仓库的FEAT分支
```bash
#提交代码
git commit -a [文件1] [文件2]... -m [备注]
```

单元测试通过后，便要提交代码到远端仓库的develop分支中了。
提交前，需要看看远端分支是否有其他人提交代码，有则需要合并。
```bash
git checkout develop #切换到develop分支
git pull #拉取代码
git log feat/feedback..develop #比较feat/feedback与develop分支的差异
```

若有输出提示信息，证明远端有人提交代码，此时合并，会看到路线图出现分叉。
```bash
#不建议如下直接merge
git merge --no-ff
```
![8aab9097e5e770f9fc4d796c4488f9b2.png](en-resource://database/1037:1)

为了得到一个干净清爽的提交路线图，合并前最好先执行
```bash
git checkout feat/feedback #切换到feat/feedback分支
git rebase develop #将本地提交合并提交到远端分支
```
![a1be826ee478d5bda7e3a874d9cab1c1.png](en-resource://database/1038:1)

**若在rebase时发现代码有冲突...**

git会暂停rebase，并提示需要先处理冲突。

处理完冲突后，add之后无需执行commit，继续rebase即可。
```bash
git add [文件]
git rebase --continue
```


将代码推送到远端（origin）分支（我们的远端仓库）
```bash
git push origin feat/feedback
```

最后，若想将origin分支中feat/feedback 分支提交到主仓库（upstream）的develop分支中，需要去主仓库open a pull request（在github上操作）。


提交申请后需要经过Code Review（代码审查，由主仓库拥有者负责）。


Code Review通过后，有以下方式将feat/feedback 分支合并到主仓库（同样在github上操作）：


**1. Create a merge commit**
> 合并结果如图，等于执行了“git merge --no-ff”进行合并。

![9fb1a0fd0ce5eb2a50f1bd8488b1b7fb.png](en-resource://database/1041:1)

**2. Squash and merge**
> 合并结果如图，等于执行了“git merge --squash”进行合并。

![52df17b223f1f917a42ae93591788454.png](en-resource://database/1039:1)

**3. Rebase and merge**

![89d9e127a2f8bd88e3693a93b3b86290.png](en-resource://database/1040:1)


最后，将本地的feature分支删除，并同步到origin

本地分支删除：git branch -D feat/feedback
远端分支删除：git push origin :feat/feedback



### Gitlab Flow