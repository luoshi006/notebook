## 安装
```sh
sudo apt install cmake -y
```

## Hello world
- 在项目的根目录中包含一个 `CMakeLists.txt` 文件，在其中定义了顶层的 CMake 操作。

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
		> Note: 因为 Cache 主要提供用户可配置的变量，所以，默认不会覆盖已经存在的 Cache 变量。使用 `FORCE` 强制覆盖。
		- Cache 会强制覆盖当前作用域中的普通变量
		- 在执行 cmake 时，使用 `-D<var>=<value>` 可以设置没有 `<type>` 类型的 Cache 变量，set 指令会添加默认类型。如果是相对路径，则根据当前工作路径拼接为绝对路径。
		- `<type>` 指定 Makefile 中的变量类型
			- `BOOL` ON/OFF
			- `FILEPATH` 文件路径
			- `PATH` 目录路径
			- `STRING` 文本字符串
			- `INTERNAL`文本字符串，在 cmake-gui 中动作不同
		- **环境变量**
			- `set(ENV{<variable>} <value>...)`

- **`list`**
	- https://cmake.org/cmake/help/v3.8/command/list.html

   ```
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
