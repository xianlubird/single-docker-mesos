## xianlubird/single-docker-mesos
基于Docker快速搭建单节点Mesos/Marathon集群,增加支持marathon管理运行docker功能

- GitHub地址：[xianlubird/single-docker-mesos](https://github.com/xianlubird/single-docker-mesos)


##一. 简介

[Mesos](http://mesos.apache.org)是集群资源管理系统，[Marathon](http://mesosphere.github.io/marathon)是运行在Mesos之上的集群计算架构。将Mesos和Marathon打包到[Docker](https://www.docker.com/)镜像中，开发者便可以在本机上快速搭建Mesos/Marathon集群，进行学习和测试。

**kiwenlau/single-mesos**镜像非常简单。Docker容器运行在Ubuntu主机之上，Mesos和Marathon运行在该容器之中。具体来讲，Docker容器中运行了一个Mesos Master和一个Mesos Slave，以及Marathon和[ZooKeeper](https://zookeeper.apache.org/)。集群架构如下图：

![](https://github.com/kiwenlau/single-mesos-docker/raw/master/image/architecuture.png)


##二. 搭建Mesos/Marathon集群

**1. 下载Docker镜像:**

```sh
docker pull index.alauda.cn/a352193394/single-docker-mesos
```

**2. 运行Docker容器:**

```sh
docker run -p 8080:8080 -p 5050:5050 --name mesos -ti -w /root -v /sys/fs/cgroup:/sys/fs/cgroup -v /var/run/docker.sock:/var/run/docker.sock mesos-single

```

docker run命令运行成功后即进入容器内部，以下为输出：

```
Start ZooKeeper...
Start Mesos master...
Start Mesos slave...
Start Marathon...
```


##三. 测试Mesos/Marathon集群

**1. 通过curl命令调用Marathon的REST API, 创建一个hello程序：**

```sh
curl -v -H "Content-Type: application/json" -X POST --data "@hello.json" http://127.0.0.1:8080/v2/apps
```

下面为hello.json。这个程序使用docker运行了一个简单的python服务。

```
{
  "id": "basic-3",
  "cmd": "python3 -m http.server 8080",
  "cpus": 0.5,
  "mem": 32.0,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "python:3",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 8080, "hostPort": 0 }
      ]
    }
  }
}

```






