# 每个Dockerfile必须以一个FROM开头，后面跟着一个镜像名
# 镜像是叠加的，比如这段代码中使用的以stretch版本的debain作为基础，再装入python 3.6.6的镜像
FROM python:3.6.6-stretch

# 以下命令在镜像中创建了一个/app目录，并把我们项目文件放入其中
RUN mkdir /app
COPY app.py /app
COPY requirements.txt /app

# 装入依赖
RUN pip install -r /app/requirements.txt
# 通过设置WORKDIR，镜像启动时会自动进入该目录
WORKDIR /app
# flask默认监听5000端口，所以我们暴露出5000端口让外部可以访问
EXPOSE 5000

# 镜像启动时会自动执行CMD内容，这里我们让flask启动起来
# CMD 只能有一个，当有多个时，只会执行最后一个
CMD ["python", "app.py"]
