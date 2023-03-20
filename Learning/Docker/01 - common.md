```sh
# 显示当前正在运行的 docker 进程
$ sudo docker ps
[sudo] password for gaojiangfeng: 
CONTAINER ID   IMAGE                    COMMAND        CREATED              STATUS              PORTS                                                                                  NAMES
0e9121e71a81   ubuntu:16.04             "/bin/bash"    About a minute ago   Up About a minute                                                                                          ubuntu16

# 将 ubuntu:16.04 image 中的更改，保存在 u16v 中
$ sudo docker commit 0e9121e71a81 u16v
```