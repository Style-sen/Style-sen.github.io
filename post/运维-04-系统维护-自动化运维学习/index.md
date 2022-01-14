# 自动化运维学习（AutoDevOps实践）


参考：[CodePipeline > 产品简介 > 应用场景](https://help.aliyun.com/document_detail/56518.html?spm=a2c4g.11186623.6.545.4ac25c2eETlStm)    
参考：[AliyunContainerService/DevOps](https://github.com/AliyunContainerService/DevOps)

## CodePipeline功能替代

    CodePipeline使用本地Gitlab服务CE版自带的功能。完成编译构建、部署测试、生产交付。

## 阿里云镜像服务功能替代

    可以将上一步生产交付的镜像文件存于本地Docker Registry中，提供拉取。

## 阿里云容器服务替代

    构建私有云生产环境，得到webhook通知之后，拉取镜像，更新容器。可使用Kubernetes集群管理容器化的应用。

## 阿里云服务器ECS

    1. 使用saltstack[GETTING STARTED WITH ALIYUN ECS](https://docs.saltstack.com/en/latest/topics/cloud/aliyun.html)


