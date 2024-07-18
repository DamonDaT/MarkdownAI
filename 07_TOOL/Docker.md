### 【Docker】

***

记录 Docker 常用知识点

***





### 【一】环境

***

##### 【1.1】登录

```shell
docker login -u [user] -p [password] http://my.mirror.com/
```

##### 【1.2】登出

```shell
docker logout http://my.mirror.com/
```

##### 【1.3】查看信息

```shell
docker info
```

***





### 【二】镜像基础

***

##### 【2.1】查看

```shell
docker images [-a] [-q] [-f dangling=true]
```

##### 【2.2】拉取

```shell
docker pull [my.mirror.com/xxx]
```

##### 【2.3】推送

```shell
docker push [my.mirror.com/xxx:tag]
```

##### 【2.4】删除

```shell
docker rmi [image_id / image_id_1 image_id_2 ...]
```

***





### 【三】容器基础

***

##### 【3.1】查看

```shell
docker ps [-a] [-q] [-f status=exited]
```

##### 【3.2】创建

```shell
docker run [-d] [-it] [-p xxx:yyy] [--network=host] [image_id] bash
```

##### 【3.3】创建并启动

```shell
docker run -d [-p xxx:yyy] [image_id] bash -c "while true; do sleep 30; done"
```

##### 【3.4】进入

```shell
docker exec -it [container_id] bash
```

##### 【3.5】启动

```shell
docker start [container_id / container_id_1 container_id_2 ...]
```

##### 【3.6】停止

```shell
docker stop [container_id / container_id_1 container_id_2 ...]
```

##### 【3.7】删除

```shell
docker stop [container_id / container_id_1 container_id_2 ...]
```

***





### 【四】高阶用法

***



##### 【4.1】更改挂载路径

***

* 查看当前挂载路径

```shell
docker info | grep 'Docker Root Dir'
```

* 停止 docker

```shell
sudo systemctl stop docker // sudo snap stop docker
```

* 备份现有容器信息，如果没有镜像的话可以不用备份，没试过这个命令

```shell
cp -rp /var/snap/docker/common/var-lib-docker /var/snap/docker/common/var-lib-docker.backup
```

* 创建新的 docker 根目录

```shell
mkdir /new/path/to/docker
```

* 拷贝现有的 docker 数据到新目录下

```shell
sudo rsync -aqxP /var/snap/docker/common/var-lib-docker/ /new/path/to/docker
```

* 修改守护进程文件的配置，将 data-root 的值改为 /new/path/to/docker

```shell
vi /snap/docker/current/config/daemon.json
```

* 启动 docker

```shell
sudo systemctl start docker // sudo snap start docker
```

***



##### 【4.2】容器转镜像

***

```shell
docker commit [container_id] my.mirror.com/xxx/yyy:[new_version]
```

***



