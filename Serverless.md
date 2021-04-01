# Serverless

### 问题一：解决什么问题

1. 省钱、省力气
2. 对比：

| 方式       | 成本                                           |
| ---------- | ---------------------------------------------- |
| 传统mvc    | 虚拟机 数据库 redis缓存 负载均衡 cdn 容灾 备份 |
| Serverless | 云服务商Serverless服务                         |

### 问题二：为什么阿里 腾讯都在关注serverless

1. 降低中长尾应用运营成本

   中长尾：大部分时间没有流量或很少流量，最少独占一台

2. 提高研发效能

   SFF（Serverless For Frontend）前端负责数据接口编排

   微服务Baas化 后端更加关注领域设计

### 问题三：Serverless 对前端工程师来说会有什么机遇？为什么我们要学习 Serverless？

1. 如果说 Node.js 语言的出现解放了一波前端工程师的生产力，那 Node.js+Serverless 又可以进一步激发前端工程师的创造力。

### 一个场景

​		以导购类型的业务为例，开发这样一个业务通常需要前端开发工程师和后端开发工程师一起配合，但这两个开发岗位在该业务形态下并不能很好地发挥自己的全部价值。对于后端工程师来说，他在这个业务里要做的事情更多只是把现有的一些服务能力、数据组合在一起，变成一个新的数据提供给前端；而前端开发工程师负责把这个数据在页面上展示出来。这样的工作比较机械化，也没有太大的挑战，并不利于后端工程师的个人成长和岗位价值发挥；但在现有的研发模式下，由于缺乏前后端的连接点，前端工程师又不能去做这些比较简单的后端工作，业务上线也不可能给到前端工程师时间和机会去学习再实践。

Serverless 对前端和后端带来的影响总体都是正向的。对于前端来说，Serverless 不仅补足了前端工程师现有的能力，还可能使整个前端行业的定位发生变化。原来经常有人会认为前端的工作很简单，面向 UI 做好开发就行，剩下的工作可以交给后端。但是云端的应用开发模型出来之后，也就是前端和 Serverless 结合之后，大家对前端的诉求就不仅仅是开发一个页面了，而是要能交付整个应用的开发。

### 什么是Serverless

#### 狭义

- serverless computing架构
- FaaS架构
- Trigger事件驱动+FaaS函数即服务+BaaS后端即服务，持久化或第三方服务
- FaaS+BaaS

#### 广义

- 服务端免运维


- 具备Serverless特性的云服务


![image-20210401105555754](/Users/jxh/Library/Application Support/typora-user-images/image-20210401105555754.png)

### 传统流程

![image-20210401112956189](/Users/jxh/Library/Application Support/typora-user-images/image-20210401112956189.png)

### Serverless

![image-20210401113021385](/Users/jxh/Library/Application Support/typora-user-images/image-20210401113021385.png)



### 默认创建了 3 个服务。

1. 第一个"GreetingServiceGreetingFunctionhttpTrigger"函数触发器，函数触发器是所有请求的统一入口，当请求发生时，它会触发事件通知函数服务，并且等待函数服务执行返回后，将结果返回给等待的请求。

2. 第二个"GreetingService"函数服务，当函数触发器通知的“事件”到来，它会查看当前有没有闲置的函数实例，如果有则调用函数实例处理；如果没有，则会创建函数实例，等实例创建完毕后，再调用函数实例处理事件。

3. 第三个"GreetingServiceGreetingFunction"函数代码，“函数服务”在第一次实例化函数时，就会从这个代码仓库中拉取代码，并构建函数实例。

### 冷启动流程图

![image-20210401113219460](/Users/jxh/Library/Application Support/typora-user-images/image-20210401113219460.png)

### 两种进程模型

- 用完即毁型：函数实例准备好后，执行完函数就直接结束。这是 FaaS 最纯正的用法。

- 常驻进程型：函数实例准备好后，执行完函数不结束，而是返回继续等待下一次函数被调用。这里需要注意，即使 FaaS 是常驻进程型，如果一段时间没有事件触发，函数实例还是会被云服务商销毁。


### 扩缩容

![image-20210401114415893](/Users/jxh/Library/Application Support/typora-user-images/image-20210401114415893.png)

### Stateful VS Stateless

- 用完即毁型是天然的 Stateless

- 常驻进程型则是天然的 Stateful


### 后端应用BaaS化

![image-20210401114947667](/Users/jxh/Library/Application Support/typora-user-images/image-20210401114947667.png)

### 微服务的概念

​		微服务是一种开发软件的架构和组织方法，其中软件由通过明确定义的 API 进行通信的小型独立服务组成，这些服务由各个小型独立团队负责。微服务架构使应用程序更易于扩展和更快地开发，从而加速创新并缩短新功能的上市时间。

### 微服务的 10 要素

API、服务调用、服务发现；日志、链路追踪；容灾性、监控、扩缩容；发布管道；鉴权。

### 解耦数据库

关键的 Stateful 对象：消息队列。它是一个稳定的绝对值得信赖的 Stateful 节点，而且对你的应用来说消息队列是全局唯一的。解耦数据库的思路就是，通过消息队列解决数据库和副本之间的同步问题

我们每个微服务中的数据库，例如 MySQL，写的操作都会产生 binary log，通过额外的进程将 binary log 变更同步到消息队列，并监听消息队列将 binary log 更新在本地执行，修改 MySQL。比较著名的解决方案就是领英开源的 Kafka，现在云服务商基本也都会提供 Kafka 服务。当然数据库和副本之间会有短暂的同步延迟问题，但问题其实也不大，因为我们通常对数据库进行写操作时也会锁表。

### 分布式架构无法同时满足的 CAP 课题

拿我们自己的例子来说也很简单，我们用消息队列 RocketMQ，写操作我们都写入 RocketMQ，rule.js 进程中我们监听 RocketMQ，一旦有 rule 写入的消息，我们就更新我们的 lowdb 数据。围绕消息队列建立的同步机制，让每个微服务的数据库和它的扩容副本之间自动同步了。对于微服务来说，它本身的数据库还是 Stateful 的，但在微服务外部看来，这个微服务是 Stateless 的。

### 业务逻辑的拆与合

![image-20210401115749693](/Users/jxh/Library/Application Support/typora-user-images/image-20210401115749693.png)

#### 拆之，领域驱动设计

用一句话简单总结，DDD 就是一套方法论：通过对业务分层抽象，分析定义出领域模型，用领域模型驱动我们设计系统，最终将复杂的业务模型拆解为独立运维的领域模型。

减少核心节点，保护核心节点，降低网络深度等等。

#### 合之，Streaming

### Serverless 工作流

### 鉴权

- FaaS使用函数安全认证方式
- 后端互调的安全性，我们用 VPC 或 IP 白名单
- 前后端jwt

### 发布管道

发布管道的流水线主要有 3 个部分：

1. 代码发布前的验证，代码测试覆盖率 CI/CD；
2. 模拟流量回归测试通过，发布到灰度环境；
3. 代码正式上线，灰度环境替换正式环境。流水线的每个节点产生的结果，都会作为下一个节点必要的启始参数。

![image-20210401135850160](/Users/jxh/Library/Application Support/typora-user-images/image-20210401135850160.png)

### 函数初始化入口

​		每次启动 FaaS 实例时，系统都会先等待初始化函数执行，而且在函数初始化时间结束后，才会继续执行函数。而从目前平台的配置来看，初始化时至少也需要 1 秒的时间

### Container Serverless

```bash
# FROM是指我们的镜像基于哪个镜像来构建，我们这里是基于jessie linux的Node.js镜像
FROM registry.cn-shanghai.aliyuncs.com/jike-serverless/nodejs
# ENV是设置环境变量
ENV HOME /home/myhome/myapp
# RUN是执行一段命令
RUN mkdir -p /home/myhome/myapp/public
# COPY是将当前目录下的内容拷贝到容器中
COPY . /home/myhome/myapp
COPY public /home/myhome/myapp/public/
COPY node_modules /home/myhome/myapp/node_modules/
# WORKDIR是设置进入容器后的工作目录
WORKDIR /home/myhome/myapp/
# ENTRYPOINT是容器启动后执行的脚本
ENTRYPOINT [ "node","index.js" ]
```

### K8s

![image-20210401172452979](/Users/jxh/Library/Application Support/typora-user-images/image-20210401172452979.png)

#### Master 节点

1. 整个集群的中枢。
2. 是Stateful 的。
3. Master 节点由 API Server、etcd、kube-controller-manager 等几个核心成员组成，它只负责维持整个 K8s 集群的状态，为了保证职责单一，Master 节点不会运行我们的容器实例。

#### Worker 节点

1. 也就是 K8s Node 节点，我们部署的容器真正运行的地方，
2. 在 K8s 中，运行容器的最小单位是 Pod。
3. 一个 Pod 具备一个集群 IP 且端口共享，Pod 里可以运行一个或多个容器，
4. 最佳的做法还是一个 Pod 只运行一个容器。这是因为一个 Pod 里面运行多个容器，容器会竞争 Pod 的资源，也会影响 Pod 的启动速度；而一个 Pod 里只运行一个容器，可以方便我们快速定位问题，监控指标也比较明确。

#### 使用 YAML 文件来控制我们的应用部署

### Serverless 底座：Service Mesh

### Serverless 底座搭建：K8s 组件 Istio

### Serverless 完整实现：K8s 组件 Knative

### FaaS 痛点

FaaS 最擅长是事件响应

1. 首先，调试困难，我们每次在本地修改和线上运行的函数都无法保持一致，而且线上的报错也很奇怪，常见的错误都是超时、内存不足等等，让我们根本无法用这些信息去定位问题。
2. 其次，部署麻烦，每次改动都要压缩上传代码包，而且因为每个云服务商的函数 Runtime 不一致，导致我们也很难兼容多云服务商。
3. 最后，很多问题客服无法跟进，需要自己到处打听。

### Serverless 应用框架

#### Serverless Framework

​		Serverless Framework 官方本身主要解决跨云服务商 Serverless 应用的部署运维问题。它利用类似 K8s 组件 Component 的概念，让开发者可以自己开发并且共享组件。通过组件来支持多语言的模板，而且 Serverless 组件的扩展能力还让 Serverless 框架具备生态优势。

#### Malagu

​		alagu 是基于 TypeScript 的 Serverless First、可扩展和组件化的应用框架。为了提升 Serverless 的开发体验，只专注 Node.js Serverless 应用。

#### Midway FaaS

​		跨云厂商，代码复用，传统迁移

#### 阿里云开发平台