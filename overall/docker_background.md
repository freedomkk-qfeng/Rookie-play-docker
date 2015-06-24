# docker背景
Docker 是 PasS 提供商 DoctCloud 开源的一个基于 LXC 的高级容器引擎，源代码托管在 Github 上，基于 go 语言并遵从 Apache2.0 协议开源。Docker 近期非常火热，无论是从 Github 上的代码活跃度，还是 Redhat 在 REHEL6.5 中集成对 Docker 的支持，就连 Google 的 Compute Engine 也支持 docker 在意之上运行，百度、阿里、新浪、京东也开始使用 Docker 作为 PaaS 基础。
![](https://github.com/freedomkk-qfeng/Rookie-play-docker/raw/master/images/overall_docker_backgroud_1.png)
某款开源软件能否在商业上成功，很大程度上依赖三件事 —— 成功的 User case，活跃的社区和一个好故事。DotCloud 在自家的 PaaS 产品上建立在 docker 之上，长期维护且有大量用户，社区十分活跃，接下来我们看看 docker 的故事。

- 环境管理复杂 -- 从各种 OS 到各种中间件到各种 app，一款产品能够成功作为开发者需要关心的东西太多，且难于管理，这个问题几乎在所有现代 IT 相关行业都需要面对，对此 Docker 可以简化部署多种应用实例工作，比如 Web 应用、后台应用、数据库应用、大数据应用比如 Hadoop 集群、消息队列等等都可以打包成一个 Image 部署。如图所示：
![](https://github.com/freedomkk-qfeng/Rookie-play-docker/raw/master/images/overall_docker_backgroud_2.png)
- 云计算时代的到来 -- AWS 的成功，引导开发者将应用转移到 cloud 上，解决了硬件管理的问题，然而中间件相关的问题依然存在， Docker 的出现正好能帮助软件开发者开阔思路，尝试新的软件管理方法来解决这个问题
- 虚拟化手段的变化 -- cloud 时代采用标配硬件来降低成本，采用虚拟化段来满足用户按需使用的需求以及保证可用性和隔离性，然而无论是 kvm 还是 Xen 在 docker 看来，都是在浪费资源，因为用户需要的是高效运行环境而非 OS，GuestOS 既浪费资源又难于管理，更加轻量级的 LXC 更加灵活和快速
- LXC 的移植性 -- LXC 在 linux2.6 的 kernel 里就已经存在了，但是其设计之初并非为云计算考虑的，缺少标准化的描述手段和容器的可迁移性，决定其构建出的环境难于迁移和标准化管理（相对于 kvm 之类的image 和 snapshot），Docker 就在这个问题上作出实质性的革新。
![](https://github.com/freedomkk-qfeng/Rookie-play-docker/raw/master/images/overall_docker_backgroud_3.png)
面对上面的问题，docker 设想是交付运行环境如同海运，OS 如同一个货轮，每一个在 OS 基础上的软件都如同一个集装箱，用户可以通过标准化手段自由组装运行环境，同时集装箱的内容可以由用户自定义，也可以由专业人员制造。这样，交付一个软件，就是一系列标准化组件的集合的交付，如同乐高积木，用户只需要选择合适的积木组合，并且在最顶端署上自己的名字（最后标准化组件是用户的app），这也就是基于 docker 的 PaaS 产品的原型。

docker 官网上提到了 docker 的典型应用场景

- Automating the packaging and deployment of applications
- Creation of lightweight, private PAAS environments
- Automated testing and continuous integration/deployment
- Deploying and scaling web apps, databases and backend services

由于 docker 基于 LXC 轻量级的虚拟化特点（0.9之后不是基于 LXC，但还是支持的），docker 相比 KVM 之类最明显的特点就是启动快，资源占用小。因此对于构建隔离性的标准化的运行环境，轻量级的 PaaS（如dokku），构建自动化测试和持续集成环境，以及一切可以横向扩展的应用（尤其是需要快速启停来应对峰谷的 web 应用）。

1. 构建标准化的运行环境，现有方案大多是在一个base OS上运行的一套puppet/chef，或者一个image文件，期缺点是前者需要base OS许多前提条件，后者几乎不可以修改（因为copy on write的文件格式在运行时rootfs是read only），并且后者文件体积大，环境管理和版本控制本身也是一个问题。
1. PaaS环境是不言而喻的，其设计之初和DotCloud的案例都是将其作为PaaS产品的环境基础
1. 因为其标准化构建方法（buildfile）和良好的REST API，自动化测试和持续集成/部署能够很好的集成进来
1. 由于LXC轻量级的特点，其启动快，而且docker能够只加载每个container变化的部分，这样的资源占用小，能够在单机环境下与KVM之类的虚拟化方案相比能够更加快速和占用更少资源
