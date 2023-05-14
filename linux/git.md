 ### 全局免密码配置
配置存储模式

git config --global credential.helper store
执行之后会在用户主目录下的.gitconfig文件中多加 helper = store

Linux 下查看：

 vim ~/.gitconfig