
# learn git branching - local


## 1. 基础篇

1. git commit
2. git branch  &  git checkout
   1. 创建分支的同时切换到新的分支上： ` git checkout -b xxx `
3. git merge
   1. 假设当前分支为A，使用 git merge  B 命令，会将A分支与B分支进行合并，合并之后的当前分支仍为A（即：将B分支合并到A分支上）
4. git rebase
   1. 类似 merge，但是会改变当前所在分支的 base（基底），（即改变其祖先），一般情况下不使用。（只有在 本地 & 特殊情况下 会使用）

## 2. 高级篇

1. 分离 HEAD
   1. HEAD 是一个对当前所在分支的符号引用 —— 也就是指向你正在其基础上进行工作的提交记录。HEAD 总是指向当前分支上最近一次提交记录。（一个提交记录对应着一个哈希值）
   2. 想看 HEAD 指向，可以通过 `cat .git/HEAD` 查看，
   2. 如果 HEAD 指向的是一个引用，还可以用 `git symbolic-ref HEAD` 查看它的指向。
2. 相对引用
   1. 使用哈希值来指定提交记录是【绝对引用】
   2. git 同时还支持相对引用：
      1. `^` ：向上移动 1 个提交记录，如 ` git checkout main^ `
      2. ~<num>  向上移动多个提交记录，如  ` git checkout main~3 `
   3. 强制修改分支位置：
      1.  ` git branch -f main HEAD~3  ` 会将 main 分支强制指向 HEAD 的第 3 级 parent 提交。
3. 撤销变更
   1. git reset： 向上移动分支，回退到指定的历史提交记录（只能在本地使用）
   2. git revert：撤销指定的提交记录，虽然是撤销操作，但是执行之后会生成一个新的提交记录，它和执行前所在位置的父节点内容相同（可以推送到远程仓库）

## 3. 移动提交记录

1. cherry-pick：
   1. 挑选某个（某些）提交记录，将其复制到当前分支的下面
2. 交互式 rebase
   1. ` git rebase -i `。  可进入交互式操作界面，有以下选项：
        1. Pick：会在你的历史记录中保留该提交。
        2. Reword：允许你修改提交信息，可能是修复一个错别字或添加其它注释。
        3. Edit：允许你在重放分支的过程中对提交进行修改。
        4. Squash：可以将多个提交合并为一个。

## 4. 杂项

1. 只取一个提交记录（git cherry-pick）
2. 提交的技巧1
   1. 使用 git rebase -i 和 git commit --amend 实现对历史提交记录的修改。
3. 提交的技巧2
   1. 使用 git cherry-pick 和 git commit --amend 实现对历史提交记录的修改。
4. Git Tag
   1.  ` git tag v1 commit_id_xxx `：给 commit_id_xxx 这个提交记录打上标签，标签名为 v1。（不指定commit id 的话，默认为当前 HEAD 所指向位置）
5. Git Describe
   1. git describe \<ref\> ：找到并显示与 ref 提交记录 距离最近的 tag。

## 5. 高级话题

1. 多次Rebase（略）
2. 两个parent节点（略）
3. 纠缠不清的分支（略）

# learn git branching - remote

## Git 远程仓库

### 1. Git Clone

（略）

### 2. 远程分支

1. 切换到远程分支时，自动进入分离 HEAD 状态。（因为不允许直接在远程分支上进行操作）


### 3. Git Fetch

1. git fetch 的作用：将本地仓库中的远程分支 更新为 远程仓库中相应分支最新的状态。
2. git fetch 实际的步骤：
             1. 从远程仓库下载本地仓库中缺失的提交记录
             2. 更新远程分支指针（如 origin/main）
3. 注意：git fetch 并不会改变本地仓库（本地分支）的状态，也不会修改磁盘上的本地文件，而只是单纯地下载了一下远程仓库的最新状态。

### 4. Git Pull

1. git pull 相当于 git fetch +  git merge 。即：首先将本地仓库中的远程分支 更新为 远程仓库中相应分支最新的状态，然后将其与本地分支进行合并
2. git pull --rebase 相当于 git fetch +  git rebase 。

### 5. 模拟团队合作

（略）

### 6. Git Push

1. git push 会将本地仓库推送到远程仓库
2. git push 不加参数时，具体行为由  push.default 配置选项决定
   1. 可通过 ` git config --global push.default ` 来修改默认的推送模式

### 7. 偏离的提交历史

1. 假设本地在远程仓库点A的基础上开发到了点B，想要提交到远程。但是，此时发现远程仓库已经在A的基础上更新到了C，这种情况下就不能直接使用 git push。因为本地当前所基于的是 **旧** 的远程分支。
2. 正确的做法是，先 git pull，再 git push。

### 8. Locked Main

1. 在团队中工作时，main 分支一般是被锁定的。并不能直接 push 到 main 分支上，而是需要 先push到 其他分支上，然后通过 pull request 流程来合并修改。

2. 但是如果我忘了，直接在本地main分支上修改，并试图push到远程的main分支，会发生什么情况？  

3. 终端会报错：`! [远程服务器拒绝] main -> main (TF402455: 不允许推送(push)这个分支; 你必须使用pull request来更新这个分支.)`

4. 这时该怎么办？

   首先，在当前位置新建一个分支 feature：

   ``` shell
   git checkout -b feature
   ```

   然后，将 feature 分支推送到服务器：

   ``` shell
   git push origin feature 
   ```

   最后，将 main 分支回退到 origin/main 所在位置：

   ```
   git reset origin/main 
   git checkout feature（后续在 feature 分支上开发，不要再在 main 分支上修改！！！）
   ```

   

## Git远程仓库高级操作


### 1. 推送主分支

1. 通过 git fetch、git rebase / git cherry-pick 和 git push，可以将多个特征分支推送到远程主分支上


### 2. 合并远程仓库

1. rebase 的优点：所有的提交都在一条线上，提交树非常简洁清晰。
2. rebase 的缺点：修改了提交树的历史，不利于追溯。
3. merge 的优缺点： 与rebase 相反。

### 3. 远程追踪

1. 当使用git clone 下载仓库时，默认设定 本地的 main 分支 来追踪远程的 origin/main 分支
2. 通过 ` git checkout -b notMain o/main `，可以新建一个 notMain 分支，让它来追踪远程的 origin/main 分支。（此时，本地的 main 分支 就不再追踪远程的 origin/main 分支了！！！）
3. 通过 git branch -u o/main foo，可以让已经存在的本地 foo 分支来 追踪远程的 origin/main 分支


### 4. git push 的参数 - 1

1. 在单纯使用 git push 而不加任何参数的时候， Git 是通过【当前所在分支的】【属性】来确定远程仓库以及要 push 的目的地的。
 2. 带参数的 push 命令： ` git push <remote> <place> `
 3. 例如，git push origin main，
    1. origin是远程仓库名称
    2. 通过“place”参数来告诉 Git， 提交记录来自于 main（并不一定是当前分支）, 要推送到远程仓库中的 main。

### 5. git push 的参数 - 2

1. 如果 git push 的来源和去向不是同一个分支，则可以用更详细的参数来分别指定：
   ` git push origin <source>:<destination> `
2. 作用是：将本地的`  <source>  `提交记录 推送到远程的 ` <destination>  `分支。
   如果指定的 ` <destination> ` 是远程不存在的分支，则会在远程新建这个分支。

### 6. git fetch 的参数 

1. 单纯使用 git fetch 而不加任何参数的时候， Git 会从远程下载所有分支的提交记录到本地对应的远程分支。
2.  ` git fetch origin <place> `
   从远程的 ` <place> ` 分支，获取本地缺少的提交记录，并下载到本地对应的 origin/<place>远程分支上。
3.  ` git fetch origin <source>:<destination> `
   从远程的 ` <source> ` 分支，获取本地缺少的提交记录，并下载到本地指定的 ` <destination> ` 分支上。
   如果指定的 ` <destination> ` 是本地不存在的分支，则会在本地新建这个分支。


### 7. 没有 source 的 source

1. git push 和 git fetch 支持 空 的 source，即在冒号的左边什么都没有。
2. 例：` git push origin :side ` 
   将 【空】 推送到远程 side 分支，将会在远程仓库中删除 side 分支！！！
3. 例：` git fetch origin :bugFix ` 
   将 【空】 拉取到本地 bugFix 分支，将会在本地当前位置创建一个 bugFix 分支。相当于 git branch bugFix

### 8. git pull

1. ` git pull origin foo ` 相当于：

   ``` shell
   git fetch origin foo
   git merge origin/foo
   ```


   作用：从远程的 foo 分支，获取本地缺少的提交记录，并下载到本地对应的 origin/foo 远程分支上。然后，将 当前所在分支 与本地的 origin/foo 远程分支进行合并。


2. ` git pull origin <source>:<destination>  ` 相当于：
   
   ``` shell
   git fetch origin <source>:<destination>
   git merge <destination>
   ```
   
   
   作用：从远程的 ` <source> ` 分支，获取本地缺少的提交记录，并下载到本地指定的 ` <destination> ` 分支上。然后，将 当前所在分支 与本地的  ` <destination> ` 分支进行合并。











