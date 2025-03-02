### 命令行操作
#### 创建数据库
两种方式：

1. sqlite3 test.db
2. .open test.db

将数据库导出到文件

1. sqlite3 test.db .dump > test.sql

从生成的test.sql恢复为数据库

1. sqlite3 test.db < test.sql

将数据库表导出和导入

如果只想导出特定表格的数据，可以使用SQLite3的.output和.dump命令的结合， 首先进入终端会话

1. .output dumpfile.sql   将输出模式设置为将结果写入dumpfile.sql文件中
2. .dump table1 table2   将table1和table2导出到dumpfile.sql
3. .exit 结束会话

导入

1. .read dumpfile.sql

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
    int column_size,                      /*结果字段的个数*/
    char **column_value,                  /*返回记录的一位字符组指针*/
    char **column_name                    /*结果字段的名称*/
);

```

4. sqlite3_close

```
int sqlite3_close(sqlite3 *)
```

5. sqlite3_prepare_v2()

实现对sql语句（模版）的解析和编译，生成了可以被执行的sql语句实例

```
int sqlite3_prepare_v2(
    sqlite3 *db,            /* Database handle */
    const char *zSql,       /* SQL statement, UTF-8 encoded */
    int nByte,              /* Maximum length of zSql in bytes. */
    sqlite3_stmt **ppStmt,  /* OUT: Statement handle */
    const char **pzTail     /* OUT: Pointer to unused portion of zSql */
);
```
6. sqlite3_stmt()

可以结合变量使用，进而实现相同操作的循环

7. sqlite3_bind_*()

用于绑定赋值变量值到prepare语句中，也就是给sqlite3_stmt变量赋值

```
int sqlite3_bind_int(sqlite3_stmt*, int, int);
int sqlite3_bind_doubule(sqlite3_stmt*, int, double);
int sqlite3_bind_text(sqlite3_stmt*, int, const char*, int, void(*)(void*));
```

参数1：prepare编译出的sql语句实例

参数2：sqlite3_stmt变量参数的序号索引值，索引值从1开始

参数3：是要绑定给第二个形参指向的变量参数的实际值，第二个形参可以指向不同的索引值

参数4： 一般为第三个形参的长度

参数5：是用于BLOB和字符串绑定后的析构函数，用于在sqlite处理完blob或字符串之后处理它，一般可以设置为NULL





8. sqlite3_step

```
int sqlite3_step(sqlite3_stmt *pStmt);

```

返回值：

SQLITE_DONE：意味着sql语句执行完成且成功。一旦执行成功后，sqlite3_step()就不应该被再次调用执行，除非我们使用sqlite3_reset()重置 sqlite3_stmt 数据。

SQLITE_ROW：这个比较常用，当我们的sql语句是 读命令，比如"SELECT* FROM..."，返回的数据一般很多，并且数据是按行返回的，且每次只返回一行，其返回值为 SQLITE_ROW，所以需要重复调用sqlite3_step函数，直到sqlite3_step返回 SQLITE_DONE.

SQLITE_MISUSE： 表示该函数实例被滥用，不合适，比如sqlite_stmt结构已经被销毁了


[5,6,7,8]相比较使用sqlite3_exec函数，现在sql语句的解析和编译只执行了一次，而sqlite3_stop执行多次，整体效率提升

9. sqlite3_reset

```
int sqlite3_reset(sqlite3_stmt *pStmt);

```
10. sqlite3_column

该函数实例用于查询(query)结果的筛选，返回当前结果的某1列。


```
int sqlite3_column_int(sqlite3_stmt*, int iCol);
double sqlite3_column_double(sqlite3_stmt*, int iCol);
const unsigned char *sqlite3_column_text(sqlite3_stmt*, int iCol);
```