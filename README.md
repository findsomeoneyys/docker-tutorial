# docker-tutorial
此教程目的是让读者能了解Docker基本结构概念与常用操作，最后以部署Django uwsgi nginx应用作为练习，展示一个Docker项目的基本构建流程。


## 写在开头
如果读者对Docker并无相关了解，建议阅读以下相关文章简单的了解下产生背景以及作用。

Docker是伴随着[DevOps](https://zh.wikipedia.org/wiki/DevOps)等相关概念火起来的，具体优势可以通过阅读知乎相关回答了解:
- [Docker 有什么优势？](https://www.zhihu.com/question/22871084)
- [Docker 的应用场景在哪里？](https://www.zhihu.com/question/22969309/answer/38317063)
- [八个Docker的真实应用场景](http://dockone.io/article/126)

相信阅读过后对Docker会有初步了解。

## 前置条件
读者需要自行根据[官方安装文档](https://docs.docker.com/install/)自行安装Docker和Docker compose
教程中使用的Docker版本为18.06.1-ce

## 教程
这篇教程分成以下三部分，读者可以根据需要自行选读。

- [了解Docker基本结构与操作](./tutorial/了解Docker基本结构与操作.md)
- [Docker运行时参数讲解与docker-compose](./tutorial/Docker运行时参数讲解与docker-compose.md)
- [Docker实战-Django uwsgi nginx](./tutorial/Docker实战-Django-uwsgi-nginx.md)

