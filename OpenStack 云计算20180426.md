### 企业IaaS私有云，商业软件有 VMWare vSphere, 开源软件 OpenStack、CloudStack。OpenStack是由NASA和著名的服务器托管商Rackspace发起的一个开源项目，2010年推出第一个版本。相对于其它开源云平台，OpenStack的license最友好，项目活跃度最高，参与开发人员最多。OpenStack被喻为云时代的Linux。

### 几乎所有的大型IT公司，比如IBM、HP、Oracle都已经推出了自己的基于OpenStack的解决方案。也有很多著名的公有云服务是运行在OPenStack之上，比如国外的Rackspace和HP的Public Cloud。国内的OpenStack用户也逐步在增长，先有瞬联软件、趣游、网易等分别尝试基于OpenStack开发部署自己的云平台，之后爱奇艺、用友、京东、百度、360、美团等也纷纷选用OpenStack。

## OpenStack的主要功能

1. 管理和虚拟化相关资源（CPU、内存、硬盘、网络），提高资源利用率
2. 管理局域网（DHCP、VLAN、IPv6），为应用程序提供灵活的网络模式
3. 带有身份认证和资源使用限额控制，容易管理接入用户，阻止非法访问
4. 分布式和异步体系结构，提供高弹性和高可用性系统
5. 虚拟机镜像管理，可以存储、导入、共享和查询虚拟机镜像
6. 虚拟机实例管理，用户可以暂停、重启虚拟机，也可以方便地对虚拟机做扩容
7. 创建和管理实例类型（Flavors），用户可以通过菜单选择要创建的虚拟机的大小
8. iSCSI存储器管理，做到数据与虚拟机分离，容错能力变强，更加灵活
9. 在线迁移虚拟机实例
10. 动态IP地址分配
11. 安全分组，定制安全访问规则，灵活分配、控制接入虚拟机实例
12. 通过浏览器的VNC代理，快速访问虚拟机实例

## OpenStack的架构

OpenStack的设计遵循松耦合的原则，由多个核心组件组成，每个组件可以单独部署，极大地提高了系统的可扩展性（目前国外与已有数千台物理机的OpenStack）。

Hprizon仪表盘，显示了一个可以为用户和管理员来管理OpenStack服务的用户界面。
Nova提供了一个可伸缩的计算平台，用来支持大量服务器和虚拟机的配置和管理。
Swift实现一个具有内部冗余、可大量伸缩的对象存储系统。
底部的Neutron，实现了网络连接即服务 （Network Connectivity as a service）。
最后，Glance项目为虚拟机磁盘映像实现了一个存储库（映像即服务，Image as a service）。

## OpenStack各组件之间的关系

Compute （Nova），它提供了一个工具来部署云，包括运行实例、管理网络以及控制用户对云的访问。它对外提供API，用户可以用API来完成创建虚拟机等工具。对内可以使用不同的虚拟化技术，例如Linux KVM、VMWare和Xen。

OpenStack Object Storage (Swift)，是一个可扩展的对象存储系统。对象存储支持多种应用，比如复制和存档数据，图像或视频服务。

OpenStack Image Service (Glance)，是一个虚拟机镜像的存储、查询和检索系统。

各组件之间通过webservice或消息队列通讯。一个典型的创建虚拟机的过程是：

1. 用户首先登陆进OpenStack系统
2. 用户在管理界面Dashboard中选择一个操作系统镜像，比如Ubuntu Linux 14.04,然后选择要创建的虚拟机的大小，比如2CPU，2GB内存，100G磁盘空间，然后按“确认”键开始创建。Dashboard将这个请求通过webservice发送给Nova。
3. Nova通过Glance中找到Ubuntu Linux 14.04的镜像，镜像存放在Swift中
4. Nova从镜像启动新的虚拟机
5. Nova和Neutron通讯，为新生成的虚拟机获取IP地址
6. Nova将新生成的虚拟机的信息，包括IP，返回给Dashboard，用户就可以用这个IP来访问这个新创建的虚拟机了。 


OpenStack还提供完备的API接口，支持的语言有Java、Python、Ruby、Node.JS等等，方便用户用程序来完成创建虚拟机的工作。

## OpenStack的私有云部署

1. OpenStack的部署难点
在OpenStack项目实施中，OpenStack的部署是一个难点。这是因为：

  OpenStack采用松耦合的设计，包含的组件非常多，比如六大组件：Keystone、Horizon、Nova、Glance、Swift、Neutron。每个组件又包含多个进程，比如Nova包含nova-api、nova-compute、nova-scheduale、nova-conductor等进程。部署OpenStack时，不同的组件或进程需要安装在不同的服务器上，导致安装配置十分复杂。
  
  OpenStack配置十分灵活，每个组件都有自己的配置文件，每个配置文件中包含上百个配置项，正确配置OpenStack是一个十分艰巨的任务。
  
  OpenStack不包含标准的HA功能，只能用第三方的开源软件实现，增加了配置的复杂度。
  
## OpenStack HA高可用方案

OpenStack是由很多不同的组件构成的，他们实现高可用的方式也各不相同，Fuel提供了一套完整的高可用方案。

![alt text](https://github.com/bakerX/Diary/blob/master/images/openstack-HA.jpg)

* OpenStack API服务，例如nova-api，glance-api，nova-api用HAProxy和pacemaker实现
* OpenStack的web管理界面Horizon，也使用HAProxy和pacemaker实现
* 消息队列服务RabbitMQ 使用自己的高可用功能，实现active/active高可用
* MySQL数据库服务器的高可用通过Galera实现，提供active/active高可用
* 多台Compute node本身就具有高可用特性，不需要特别实现。

控制节点实现高可用需要至少3台物理服务器。

## 一个典型的OpenStack HA硬件配置方案

* 一个千兆以太网交换机接管理网络，一个万兆以太网交换机接数据网络
* 一个服务器作为Fuel master server
* 三台服务器作为OpenStack控制节点
* 10 ~ 20 台服务器作为OpenStack的计算节点
* 5台服务器作为OpenStack的存储节点

具体服务器如下配置所示，整个机柜满配，支持超过1000个虚拟机同时运行。

![alt text](https://github.com/bakerX/Diary/blob/master/images/openstack-rack.jpg)

![alt text](https://github.com/bakerX/Diary/blob/master/images/openstack-spec.jpg)

* 计算节点需要大量的内存来创建虚拟机，建议配置128G内存
* 计算节点和存储节点需要大量的硬盘来存储用户数据，由于数量比较大，采购时可以考虑性比价比比较高的1TB的硬盘
* 推荐的服务器型号包括： Dell PowerEdge R620、IBM System X3650、HP Proliant DL160 

医疗行业因为其独特的商业价值、隐私保护和社会公益属性，注定了不可能像普通电商、游戏或者工具软件那样，全部可以采用公有云服务，私有云服务
不但不会消失，还会有更大的市场机会，移动医疗企业既要关注与外网的公有云信息交互，也要留有接口考虑与医院内部私有云的互通互联。同时医疗私有云可以通过技术路线与公有云进行有控制的交互。某种程度来说，类似华为的“敏捷网络”产品，内网、外网、移动互联体系“三网合一”，受控跳转。 
 

出处： https://xueqiu.com/u/4910651626 
  
  
