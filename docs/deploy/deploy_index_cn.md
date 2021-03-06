# Loopring部署文档

> relay-cluster相关服务都部署在aws，部分功能强依赖aws提供的相关服务，如果采用其他云服务提供商，可能会造成部分功能不可用，或出现一些非预期的异常。

> relay-cluster及其依赖的extracor服务，都需要通过集群的方式进行部署来避免单点故障。虽然可以只部署单个节点，但是单节点的正常启动依然需要依赖于组成集群的基础组件，包括zookeeper，kafka

下面按照依赖的顺序进行介绍，先介绍被依赖的服务

## 通用
部署任何服务前要先选择合适配置的EC2实例启动，后续服务会部署在该实例上。实例启动后还需要关联自定义安全组以允许实例之间进行特定端口的通信。

* [EC2实例](new_ec2_cn.md)

启动合适性能的EC2实例，并可根据部署服务的类型，在启动向导中进行必要的配置，减少后续的二次操作。

* [aws安全组](security_group_cn.md)

通过安全组限制非预期的端口访问，提高服务的安全性。该文档集中定义了后端所有依赖的安全组配置，可以按照顺序进行配置。

## 存储及通信
* [ethnode](deploy_geth_cn.md)

relay-cluster会通过和geth节点的交互来实现eth网络的接入

* [mysql](deploy_mysql_cn.md)

是relay-cluster的主要后台存储，存储包含订单，交易

* [redis](deploy_redis_cn.md)

主要用来提高系统的存取速度，或者存放非关键的数据

* [zookeeper](deploy_zookeeper_cn.md)

用来做系统的配置管理以及kafka的元数据存储
* [kafka](deploy_kafka_cn)

kafka实现服务间的异步通信，方便系统解耦和扩展

## 服务
* [接入CodeDeploy](codedeploy_cn.md)

目前relay相关组件是通过aws CodeDeploy+github进行部署，方便快速迭代

* [extractor](deploy_extractor_cn.md)

服务解析eth网络交易，通过kafka将解析结果同步到relay-cluster

* [relay-cluster](deploy_relay_cluster_cn.md)

是后台服务的核心组件，对外提供jsonRpc接口，是路印钱包和交易系统的接入服务，同时提供motan-rpc接口给miner

* [miner](deploy_miner_cn.md)

miner用来撮合交易

## web接入
目前通过aws ALB作为relay-cluster和ethnode的统一请求转发入口

* [alb](deploy_alb_cn.md)

## 辅助管理系统
* [kafka-manager](deploy_kafka_manager_cn.md)

是一个开源的kafka集群浏览和管理系统

* [node-zookeeper-browser](deploy_zk_browser_cn.md)

提供了一个web界面用来辅助查看和编辑kafka，方便对系统进行配制管理

* [motan-manager](deploy_motan_manager_cn.md)

提供了一个web界面用来辅助查看motan-rpc的启动状态，并能执行简单的配置操作

## 监控和告警
* [cloudwatch](cloud_watch_cn.md)

cloudwatch可以实现指标的上报，并配置相关规则进行告警

* [sns](sns_cn.md)

sns是aws的通知服务，可以通过接入SNS的API来进行直接的系统通知，通知方式包含短信，邮件。方便在系统的关键业务逻辑中插入该通知服务，方便运维人员及时发现故障点。
