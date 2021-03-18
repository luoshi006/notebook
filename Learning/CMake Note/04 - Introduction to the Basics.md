## Minimum Version
- `CMakeLists.txt` 第一行，指定 CMake 的最低版本号。
```cmake
cmake_minimum_required(VERSION 3.1)
```
- 不同的 CMake 版本会定义不同的配置策略 [Policy](https://cmake.org/cmake/help/latest/manual/cmake-policies.7.html)

## Setting a project
- https://cmake.org/cmake/help/latest/command/project.html
```cpp
project(MyProject VERSION 1.0
                  DESCRIPTION "Very nice project"
                  LANGUAGES CXX)
```

## Making an executable
- https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html
```cmake
add_executable(one two.cpp three.h)
```
这里的 `one` 既是可执行文件的名称，也是 CMake Target 的名称；后面是所有用到的源文件列表。

## Making a library
- https://cmake.org/cmake/help/latest/command/add_library.html
```cmake
add_library(one STATIC two.cpp three.h)
```
其中，`STATIC`, `SHARED`, or `MODULE` 表示编译的库类型。如果不指定，则采用 `BUILD_SHARED_LIBS`

## Targets are your friend

```cmake
target_include_directories(one PUBLIC include)
```
为目标 `one` 包含目录 `include`。`PUBLIC` 表示链接 `one` 的任何 target 也必须包含该目录。

```cmake
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

Target 的属性包括：Include 目录、链接库、编译选项、预定义宏等

```cmake
cmake_minimum_required(VERSION 3.8)

project(Calculator LANGUAGES CXX)

add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)
```