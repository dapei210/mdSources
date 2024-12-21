### 命令行操作
#### 创建数据库
两种方式：

1. sqlite3 test.db
2. .open test.db

将数据库导出到文件

1. sqlite3 test.db .dump > test.sql

从生成的test.sql恢复为数据库

1. sqlite3 test.db < test.sql

#### 查看表信息

1. 显示表头：.header on来开启表头的显示
2. 按列显示：.mode column指令来让数据按列的方式整齐的显示出来
3. 显示执行时间：.timer on指令来开始指令运行时间的显示


### sqlite3接口函数

1. sqlite3_open

```
int sqlite3_open(
  const char *filename,   /* Database filename (UTF-8) */
  sqlite3 **ppDb          /* OUT: SQLite db handle 数据库handle ，用结构体sqlite3来表示一个打开的数据库对象 */
);

```

2. sqlite3_exec
```
int sqlite3_exec(
  sqlite3*,                                  /* An open database */
  const char *sql,                           /* SQL to be evaluated */
  int (*callback)(void*,int,char**,char**),  /* Callback function */
  void *,                                    /* 1st argument to callback */
  char **errmsg                              /* Error msg written here */
);
```

3. 回调函数callback
```
int callback(
    void *params,                             /*sqlite3_exec传入的第四个参数*/
    int column_size,
    char **column_value,
    char **column_name
);

```