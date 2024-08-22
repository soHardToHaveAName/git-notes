
下载远程分支：

```
git checkout -b 本地分支名 origin/远程分支名
```



查看 历史 log：

``` 
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```

查看 历史 log：（包括已经 删除掉的历史提交记录）

```
git log --reflog --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```

（git commit --amend  操作，实际上是将上一条提交记录删除，然后创建一个同名的新的提交记录。因此，如果要查看 git commit --amend  历史操作记录，需要使用  git log --reflog 命令。）



提交新作业：（新的 Change ID）

```
git add ./practice/*/*.c
git add ./practice/*/*.png
sudo rm ./practice/*/*.out
git status
git commit
git push origin HEAD:refs/for/platform_homework_wangchongyu
```

其中，refs/for 是Gerrit的约定，

 意思是，我们提交代码到服务器之后是需要经过 code review 之后才能进行merge的。


修改提交了的作业：（不改变 Change ID，但 Commit ID 变了）

``` 
git add
git commit --amend
git commit --amend --no-edit
git push origin HEAD:refs/for/platform_homework_wangchongyu
```

```
touch push-amend.sh
#!/bin/sh
git commit --amend --no-edit
git push origin HEAD:refs/for/platform_homework_wangchongyu
```


删除暂存区中的某些文件：

（即：我以前通过 add 把文件A 添加到了暂存区，但是我现在不想要 A文件了。这时，就可以使用 git rm -f 来让这个文件同时从 暂存区 和 工作区 消失 ）

``` 
git rm -f [filename]
```



删除暂存区中的某些文件，但是将其保留在工作区：

（即：我以前通过 add 把文件A 添加到了暂存区，但是我现在后悔了，想要撤回这个 ”添加操作“，但是我又不想把本地的 A文件给删除。这时，就可以使用 git rm --cached 命令，只删除掉暂存区的 A 文件）

``` 
git rm --cached file
```





当修改本地仓库下文件的权限之后，git status 中会出现很多 modified，这是因为 git 设置文件权限追踪。可以使用下面这行代码关闭这个功能：

``` 
git config core.filemode false
```



查看当前仓库下git的配置：

``` 
cat .git/config
```


如何将一个文件从commit中移除？
可以使用以下命令
git reset HEAD^ filePath && git ckfilePath  && git add filePath  && git cm --amend

我的方法：
git checkout HEAD^
git checkout -b fix_branch
rm wrong.txt
git add wrong.txt
git commit --amend

【等到 merge 之后，git cherry-pick <后续提交>】





Cherry-Pick 操作实例：

1，前序工作
      请各位新人检查自己的git 分支上，是否存在code_skeleton的全部代码提交，共以下几条；如果不全，请将缺失的提交cherry-pick到自己的分支，提交、合入。不理解cherry-pick用法的请咨询导师
292a368328322f7dd676545e2ab0a066f819ead0 <通用><编译更新>添加-g选项编译更多调试信息，方便使用gdb等工具调试程序
85d5f5070e125b5b5e0fe8ef81ab66472af9cde5  <通用><功能更新>添加test命令，在其中预埋部分bug，用于后续debug培训
bab5d50d70157e65c47df1d4a823dc109be67587 <通用><功能更新>程序更名为code skeleton
c47b3bafb3ca56301bb608a189b3f57fc653eda8 <通用><功能更新>程序更名为code skeleton(2)
      提交日期：上海-8月9日前，深圳-8月12日前


git fetch origin platform_homework_wangchongyu

git checkout platform_homework_wangchongyu

git reset --hard <远程最新已经 Merge 了的 commit id>

git branch update_public_changes

git checkout update_public_changes

git fetch origin platform_public

git cherry-pick  740f45e  c47b3ba  681e13e

git push  origin HEAD:refs/for/platform_homework_wangchongyu




创建本地 git 仓库：

mkdir git-test && cd git-test

git init

git config user.name "myname"

git config user.email "111@qq.com"

git config -l




关于 git reset：

- 回退所有内容到上一个版本
    ```
    git reset HEAD^
    ```

-  回退test.txt这个文件的版本到上一个版本
    ```
    git reset HEAD^ test.txt
    ```

- 向前回退到第3个版本
    ```
    git reset  HEAD~3
    ```

- 回退到某个版本51363e6
    ```
    git reset 51363e6
    ```


reset 的三种模式：

--hard：重置位置的同时，直接将 working Tree工作目录、 index 暂存区及 repository 都重置成目标节点的內容,所以效果看起来等同于清空暂存区和工作区。

--soft：重置位置的同时，保留working Tree工作目录和index暂存区的内容，只让repository中的内容和 reset 目标节点保持一致，因此原节点和reset节点之间的【差异变更集】会放入index暂存区中(Staged files)。所以效果看起来就是工作目录的内容不变，暂存区原有的内容也不变，只是原节点和Reset节点之间的所有差异都会放到暂存区中。

--mixed（默认）：重置位置的同时，只保留Working Tree工作目录的內容，但会将 Index暂存区 和 Repository 中的內容更改和reset目标节点一致，因此原节点和Reset节点之间的【差异变更集】会放入Working Tree工作目录中。所以效果看起来就是原节点和Reset节点之间的所有差异都会放到工作目录中。


git checkout filename ：
实现暂存区文件检出到工作区中，其功能与git restore相一致。



解决冲突的工具：
Beyond Compare
TortoiseGit
Vscode
Sublime Merge 
Meld
The Perforce Visual Client (P4V) 
Kdiff3
IntelliJ IDEA
P4merge
vimdiff



本地仓库执行节点清理命令
节点清理压缩（需较多内存）：在git所在目录执行命令：
git gc --aggressive --prune=now
作用：将本地节点清理压缩，减小差异节点清点耗时。

