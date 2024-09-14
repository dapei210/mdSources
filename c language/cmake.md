cmake的内置命令是不区分大小写的

因此add_subdirectory与ADD_SUBDIRECTORY作用一致。

 cmake的所有变量都是区分大小写的

### 参数
aux_source_directory-----整合源文件

include_directories--引入头文件路径

//生成静态库或动态库，参数1：库名称；参数2：静态或动态；参数3：生成所需要的源文件
```
add_library(func_shared SHARED ${SRC_LIST})
add_static(func_static STATIC  ${SRC_LIST})
```

//设置最终生成的库名称，统一为myfunc
```
set_target_properties(func_shared PROPERTIES  OUTPUT_NAME "myfunc")
set_target_properties(func_static PROPERTIES  OUTPUT_NAME "myfunc")
```
1. PROJECT_SOURCE_DIR 与 PROJECT_BINARY_DIR
cmake系统预定义变量： 指定工程路径
```
PROJECT_SOURCE_DIR/CMAKE_SOURCE_DIR/_SOURCE_DIR： 为包含PROJECT()命令的最近一个CMakeLists.txt文件所在的文件夹路径。

PROJECT_BINARY_DIR/CMAKE_BINARY_DIR/_BINARY_DIR： 运行cmake命令的目录，即工程编译发生的路径。
```
2. EXECUTABLE_OUTPUT_PATH 与 LIBRARY_OUTPUT_PATH
cmake系统预定义变量
```
以上两变量在新版本中已被`CMAKE_RUNTIME_OUTPUT_DIRECTORY`和`CMAKE_ARCHIVE_OUTPUT_DIRECTORY`代替
```
//设置库默认生成到哪里
```
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/../lib)
```

PROJECT_BINARY_DIR是cmake系统变量，是执行cmake命令的目录
LIBRARY_OUTPUT_PATH是cmake系统变量，项目生成的库文件都放在这个目录下
EXECUTABLE_OUTPUT_PATH是cmake系统变量，是生成的可执行文件的的目录

//设置可执行文件生成到哪里
```
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/../bin)
```
3. CMAKE_EXPORT_COMPILE_COMMANDS选项：
默认不开启，开启的两种方法：

```
在CMakeLists.txt中添加 set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
在命令行中添加-DCMAKE_EXPORT_COMPILE_COMMANDS=on
开启后，其生成的文件compile_commands.json，包含所有编译单元所执行的指令，可以在此json中查看cpp文件是否生成.o（即该cpp是否编译了）
```

//查找库文件，第二个参数：要查找的库文件，第三个参数：要在哪个目录下查找
//第一个参数：是一个白能量，用于存储查找到的库文件
```
find_library(FUNC_LIB myfunc ${PROJECT_BINARY_DIR}/../lib)
```

//生成可执行文件
```
add_executable(hello ${MAIN_SRC})
```

//把库链接到可执行文件中
```
target_link_libraries(hello ${FUNC_LIB})
```

//编译时添加一些编译选项
```
add_compile_options(-std=c++11 -Wall -O2)
```

//加入一个配置头文件，用于处理CMake对源码的设置，以TutorialConfig.h.in 为模版，替换相关变量，以生成 TutorialConfig.h

```
configure_file (
	"${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
	"${PROJECT_BINARY_DIR}/TutorialConfig.h"
)
```
//版本号 1.0
```
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)
```

//在源代码中添加 TutorialConfig.h.in 文件
```
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

当 CMake 生成这个头文件时，@Tutorial_VERSION_MAJOR@ 和 @Tutorial_VERSION_MINOR@ 的值将会由 CMakeLists.txt 中对应的值替换

//是否使用我们自己的函数
```
option (USE_MYMATH
        "Use tutorial provided math implementation" ON)
```


include指令

.cmake文件是一个模块文件，可以被include到CMakeLists.txt中
include指令一般用于语句的复用，也就是说，如果有一些语句需要在很多CMakeLists.txt文件中使用，为避免重复编写，可以将其写在.cmake文件中，然后在需要的CMakeLists.txt文件中进行include操作就行了