---
title: 🤖 Docker 容器
linkTitle: Docker
summary: 容器技术
date: '2022-10-15'
type: book
tags:
  - virtualization
---

{{< toc hide_on="x1" >}}

## Docker 学习路线图

{{< figure src="https://img.icons8.com/fluency/1000/000000/docker.png" >}}

## 各个组件之间的关系
{{< figure src="https://img-blog.csdnimg.cn/20201224163957704.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Fkb3JhYmxlXw==,size_16,color_FFFFFF,t_70" >}}

## Docker运维流程图

{{< figure src="https://img-blog.csdnimg.cn/20201224164351843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Fkb3JhYmxlXw==,size_16,color_FFFFFF,t_70" >}}


## Docker 常用命令
{{< figure src="https://img-blog.csdnimg.cn/2020122421132889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Fkb3JhYmxlXw==,size_16,color_FFFFFF,t_70#pic_center" >}}

## 仓库（Respository）
- Docker 仓库是集中存放镜像文件的场所。镜像构建完成后，可以很容易的在当前宿主上运行，但是， 如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry （仓库注册服务器）就是这样的服务。
- 通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本 。我们可以通过<镜像名>:<标签>的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。

--- 

## 镜像（Image）
  - Docker 把应用程序及其依赖，打包在 image 文件里面。只有通过这个文件，才能生成 Docker 容器。image 文件可以看作是容器的模板。
  - image 文件是通用的，一台机器的 image 文件拷贝到另一台机器，照样可以使用。
  - 一般来说，为了节省时间，我们应该尽量使用别人制作好的 image 文件，而不是自己制作。即使要定制，也应该基于别人的 image 文件进行加工，而不是从零开始制作。通常可以通过docker file来构造image镜像。

---

## 容器（Container）
  - Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例，容器是可以修改的，可以将容器当前的状态保存到对应的镜像中。

---
