## 0 Summary
- **Modern CMake**
> 开源社区非常流行的build system generator，通过执行CMakeLists生成makefile/ninja等，构建C++项目。尤其是2013年以来Modern CMake的发展，理念是：一切皆为target or target property。Modern CMake通过target之间的依赖关系，实现target属性(头文件查找路径、编译选项、链接依赖)内部隐藏或者自动传递，可以非常方便地处理大型C++项目的复杂依赖关系。
> 相比于makefile，Modern CMake大大解放了C/C++研发人员的生产力。

- 核心问题在于能够对模块化的对象，进行更加细节的配置。从而保证多个模块组合时，耦合度降低。

## 1 Target 相关属性
### 1.1 `target_compile_options()` 指定 Target 的编译选项
```cmake
target_compile_options(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```
-   **PRIVATE**，编译选项会应用于给定的目标，不会传递给与目标相关的目标。
-   **INTERFACE**，给定的编译选项将只应用于指定目标，并传递给与目标相关的目标。
-   **PUBLIC**，编译选项将应用于指定目标和使用它的目标。

#### 1.1.1 使用生成器表达式
- **逻辑表达式**，`$<condition:outcome>`。
- **信息表达式**，`$<information>`或`$<information:input>`(获取information的input 属性)
- **输出表达式**，`$<operation>`或`$<operation:input>`。
```cmake
target_compile_option(compute-areas
  PRIVATE
    ${CXX_FLAGS}
"$<$<CONFIG:Debug>:${CXX_FLAGS_DEBUG}>"
"$<$<CONFIG:Release>:${CXX_FLAGS_RELEASE}>"
)
```
#### 1.1.2 `target_compile_features`
- `-std=gnu++11`

### 1.2 `set_target_properties`

#### 1.2.1 C++11
- 设置全局属性
```cmake
set(CMAKE_CXX_STANDARD 11 CACHE STRING "The C++ standard to use")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
```

- 设置 Target 属性
```cmake
set_target_properties(myTarget PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
)
```

### 1.3 `target_compile_definitions`
- 在预处理阶段，为 Target 添加定义；
- `add_definitions` 仅能设置全局定义
```cmake
target_compile_definitions(<target>
    <INTERFACE|PUBLIC|PRIVATE> [items1...]
    [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])

# e.g.
target_compile_definitions(foo PUBLIC FOO)
target_compile_definitions(foo PUBLIC "COMPILER_NAME=\"${CMAKE_CXX_COMPILER_ID}\"")
```
-   **PRIVATE**，定义将只应用于给定的目标，而不应用于相关的其他目标。
-   **INTERFACE**，对给定目标的编译定义将只应用于使用它的目标。
-   **PUBLIC**，编译定义将应用于给定的目标和使用它的所有其他目标。

### 1.4 `target_sources`
```cmake
target_sources(<target>
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```

#### 指定 Target 包含的头文件路径
```cmake
target_include_directories(<target> [SYSTEM] [AFTER|BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```


