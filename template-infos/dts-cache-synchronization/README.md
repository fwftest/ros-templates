

**一键部署**
--------

80

https://www.aliyun.com/solution/tech-solution/rtsorarctebcc

### **方案概览**

在许多业务场景中（比如商品信息查询、库存管理、账户余额查询、交易记录查询、用户信息查询等），为了提高数据查询速度和降低数据库压力，我们通常会使用Redis作为缓存层。然而，保持MySQL与Redis缓存之间的数据一致性是一个关键挑战。本方案将探讨基于Cache-Aside Pattern模式下的一种高效的MySQL与Redis缓存同步一致性方案，以确保业务数据的实时性和准确性，提高数据查询速度，降低数据库压力。

**说明** 

Cache-Aside Pattern的优点是可以有效减轻数据库的压力，提高应用程序的性能和可扩展性。缺点是若缓存中的数据失效或未更新，可能导致应用程序读取到旧数据。此外，Cache-Aside Pattern不适用于数据更新频繁的应用程序，因为缓存不断失效和更新会导致缓存中的数据不稳定。

### **方案架构**

方案提供的默认设置（如地域、VPC、安全组、vSwitch、实例名称等）完成部署后在阿里云上搭建的RDS MySQL实时数据同步到云数据库 Redis进行加速分析的架构图如下图所示。实际部署时您可以根据资源规划修改部分设置，但最终形成的运行环境与下图相似。

![42ee575d2247542d249498e4acdd3bb6.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6699353961/p711327.png)

本方案的技术架构包括以下基础设施和云服务：

* 地域和可用区：云数据库 RDS MySQL 实例、云数据库 Redis 实例以及数据传输服务 DTS 实例必须在同一个地域中，但可以选择部署在不同的可用区。
* 1个专有网络VPC：云数据库 RDS MySQL 实例、云数据库 Redis 实例以及数据传输服务 DTS 实例必须在同一个 VPC 网络环境中。
* 1个云数据库RDS MySQL版实例：为线上订单/票务等服务提供数据持久化服务和事务一致性服务。
* 1个云数据库Redis版实例：为线上订单/票务等服务提供缓存加速、库存查询以及秒杀限流等服务。
* 1个数据传输服务DTS实例：用于订阅云数据库RDS MySQL中相关的数据库表对象的BINLOG日志数据。
* 1台云服务器ECS实例：用于部署DTS订阅应用程序，用于接收数据传输服务DTS订阅云数据库RDS实例的更新事件，并且通过应用程序对云数据库Redis进行更新。
### **部署准备**

10

开始部署前，请按以下指引完成账号申请、账号充值。

### **准备账号**

1. 如果您还没有阿里云账号，请访问[阿里云账号注册页面](https://account.aliyun.com/register/qr_register.htm)，根据页面提示完成注册。阿里云账号是您使用云资源的付费实体，因此是部署方案的必要前提。
2. [为阿里云账号充值](https://help.aliyun.com/document_detail/324650.html)。
   
   1. 为节省成本，本方案默认选择使用按量付费资源，使用按量付费资源需要确保账户余额不小于100元。
   2. 完成本方案的部署及体验，预计产生费用不超过20元（假设您选择下表中的相关规格资源，且运行时间不超过2小时，如果调整了资源规格，请以控制台显示的实际报价以及最终账单为准）。
      
      | **云服务** | **规格配置** | **地域** | **预估费用参考** |
      | --- | --- | --- | --- |
      | 云服务器 ECS | 规格：ecs.g5.xlarge，4 vCPU 16 GB  存储空间：40 GB（ESSD PL1云盘）  带宽：100 Mbps（按使用流量） | 华东2（上海） | 配置费用：1.854 元/小时  公网流量费用：0.800 元/GB |
      | 云数据库 RDS MySQL 版 | 规格：mysql.x4.medium.2c  存储空间：100 GB | 华东2（上海） | 1.8 元/小时 |
      | 云数据库 Redis | 规格：标准版 高可用 16GB主从版 | 华东2（上海） | 3.00 元/小时 |
      | 数据传输服务 DTS | 数据订阅 | 华东2（上海） | 3.00 元/小时 |
      | 按量费用：9.654 元/小时  公网流量费用：0.800 元/GB | | | |
      
      **说明** 
      
      在方案部署过程中，由于涉及到资源操作权限，为了简化操作流程，建议使用阿里云主账号进行体验。
### **一键部署**

40

资源编排（ROS）可以让您通过YAML或JSON文件清晰简洁地描述所需的云资源及其依赖关系，然后自动化地创建和配置这些资源。您可以通过下方提供的ROS一键部署链接，来自动化地完成这些资源的创建和配置。

本文介绍的ROS模板主要完成了以下内容：

* 部署1个专有网络VPC。
* 部署1台交换机。
* 部署1台云服务器 ECS。
* 部署1个云数据库RDS MySQL。
* 部署1个云数据库Redis。
* 配置1个数据传输服务DTS订阅任务。
* 已在ECS中部署DTS订阅程序。
  
  **说明** 
  
  为了避免订阅程序单点故障引起的数据同步延迟，您可以在不同可用区下的两台ECS云服务器上部署订阅程序形成主备容灾。更多信息，请参见：[数据订阅SDK容灾](https://help.aliyun.com/zh/dts/use-cases/disaster-tolerance-of-data-subscription-sdk)。

1. 打开[一键配置模板链接](https://ros.console.aliyun.com/region/stacks/create?templateUrl=https://ros-public-templates.oss-cn-hangzhou.aliyuncs.com/service_template/technical-solution/dts-cache-synchronization.yml&pageTitle=实时同步RDS与Redis构建缓存一致性&isSimplified=True&productNavBar=disabled&disableRollback=false&disableNavigation=true&hideStepRow=true)前往ROS控制台，系统自动打开使用新资源创建资源栈的面板。
   
   **说明** 
   
   ROS控制台默认处于您上一次访问控制台时的地域，请根据您创建的资源所在地域修改地域后再执行下一步。
2. 确认好地域后（本教程以**华东2（上海）**地域为例），在**配置参数模板**步骤中配置**资源栈名称**、**ECS**和**Database**。
   
   | **配置项** | **参数** | **说明** | **示例值** |
   | --- | --- | --- | --- |
   | **资源栈名称** | 资源栈名称 | ROS一键部署任务名称，可自定义。 | rds2redis |
   | **ECS** | 交换区可用区 | ECS实例所在的可用区。 | 可用区G |
   | 实例类型 | ECS实例的架构、分类和规格配置。 | X86计算通用型：ecs.g6.large |
   | 系统盘类型 | ECS实例的硬盘类型。 | ESSD云盘 |
   | 实例密码 | ECS实例的密码。 | |
   | **RDS** | 实例规格 | 云数据库RDS实例的规格。 | mysql.x4.medium.2c |
   | 数据库名 | 创建的数据库名称。 | |
   | RDS数据库账号 | RDS实例的数据库账号和密码。 | |
   | RDS数据库密码 |
   | **Redis** | 实例规格 | 云数据库Redis的规格。 | Redis 16G集群（2节点）：  redis.logic.sharding.8g.2db.0rodb.4proxy.default |
   | 实例密码 | Redis实例的密码。 | |
   | **DTS** | 消费组账号 | DTS订阅消费组的账号和密码。 | |
   | 消费组账号的密码 |
3. 单击**下一步**，跳转至资源预览页，单击**创建**，系统将自动创建并部署本教程所需的资源。

**说明** 

根据提示开通缺失的角色权限

![Snipaste_2024-09-20_10-26-18](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2901186271/p851500.png)

4. 当**资源栈信息**页面的**状态**显示为**创建成功**时表示一键配置完成。
   
   **说明** 
   
   创建时间较久，耗时约30分钟，请耐心等待。
   
   ![image.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7516813961/p711427.png)
5. 单击资源页签，找到已创建的RDS实例，单击实例ID，进入RDS实例详情页，获取RDS的公网连接地址。
6. 登录RDS实例，向源端 RDS MySQL 上写入、更新、删除相关数据，以模拟RDS MySQL上面的数据变更。
### **数据同步结果验证**

10

#### **1. 更新RDSMySQL数据**

上一步中您已经在数据传输服务DTS管理控制台上配置了DTS延迟告警、检查了DTS同步延迟等情况，接下来可以向源端 RDS MySQL 上写入、更新、删除相关数据，以模拟RDS MySQL上面的数据变更，比如可以模拟TPCC压测程序等或者以业务系统真实的更新数据，以观察DTS订阅情况等。

通过DTS运行程序的标准输出日志情况，可以观察到DTS订阅任务在订阅源端数据变更的情况，如图所示：

![image.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6927299861/p695971.png)

#### **2. 检查Redis最新数据**

上一步中您已经部署好了数据传输服务DTS数据订阅程序，并且对源端RDS MySQL有数据更新行为发生，即RDS MySQL实例中已经有BINLOG日志的更新，通过上述步骤可以观察到DTS订阅程序可以订阅到BINLOG的最新事件，接下来检查Redis实例中是否已经有最新的数据写入和更新。

通过Redis客户端等工具连接到Redis，检查Redis实例中最新的数据，并且针对其中的key进行验证，如图所示：

![image.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6927299861/p695980.png)

同时，也可以通过如MySQL客户端等工具连接到RDS MySQL实例，检查RDS MySQL实例中对应的最新数据，如图所示：

![image.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6927299861/p695985.png)

**说明** 

RDS MySQL数据库中的数据与Redis中的数据并不是强一致的，可能会存在一定的延迟。

#### **3. 检查DTS订阅延迟**

上一步中您已经部署好了数据传输服务DTS数据订阅程序，并且对源端RDS MySQL有数据更新行为发生，接下来您需要对DTS订阅和数据同步的效果进行验证。针对生产环境，建议您在数据传输服务DTS控制台配置好对DTS订阅程序的**监控报警**等。

1. 登录[数据传输服务DTS管理控制台](https://dtsnew.console.aliyun.com/sbe)。
2. 在顶部菜单栏，选择地域（本文以**华东2（上海）**地域为例）。
3. 在左侧导航栏，选择**数据订阅**。
4. 在**订阅任务**页面，定位到上述步骤中创建的订阅任务，在**操作**列上点击**任务详情**。
5. 进入任务详情页面后，在左侧导航栏选择**数据消费**，点击**任务管理**。
6. 查看**增量数据采集**等页面，检查DTS订阅延迟情况等，如图所示：
   
   ![image.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6927299861/p695952.png)
### **完成及清理**

20

#### **方案验证**

完成了数据同步结果验证等步骤后，您可以通过配置和观察DTS**监控报警**、**性能监控**等来观察数据同步的可用性、延迟等，通过观察RDS MySQL实例和Redis实例中相关的记录来验证同步的正确性和一致性（注意考虑延迟带来的不一致情况）。

#### **清理资源**

在本方案中，您创建了1个专有网络VPC、1台交换机、1个云数据库 RDS MySQL 实例、1个云数据库 Redis 实例、1个数据传输服务DTS实例以及1个云服务器ECS实例。测试完方案后，您可以在[ROS控制台](https://ros.console.aliyun.com/overview)找到目标资源栈，然后直接删除资源栈即可（删除时，**删除方式**选择为**释放资源**）。
