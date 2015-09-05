
# 希云cSphere安装
1. 控制器(Controller)，负责资源调度和提供各种管理API，并提供管理面板的前端页面服务
2. Agent，负责收集docker宿主机的系统信息并协助控制器与各个docker daemon进行通信

## 前提条件
1. 每台运行docker的主机到控制器所在主机之间的网络必须能够连通
2. docker版本必须在1.6.0以上
3. 仅支持64位系统，已测试可在以下操作系统下正常运行：

  * CentOS 7+
  * Ubuntu 14.04+
  * Debian 7+
  * CoreOS
  * Boot2Docker


# 安装步骤
cSphere的安装脚本将会自动检查并安装Docker，如果你的机器已经安装了Docker，请确保版本号。

*注意，docker 1.7.1有严重bug，请勿使用。*

### CentOS系列提醒
CentOS/Redhat/Fedora/AMI系统，使用devicemapper作为存储后端，但注意使用devicemapper必须分配两个独立的磁盘分区给docker使用，loopback模式100%会出现故障，请避免使用！

编辑 /etc/sysconfig/docker配置文件，注意替换里面的basesize,datadev和metadatadev

```
DOCKER_OPTS="--storage-driver=devicemapper --storage-opt dm.basesize=50G --storage-opt dm.datadev=/dev/sde1 --storage-opt dm.metadev=/dev/sdf1"
```

注解：

- basesize 表示镜像和容器的空间大小，不同容器如果使用相同镜像，不会增加大小。容器内的修改应用COW技术，修改后的内容不计算此空间中，默认10G，建议basesize设为datadev设备分区大小的1/3
- datadev 存放镜像和容器，要求必须是ext4或xfs文件系统，默认ext4，如果设为xfs，使用dm.fs指定
- metadatadev 用于存放元数据，对空间要求不大，可建立一个10G的分区。建议和datadev使用不同设备，如果条件许可，使用SSD，使用时需要将分区前4k清0。 ```dd if=/dev/zero of=$metadata_dev bs=4096 count=1```



### 部署控制器

```bash
curl -SsL -o /tmp/csphere-install.sh https://csphere.cn/static/csphere-install-v2.sh
sudo env ROLE=controller CSPHERE_VERSION=0.13.0 /bin/sh /tmp/csphere-install.sh
```

### 部署Agent
打开浏览器，访问controller A主机的1016端口，第一次访问填入管理员邮箱密码注册，即可看到控制台的界面。
点击左侧的“主机”菜单，进入主机列表页面，点击“添加主机”并复制脚本，在Agent主机安装Agent程序，即可开始希云cSphere旅途。

任何建议或意见，欢迎反馈到 contactus@nicescale.com 或 [希云用户社区](http://discuss.csphere.cn/)
