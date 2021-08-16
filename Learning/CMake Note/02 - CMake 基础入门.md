## 0. 安装
```sh
sudo apt install cmake -y
```

## 1. Hello world
- 在项目的根目录中包含一个 `CMakeLists.txt` 文件，在其中定义了顶层的 `CMake` 操作。

```cmake
cmake_minimum_required (VERSION 3.1.0)

project(hello_world)

message(STATUS "This is BINARY dir " ${PROJECT_BINARY_DIR})
message(STATUS "This is SOURCE dir " ${PROJECT_SOURCE_DIR})

add_executable(hello_world main.cc)
```

- **`cmake_minimum_required()`**
	- `cmake_minimum_required()` 会自动调用 [`cmake_policy(VERSION)`](https://cmake.org/cmake/help/v3.20/command/cmake_policy.html#command:cmake_policy "cmake_policy") 进行策略配置，与 cmake 版本强相关。
- **[`PROJECT(projectname [C] [CXX] [JAVA])`](https://cmake.org/cmake/help/v3.8/command/project.html)**。
	- `PROJECT` 需要紧跟`cmake_minimum_required()` 放在 `CMakeLists.txt` 的最前面，保证构建 version 和 policy 配置。若没有`PROJECT`，则 CMake 会抛出警告，同时自动添加 `project(Project)`，并设值默认语言为 `C/CXX`。
	- 每一条 `PROJECT` 指令开始一段作用域，直到遇到下一条 `PROJECT` 为止。同时，所有的构建目标定义（例如 `add_executable`），以及所有的其它 CMake 脚本指令，都归属到相应的 `PROJECT` 作用域中。
	- `PROJECT` 指令在开始 `projectname` 项目的作用域的同时，也隐含地定义 `<projectname>_BINARY_DIR` 和 `<projectname>_SOURCE_DIR` 变量。同时 `PROJECT_BINARY_DIR` 和 `PROJECT_SOURCE_DIR` 变量也会被定义，且与 `<projectname>_BINARY_DIR` 和 `<projectname>_SOURCE_DIR` 变量相同。使用哪一套变量取决于你的喜好，但为了省力，**推荐 `PROJECT_BINARY_DIR` 和 `PROJECT_SOURCE_DIR` 这一套**。
- **`add_executable`**
	- 指令用于指定一个可执行文件类型的构建目标，该可执行文件的文件名将会是 `hello_world`。在统一个项目中，该名称必须是全局唯一的。
	- [`IMPORTED`](https://cmake.org/cmake/help/v3.8/prop_tgt/IMPORTED.html#prop_tgt:IMPORTED "进口")
	- `EXCLUDE_FROM_ALL` 置 `True`，将从默认构造目标中移除，即，`make` 默认不会编译该目标，需使用 `make hello_world` 显式指定目标编译。
- **`set`**
	- [`SET`](https://cmake.org/cmake/help/v3.8/command/set.html) 指令可以定义一个变量，以后通过 `${VariableName}` 引用。
	> Note: 如果你在 `IF (VariableName)` 指令中检测一个变量时，不要使用 `${}` 语法。其原因在于你想用 `IF` 检测的是该变量。
	- **普通变量**
		- `set(<variable> <value>... [PARENT_SCOPE])`
		- 指定`PARENT_SCOPE`，可以将 `variable`  的作用域提升到上一层，在当前作用域不生效，退出到上一层作用域时生效。（每个目录和函数都拥有独立的作用域）【目测，未测试】
	- **缓存项 Cache Entry**
		- `set(<variable> <value>... CACHE <type> <docstring> [FORCE])`
		> Note: 因为 Cache 主要提供用户可配置的变量，所以，**默认不会覆盖已经存在的 Cache 变量**。使用 `FORCE` 强制覆盖。
		> 
		> `CMake` 会生成 `CMakeCache.txt`文件保存 `cache` 变量。所以 `CMake` 能够记住你的配置。
		- Cache 会强制覆盖当前作用域中的普通变量
		- 在执行 cmake 时，使用 `-D<var>=<value>` 可以设置没有 `<type>` 类型的 Cache 变量，set 指令会添加默认类型。如果是相对路径，则根据当前工作路径拼接为绝对路径。
		- `INTERNAL`表示内部变量；`ADVANCED`可以被外部修改，与 GUI 操作相关
		- `<type>` 指定 Makefile 中的变量类型
			- `BOOL` ON/OFF
			- `FILEPATH` 文件路径
			- `PATH` 目录路径
			- `STRING` 文本字符串
			- `INTERNAL`文本字符串，在 cmake-gui 中动作不同
		- **环境变量**
			- `set(ENV{<variable>} <value>...)`


```cmake
# 默认不会覆盖现有 cache 变量
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")

# 临时全局变量
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "" FORCE)
mark_as_advanced(MY_CACHE_VARIABLE)

# Bool 变量
option(MY_OPTION "This is settable from the command line" OFF)
```

- **`list`**
	- https://cmake.org/cmake/help/v3.8/command/list.html

   ```cmake
	list(LENGTH <list> <output variable>)
	list(GET <list> <element index> [<element index> ...]
			 <output variable>)
	list(APPEND <list> [<element> ...])
	list(FILTER <list> <INCLUDE|EXCLUDE> REGEX <regular_expression>)
	list(FIND <list> <value> <output variable>)
	list(INSERT <list> <element_index> <element> [<element> ...])
	list(REMOVE_ITEM <list> <value> [<value> ...])
	list(REMOVE_AT <list> <index> [<index> ...])
	list(REMOVE_DUPLICATES <list>)
	list(REVERSE <list>)
	list(SORT <list>)
   ```
   - 常用的 `APPEND` 向列表中添加元素
- **`message`**
	- `message([<mode>] "message to display" ...)`
	- `<mode>` 定义消息类型
		- `(none)`，重要消息
		- `STATUS`，附带消息
		- `WARNING`，警告
		- `AUTHOR_WARNING`，警告
		- `SEND_ERROR`，错误、skip
		- `FATAL_ERROR`，错误，中断程序报错
		- `DEPRECATION`，兼容性关键字


## 2. Programming in CMake

### 2.1 Control flow
- [`if` 语句](https://cmake.org/cmake/help/latest/command/if.html)


```cmake
if(variable)
    # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
else()
    # If variable is `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`, or ends in `-NOTFOUND`
endif()
# If variable does not expand to one of the above, CMake will expand it then try again
```

```cmake
if("${variable}")
    # True if variable is not false-like
else()
    # Note that undefined variables would be `""` thus false
endif()
```

> - **一元运算符**
>   - `NOT`, `TARGET`, `EXISTS` (file), `DEFINED`, etc.
> - **二元运算符**
>   - `STREQUAL`, `AND`, `OR`, `MATCHES` (regular expression), `VERSION_LESS`, `VERSION_LESS_EQUAL` (CMake 3.7+), etc.

### 2.2 [generator-expressions](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html)
> 用于支持 build 或 install 时的逻辑判断，即 Make 过程中的逻辑控制

### 2.3 Macros and Functions
- 宏和函数的区别在于**作用域**。宏没有作用域。
	- 在函数中定义外部可见的变量需要使用 `PARENT_SCOPE`，对于嵌套函数则非常不优雅。
	- 宏没有作用域，所以变量是当前域可见。


```cmake
# ref from: https://cloud.tencent.com/developer/article/1433475
# 定义一个宏，显式声明了两个参数hello,world
macro(argn_test hello world)
	MESSAGE(STATUS ARGV=${ARGV})
	MESSAGE(STATUS ARGN=${ARGN})
	MESSAGE(STATUS ARGV0=${ARGV0})
	MESSAGE(STATUS ARGV1=${ARGV1})
	MESSAGE(STATUS ARGV2=${ARGV2})
	MESSAGE(STATUS ARGV3=${ARGV3})
endmacro()
# 调用宏时传入4个参数
argn_test(TOM JERRY SUSAN BERN)
```

```sh
-- ARGV =TOMJERRYSUSANBERN
-- ARGN =SUSANBERN
-- ARGV0=TOM
-- ARGV1=JERRY
-- ARGV2=SUSAN
-- ARGV3=BERN
```

#### CMake 自带的参数解析
- [`cmake_parse_arguments`](https://cmake.org/cmake/help/latest/command/cmake_parse_arguments.html)
```cmake
cmake_parse_arguments(<prefix> <options> <one_value_keywords> <multi_value_keywords> <args>...)
```
- 示例：`../Learning/CMake Note/source/02-test/arguments/`

### 2.4 [Configure File](https://cmake.org/cmake/help/latest/command/configure_file.html)
- `ref from: https://www.jianshu.com/p/2946eeec2489`
```cmake
configure_file(<input> <output>
               [FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NO_SOURCE_PERMISSIONS] [USE_SOURCE_PERMISSIONS]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```
-  将 `<input>` 文件复制到 `<output>`，并将其中 `@VAR@` or `${VAR}` 替换为所引用的变量。
- 输入文件中的 `#cmakedefine VAR ...`，将被替换为`#define VAR ...` 或 `/* #undef VAR */`
#### 示例
```cmake
===== config.h.in ======
#cmakedefine var1
#cmakedefine var2 "@var2@" #注意：@@之间的名称要与cmakedefine后的变量名一样
#cmakedefine01 var

===== CMakeLists.txt =====
cmake_mininum_required(VERSION 2.8)
project (configure_file_test)
option (var1 "use var1..." ON)  #定义var1，也可以使用cmake -Dvar1=ON替代
set (var2 13) #指定var2的值

option(var "use var..." ON) # 实际也可以用cmake -Dvar=ON或者cmake -Dvar=OFF来定义或者不定义
#set (var XXX) # 用set来定义var也可以，验证发现XXX为0的时候相当于var未定义，XXX为非0的时候效果相当于var有定义

configure_file (config.h.in config.h)

===== config.h =====
#define var1
#define var2 "13"

#define var 1 # 当var有定义
#define var 0 # 当var未定义
```
##### **`options`** - 参数选项
1.  `COPYONLY`：仅把`input`文件拷贝到`output`，不做任何替换。注意，!该选项与 `NEWLINE_STYLE`冲突，不能同时使用。
2.  `ESCAPE_QUOTES`：忽略反斜杠（C语言风格）的转义。
3. `@ONLY`：只替换`@var@`，不替换`${var}`。`${var}`在脚本语言中有语义含义（替换会导致含义发生变化），因此在处理这类文件的时候，该选项很有用。
4. `NEWLINE_STYLE`：指定`output`文件的换行风格，该参数后面要指明换行的规则，如`UNIX|DOS|WIN32|LF|CRLF`。

### 2.5 执行其他命令
#### · 2.5.1 Running a command at configure time
- 通过 [`execute_process`](https://cmake.org/cmake/help/latest/command/execute_process.html) 在配置时执行指令，通常要避免直接使用指令名，而是通过变量引用，防止未知错误。

```cmake
find_package(Git QUIET)

if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
    execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                    RESULT_VARIABLE GIT_SUBMOD_RESULT)
    if(NOT GIT_SUBMOD_RESULT EQUAL "0")
        message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
    endif()
endif()
```

#### · 2.5.2 Running a command at build time
- ref from https://zhuanlan.zhihu.com/p/95771200

> `add_custom_target` 创建执行自定义命令的目标
> `add_custom_command` 指定必须执行的命令，以生成文件或在其他目标的特定生成事件中生成。
>  - `add_custom_command`  - `OUTPUT` 编译目标，生成输出文件
> - `add_custom_command` - `TARGET` 在构建目标前后，执行指令

```cmake
# add_custom_command - OUTPUT
add_custom_command(OUTPUT printout 
                    COMMAND ${CMAKE_COMMAND} -E echo compile finish
                    VERBATIM
                   )

# add_custom_command - TARGET
add_executable(${PROJECT_NAME} main.c)
add_custom_command(TARGET ${PROJECT_NAME} 
                    POST_BUILD 
                    COMMAND ${CMAKE_COMMAND} -E echo compile finish
                    VERBATIM
                   )

# add_custom_target
add_custom_target(CopyTask
                  COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/config ${CMAKE_CURRENT_SOURCE_DIR}/etc
                 )
```


#### 2.5.3 Included common utilities
- ref from: https://cmake.org/cmake/help/latest/manual/cmake.1.html#run-a-command-line-tool
> `cmake -E <command> [<options>]` \ `${CMAKE\_COMMAND} -E`支持跨平台调用系统指令

###  2.6 循环
```cmake
# foreach(<loop_var> <items>)
foreach(i 0 1 2 3)
    message(STATUS "current is ${i}")
endforeach(i)

# foreach(<loop_var> RANGE <stop>)
foreach(i RANGE 3)
    message(STATUS "current is ${i}")
endforeach(i)

# foreach(loop_var RANGE start stop [step])
foreach(i RANGE 0 3 1)
    message(STATUS "current is ${i}")
endforeach(i)

# foreach(loop_var IN [LISTS [list1 [...]]]
#                     [ITEMS [item1 [...]]])
set(A 0;1)
set(B 2 3)
foreach(i IN LISTS A B)
    message(STATUS "current is ${i}")
endforeach()
```


## 3. Adding Feature

### 3.1 Build type
- CMake 默认使用 非 release、非 debug 编译类型

### 3.2 Debug
- Trace 每行执行时打印
```sh
cmake -S . -B build --trace-source=CMakeLists.txt
```

## 4. 交叉编译
```sh
cmake .. -DCMAKE_TOOLCHAIN_FILE=../cmake/toolchain.cmake
```
- CMake 给交叉编译预留了一个变量`CMAKE_TOOLCHAIN_FILE`，它定义了一个`.cmake`文件的路径，该文件里面设置了一系列CMAKE变量和属性。
- 不同平台架构的交叉编译工具链可以编写不同的`toolchain.cmake` 文件

```cmake
# 交叉编译方式
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

# set(CMAKE_SYSROOT /home/devel/rasp-pi-rootfs)
# set(CMAKE_STAGING_PREFIX /home/devel/stage)

#指定C/C++交叉编译器
set(CMAKE_C_COMPILER   arm-linux-gnueabihf-gcc)
set(CMAKE_CXX_COMPILER arm-linux-gnueabihf-g++)

#从来不在指定目录下查找工具程序
# set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
#只在指定目录下查找库文件
# set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
#只在指定目录下查找头文件
# set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
# set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```