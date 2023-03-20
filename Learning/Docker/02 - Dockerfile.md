```dockerfile
# 安装依赖
RUN pip3 install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip3 install gunicorn -i https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip3 install gevent -i https://pypi.tuna.tsinghua.edu.cn/simple

# 更新apt源
#RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
#RUN sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN apt-get update
```

- 通常使用 supervisor 管理后台进程
```dockerfile
RUN apt-get update && apt-get install -y supervisor

# supervisord https://www.jianshu.com/p/0b9054b33db3
RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
```

