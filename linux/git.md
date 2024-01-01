 ### 全局免密码配置
配置存储模式

git config --global credential.helper store
执行之后会在用户主目录下的.gitconfig文件中多加 helper = store

Linux 下查看：

 vim ~/.gitconfig


 ### 切换到远程仓库对应的分支
 
首先，使用 git branch -r 命令查看所有的远程分支：

git branch -r
上述命令会显示远程仓库中的所有分支，例如：

origin/master
origin/feature-branch
origin/bugfix-branch

选择要切换到的远程分支，并使用 git checkout 命令进行切换。例如，切换到名为 feature-branch 的远程分支：

git checkout origin/feature-branch

注意，此时您已经处于一个“游离”的 HEAD 状态，不能进行提交操作。如果您想在远程分支上进行修改并提交更改，最好使用以下命令将其重命名为本地分支：

git checkout -b feature-branch origin/feature-branch

上述命令将创建一个本地分支，名称为 feature-branch，与远程分支 origin/feature-branch 建立绑定关系，并自动切换到该分支。现在，您可以在此分支上对代码进行修改并提交更改了

git push -u origin feature-branch


### 撤销commit
git reset --soft HEAD^

这样就成功撤销了commit，如果想要连着add也撤销的话，–soft改为–hard（删除工作空间的改动代码）

HEAD^ 表示上一个版本，即上一次的commit，也可以写成HEAD~1
如果进行两次的commit，想要都撤回，可以使用HEAD~2

–soft
不删除工作空间的改动代码 ，撤销commit，不撤销git add file

–hard
删除工作空间的改动代码，撤销commit且撤销add

另外一点，如果commit注释写错了，先要改一下注释，有其他方法也能实现，如：

git commit --amend
这时候会进入vim编辑器，修改完成你要的注释后保存即可。