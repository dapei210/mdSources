### go源代码编译安装

1.5以上的版本安装，需要先安装1.4版本

1. 下载源代码后src目录下执行./all.bash即可
2. 安装好后将go目录文件复制到/root/go1.4
3. 安装1.4以后的版本，同样执行./all.bash
4. 设置几个环境变量到/etc/profile目录

```
export GOROOT=/usr/local/go***
export GOBIN=$GOROOT/bin
export PATH=$PATH:$GOBIN
```

5. source /etc/profile生效

GO111MODULE是 go modules 功能的开关，关闭操作如下：

go env -w GO111MODULE=off