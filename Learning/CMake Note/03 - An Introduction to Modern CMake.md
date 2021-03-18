## Installing CMake
```sh
sudo snap install cmake --classic
```

## Running CMake
- 尝试使用 `--build` 参数来摆脱 `make` 依赖
```sh
cmake -S . -B build
#相当于
mkdir build && cd build && cmake ..

#----------------
cmake --build [path]
#相当于
make



# From the build directory (pick one) 
make install 
cmake --build . --target install 
cmake --install . # CMake 3.15+ only  

# From the source directory (pick one) 
cmake --build build --target install
```

### 配置参数
- 使用 `-D` 配置 option 选项
- 使用 `-L / -HL` 查看当前支持的配置选项

### verbose
```sh
VERBOSE=1 make #命令行
make VERBOSE=1 #传参实现
```

### 常见 CMake 选项

-   `-DCMAKE_BUILD_TYPE=` Pick from Release, RelWithDebInfo, Debug, or sometimes more.
-   `-DCMAKE_INSTALL_PREFIX=` The location to install to. System install on UNIX would often be `/usr/local` (the default), user directories are often `~/.local`, or you can pick a folder.
-   `-DBUILD_SHARED_LIBS=` You can set this `ON` or `OFF` to control the default for shared libraries (the author can pick one vs. the other explicitly instead of using the default, though)
-   `-DBUILD_TESTING=` This is a common name for enabling tests, not all packages use it, though, sometimes with good reason.
## Do's and Don'ts
- blablabla