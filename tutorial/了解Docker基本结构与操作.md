# Docker基本结构与操作

## 基本概念
要想顺利使用Docker，那么我们首先要了解几个基本名词：镜像(image),容器(container),仓库(Registry)

### 镜像(image)
官方对于镜像是这样定义的：
> A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
> 
在一个Docker项目周期内，镜像的功能主要是这两点：

- 镜像包含了要执行的应用所需要的全部环境，这个是在**Dockerfile**中定义的，稍后的教程中我们会提到这个，在这里先简单的理解**Dockerfile**是镜像的制作文件。
- 镜像充当了一个模板的角色，当一个docker项目启动时，镜像经由**Docker Engine**处理转化成容器。

### 容器(container)
正如我们刚刚提到的，镜像经由**Docker Engine**处理转化成容器。所以容器可以理解为是镜像的实例，一个镜像可以创造出多个不同的容器。
容器可以被创建，启动,停止,删除，并且他们之间是独立的。

### 仓库(Registry)
仓库可以简单的理解成类似pip之类的东西，仓库可以分享Image。
仓库有官方的[Docker Hub](https://hub.docker.com/),也有自己或者别人搭建的私有仓库。


## 镜像创建和执行
镜像创建有两种方式，一个是刚刚提到过的通过**Dockferfile**创建，另外还有一种方式是通过对容器的commit，把该容器转化成一个镜像，但是官方并不提倡使用这个方式。这里着重介绍较为常用的**Dockferfile**，构建一个简单的flask-app。相关代码可以在code目录中获取。

首先准备`requirements.txt`和`app.py`

```
requirements.txt

Flask==1.0.2
```

```
app.py


from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

接着创建一个Dockerfile：
```
# 每个Dockerfile必须以一个FROM开头，后面跟着一个镜像名
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

```
简单的编写Dockerfile并不太复杂，大部分命令与我们在Linux下shell操作是一致的。我们只要简单的记住这几个要点就可以满足基本需求，更多的参数可以参考官网的[Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage)

1. Dockerfile必须以`FROM {image}`开头，这个代表我们接下来的操作是基于某个镜像的，这个步骤就好比我们ssh到某一个特定环境的机器中。
2. 接下来操作一般就是准备项目启动需要的环境，这个步骤中我们用到比较频繁的命令分别是`RUN`,`COPY`,还有相对频率不太高的`WORKDIR`,`EXPOSE`,`CMD`，
`RUN`的意思就是执行一条命令，这个与shell操作一致，就好比我们刚提到的ssh到特定环境的机器中，然后安装项目需要的库而执行`pip install -r requirements.txt`，
`COPY`顾名思义就是复制，主要就是把你本地的文件复制到镜像当中。
`WORKDIR`设置一个工作目录，设置之后呢，当镜像启动的时候，会自动进入该目录，只能有一个，重复定义则只取最后一条定义的。
`EXPOSE`暴露端口，就像我们刚刚说到的，是在一个特定环境的机器中，那么我们发布web application时，也需要允许外部访问该端口才能和app交互。
3. `CMD`收尾，`CMD`就是当镜像时执行的命令，一般是项目的运行命令。只能有一个，重复定义则只取最后一条定义的。在我们刚刚的Dockerfile中，定义了`CMD ["python", "app.py"]`，此时，当我们执行这个镜像时，会自动执行这个命令启动我们的flask app

Dockfile写好了，接下来我们要制作镜像，在同级目录下执行以下命令：
`docker build -t flask-hello .`
这个命令中用了一个`-t`参数，这个参数的意思是tag，一般规则是 镜像名字[:tag]，标签就好比我们git里面的分支，比如`python:3.5`，`python:3.6`我们很容易可以明白都是python环境，但是一个是3.5版本的，一个是3.6版本的。
执行后，命令行会输出执行结果，看到以下结果时，就代表我们构建成功了。
```
....
Successfully built eb678b213b41
Successfully tagged flask-hello:latest
```
这时候我们执行刚构建好的镜像，在命令中执行`docker run -p 5000:5000 flask-hello`，会输出如下：
```
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```
这与我们平时启动flask行为一致，此时打开浏览器访问[127.0.0.1:5000](http://127.0.0.1:5000/)，即可看到Hello World!

## 镜像执行
刚才通过执行`docker run -p 5000:5000 flask-hello`，紧接着我们便可以通过访问[127.0.0.1:5000](http://127.0.0.1:5000/),与我们的flask app交互,接下来我们探讨下中间发生了什么。

在前面的概念介绍中，提到了镜像充当的是模板角色，当镜像被执行时，Docker Engine会根据此镜像生成一个容器。镜像与容器就好比是类与类的实例关系。他们之间最大的区别就是镜像是只读的，但是容器是可以读写的，就好像类只是一个的定义，但是类的实例有各自的属性。我们管理Docker应用的时候，是以容器为单位的。

在刚刚执行的命令中我们提供了一个 `-p 5000:5000` ，这个参数的意思相当于绑定端口，左边的是指我们本地机器上的端口，右边的是指容器内的端口。这是因为容器是独立的，这个独立是指相对本地机器与其他容器，这个容器就像是一台单独的机器，所以执行这个绑定操作，我们可以通过访问本地机器的5000端口访问到容器里的5000端口，如果我们修改成`5050:5000`，就变成访问本地机器的5050端口访问到容器内的5000端口了。

之前一直提到镜像与容器就好比是类与类的实例，那么我们很容易可以联想到，通过提供不同参数，这个flask app是不是可以运行在复数以上的端口，并且各自独立呢，这当然是可以的。我们新打开一个命令行，执行`docker run -p 5050:5000 flask-hello`，这时候我们不管访问本地的5050或者5000端口都可以访问到我们的flask app，并且他们之间是独立的

## 推送镜像

我们制作好镜像后可以推送到仓库中，同时也可以使用别人已经制作好的镜像。
具体推送过程可以参考官网的[推送镜像](https://docs.docker.com/docker-cloud/builds/push-images/),注意的是如果镜像包含隐私或者不能暴露的源码的话，请不要推送到Docker hub，而是留在本地或者推送到私有仓库。

使用别人的镜像并不复杂，这里以使用Docker hub的镜像为例，只需把run命令中`image`替换成`$DOCKER_ID_USER/image`即可。
本次教程中制作的镜像已经推送上去,读者可以执行以下命令实验：
`docker run -p 5000:5000 xxx008/flask-hello`