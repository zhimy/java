git 提交代码流程


    1. 保存本地正在开发或已经开发完成的代码(此步骤建立在查看代码状态 如果有改动则需要保存 如果没有任何改动可跳过该步骤，查看代码状态指令 git status)
` 保存本地代码指令： git stash save xxx “xxx”标志stash的名称 自定义即可


    2.同步线上代码：
· 同步代码分两步指令：
                         git pull
                         git fetch
                          
git rebase origin/master
     

3. 将保存的本地代码apply回git库
· 指令：git stash apply stash@{x} x 表示stash的角标
有冲突解决冲突
   
4. git add xxxxxxxx "xxxxxxxxx"为要添加的文件路径

    git commit -m""
    git commit --amend   修改最新提交的文件
    
5. git push origin HEAD:refs/for/master

//切换分支
git checkout -b dev origin/dev

git show stash@{*}  "*"代表git stash list  中的序号

git commit --amend   修改最新提交的文件

git commit -m""

git 保存代码   自定义格式，12.19.10（月.日.当前 当前小时）


如果提交错误，先保存现有代码，再把之前的提交的代码同步合并下，再提交

先git commit 保存到库中，
保存在list中   查看 list   git stash list

最新的就是顶层的， 
 git stash apply stash@{0}
然后重新提交
查看当前状态 git status
如果在git命令过程中操作过后如果遇到，文件爆红叉的情况可以在工程中找到 Maven ->Update一下

git branch 查看本地分支   加（-a） 查看远程分支

git reset (文件名)可以将add进去的文件移出来  

如果遇到同步不下来的问题时候，请先看自己的的代码是否保存到本地中

git reset Head^     PS:"^"为 shift + 6（上尖括号）；回退到上一个结点

git reset HEAD^   ：可将所有的提交的文件reset下

git log 查看历史提交版本

git push origin HEAD:refs/for/master


========================================================================

git cherry-pick 8f63b09618c9000ff9***********  (Commit)
git cherry-pick --continue

========================================================================
新拉的目录commit 的时候需要添加
scp -p -P 29418 [email]yingxinjun@10.21.7.94[/email]:hooks/commit-msg ${gitdir}/hooks/
scp -p -P 29418 [email]yingxinjun@10.21.7.94[/email]:hooks/commit-msg .git/hooks/

如果

========================================================================
将分支代码回到指定版本
git revert -n xxxx
git commit -am "xxx"
git push
========================================================================


// 问题：error: failed to push some refs to 'https://github.com/user/MCN.git'
// 解决↓
git pull --rebase origin master

// 清空stash中的内容
git stash clear

//删除指定下标的内容
git stash drop stash@{0}

//删除工作树中的缓存文件
出现以下错误的时候：The following untracked working tree files would be overwritten by checkout
git clean -d -fx


或者直接去git 页面上 点击Cherry Pick


// 强制更新分支代码，本地所有修改都会废弃，慎用
git reset --hard origin/master

单个merge，示例：git checkout -p mcn-spider-dev {path}
一、maser合道dev
切到dev分支
git checkout -p master FilePath
git commit -m ""
git push

二、dev合到master
切到master
git checkout -p dev FilePath
git commit -m ""
git push

// 分支merge
一、master代码合并到dev
    1）在master分支 pull 最新代码
    2）把master代码提交  push 到 origin/master
    3）在dev分支上 dev pull最新代码
    4）dev  git merge origin/master --no-ff
    5)解决冲突  git commit --amend
    6)git push origin HEAD:refs/for/dev
二、dev代码合并到master
    master pull 最新代码
    master push 到 origin/master
    dev pull最新代码
    master  git merge origin/dev --no-ff
    解决冲突  git commit --amend
    git push origin HEAD:refs/for/master































