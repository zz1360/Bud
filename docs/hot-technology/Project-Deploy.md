# **项目部署专题**

#### 5.11.1 Linux

常见的linux命令（脱口而出，5秒说出10个面试时才ok）

##### 常用指令

ls          显示文件或目录

   -l        列出文件详细信息l(list)

   -a        列出当前目录下所有文件及目录，包括隐藏的a(all)

mkdir       创建目录

   -p        创建目录，若无父目录，则创建p(parent)

cd       切换目录

touch      创建空文件

echo      创建带有内容的文件。

cat       查看文件内容

cp       拷贝

mv       移动或重命名

rm       删除文件

   -r        递归删除，可删除子目录及文件

   -f        强制删除

find       在文件系统中搜索某文件

wc       统计文本中行数、字数、字符数

grep      在文本文件中查找某个字符串

rmdir      删除空目录

tree      树形结构显示目录，需要安装tree包

pwd      显示当前目录

ln       创建链接文件

more、less     分页显示文本文件内容

head、tail     显示文件头、尾内容

ctrl+alt+F1     命令行全屏模式

##### 系统管理命令

stat       显示指定文件的详细信息，比ls更详细

who       显示在线登陆用户

whoami      显示当前操作用户

hostname     显示主机名

uname     显示系统信息

top        动态显示当前耗费资源最多进程信息

**ps**       **显示瞬间进程状态** **ps -aux**

du        查看目录大小 du -h /home带有单位显示目录信息

df        查看磁盘大小 df -h 带有单位显示磁盘信息

ifconfig      查看网络情况

ping       测试网络连通

netstat      显示网络状态信息

man       命令不会用了，找男人 如：man ls

clear      清屏

alias       对命令重命名 如：alias showmeit="ps -aux" ，另外解除使用unaliax showmeit

kill        杀死进程，可以先用ps 或 top命令查看进程的id，然后再用kill命令杀死进程。

##### 打包压缩相关命令

gzip：

bzip2：

tar:        打包压缩

   -c       归档文件

   -x       压缩文件

   -z       gzip压缩文件

   -j       bzip2压缩文件

   -v       显示压缩或解压缩过程 v(view)

   -f       使用档名

例：

tar -cvf /home/abc.tar /home/abc       只打包，不压缩

tar -zcvf /home/abc.tar.gz /home/abc    打包，并用gzip压缩

tar -jcvf /home/abc.tar.bz2 /home/abc   打包，并用bzip2压缩

当然，如果想解压缩，就直接替换上面的命令  tar -cvf / tar -zcvf / tar -jcvf 中的“c” 换成“x” 就可以了。

##### Linux管道

将一个命令的标准输出作为另一个命令的标准输入。也就是把几个命令组合起来使用，后一个命令除以前一个命令的结果。

例：grep -r "close" /home/* | more    在home目录下所有文件中查找，包括close的文件，并分页输出。

##### 文件权限管理

三种基本权限

R      读      数值表示为4

W     写      数值表示为2

X      可执行     数值表示为1

#### 5.11.2 jenkins

Jenkins是一个领先的开源自动化服务器，可用于自动化构建，测试，部署软件等相关任务。jenkins可以很好的支持各种语言（比如：java, c#, php等）的项目构建，也完全兼容ant、maven、gradle等多种第三方构建工具，同时跟svn、git能无缝集成，也支持直接与知名源代码托管网站，比如github、bitbucket直接集成。

Jenkins只是一个平台，真正运作的都是插件。这就是jenkins流行的原因，因为jenkins什么插件都有 

Hudson是Jenkins的前身，是基于Java开发的一种持续集成工具，用于监控程序重复的工作，Hudson后来被收购，成为商业版。后来创始人又写了一个jenkins，jenkins在功能上远远超过hudson

#### 5.11.3 docker

##### 5.11.3.1 Docker是什么？

Docker是一个虚拟环境容器，可以将你的开发环境、代码、配置文件等一并打包到这个容器中，并发布和应用到任意平台中。比如，你在本地用Python开发网站后台，开发测试完成后，就可以将Python3及其依赖包、Flask及其各种插件、Mysql、Nginx等打包到一个容器中，然后部署到任意你想部署到的环境。

 

Docker官方文档比较全，建议有能力的读一下官方文档。

##### 5.11.3.2 Docker的三个基本概念

![https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181010205425908-509725301.jpg](file:///C:/Users/79287/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

从上图我们可以看到，Docker 中包括三个基本的概念：

###### Image(镜像)

###### Container(容器)

###### Repository(仓库)

镜像是 Docker 运行容器的前提，仓库是存放镜像的场所，可见镜像更是 Docker 的核心。

镜像（Image）：类似于虚拟机中的镜像，是一个包含有文件系统的面向Docker引擎的只读模板。任何应用程序运行都需要环境，而镜像就是用来提供这种运行环境的。例如一个Ubuntu镜像就是一个包含Ubuntu操作系统环境的模板，同理在该镜像上装上Apache软件，就可以称为Apache镜像。

容器（Container）：类似于一个轻量级的沙盒，可以将其看作一个极简的Linux系统环境（包括root权限、进程空间、用户空间和网络空间等），以及运行在其中的应用程序。Docker引擎利用容器来运行、隔离各个应用。容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响。注意：镜像本身是只读的，容器从镜像启动时，Docker在镜像的上层创建一个可写层，镜像本身不变。

仓库（Repository）：类似于代码仓库，这里是镜像仓库，是Docker用来集中存放镜像文件的地方。注意与注册服务器（Registry）的区别：注册服务器是存放仓库的地方，一般会有多个仓库；而仓库是存放镜像的地方，一般每个仓库存放一类镜像，每个镜像利用tag进行区分，比如Ubuntu仓库存放有多个版本（12.04、14.04等）的Ubuntu镜像。

##### 5.11.3.3 docker的架构

Docker 使用的是 C/S 结构，即客户端/服务器体系结构。明白了 Docker 客户端与 Docker 服务器进行交互时， Docker 服务端负责构建、运行和分发 Docker 镜像。 也知道了Docker 客户端和服务端可以运行在一台机器上，可以通过 RESTful 、 stock 或网络接口与远程 Docker 服务端进行通信。

我们从下图可以很直观的了解到Docker的架构：

![https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181012214103608-969706945.png](file:///C:/Users/79287/AppData/Local/Temp/msohtmlclip1/01/clip_image004.gif)

 

Docker 的核心组件包括：

1、Docker Client

2、Docker daemon

3、Docker Image

4、Docker Registry

5、Docker Container

##### 5.11.3.4 Dockerfile常用的指令

搜索    docker search *image_name*

下载    docker pull *image_name*

查看    docker images   docker image ls

删除    docker rmi *image_name* docker image rm *image_name*

导出docker镜像 docker save *image_name* > /save/image/path/name.tar.gz

导入docker镜像 docker load < /save/image/path/name.tar.gz

提交创建的自定义的镜像  docker commit *container_id* *镜像名称*

使用运行的容器执行命令  docker exec *container_id* command

查看容器    docker ps   docker ps –a docker ps –aq

停止容器    docker stop *container_id*

删除容器    docker rm *container_id*

#### 5.11.4 项目部署上线

一句话，就是把本地开发的打成jar/war包丢到服务器上运行起来。

传统的ssm/ssh项目是打成war包丢到服务器上的tomcat下的webapps下面，然后运行tomcat就行；springboot项目打成jar包丢到服务器上，执行java –jar xxx.jar启动项目就行