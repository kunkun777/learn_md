# Dubbo

---

## 一、分布式系统

### 1.1 分布式

什么是分布式？来自维基百科--分布式系统（*distributed system*）是建立在网络之上的[软件](https://baike.baidu.com/item/软件)系统。正是因为[软件](https://baike.baidu.com/item/软件/12053)的特性，所以分布式系统具有高度的[内聚性](https://baike.baidu.com/item/内聚性/4973441)和透明性。因此，网络和分布式系统之间的区别更多的在于高层[软件](https://baike.baidu.com/item/软件/12053)（特别是[操作系统](https://baike.baidu.com/item/操作系统/192)），而不是硬件。看了这个解释我是一头雾水。到底什么是分布式，分布式用来做什么，我们先来看些与分布式有关的东西。

### 1.2 分布式拆分

- **水平拆分**

  就是不断地增加服务器部署同一个应用，但这样治标不治本。所以产生了垂直拆分

- **垂直拆分**

  就是不断地拆分服务将不同的微服务交付给不同的服务器上单独运行。弊端是由于应用不可能完全脱离互相的其他服务，所以我们将项目的前后端分离，并将不同的业务代码的处理放到不同的服务器。

  ![image-20200409222738738](D:\App\Typora\resources\md-image\dubbo\image-20200409222738738.png)

  这样的话，由于我们将不同的业务逻辑分开，就会导致web服务器和逻辑处理的服务器分开难以管理。这时，就需要我们的RPC来解决找个问题。那什么是RPC呢？

  这里先简单介绍一下，***RPC***又称远程过程调用，是一种分布式服务框架，专门管理web端和service服务端的通信的。

  当业务越来越多的时候，不同业务之间的访问量参差不齐，有的“门客满盈”，有成千上百次的访问，而有的却寥寥几次。所以为了解决这个问题，我们还需要引进***流动计算架构***来调度，进行服务器集群的管理，提高服务器的利用率。

### 1.3 dubbo简介

`dubbo`使得应用可通过高性能的 `RPC `实现服务的输出和输入功能，可以和 [Spring](https://baike.baidu.com/item/Spring)框架无缝集成。

#### 1.3.1 **特性** 

是一款高性能、轻量级的开源`Java RPC`框架，它主要提供了三大核心能力：

- 面向接口的远程方法调用，

- 智能容错和负载均衡，

- 服务自动注册和发现。

  那么这些都是什么呢？我们可以在[apache官网](dubbo.apache.org/en-us/)里找到dubbo的一些**特性**：

  ![image-20200409225043778](D:\App\Typora\resources\md-image\dubbo\image-20200409225043778.png)

  - 面向接口代理的高性能RPC调用

    也就是当服务器A要调用服务器B中的服务的时候，我们只需要在A中调用某一个接口就可以使用到B中功能，而不用考虑其中是如何调用的。就类似可以理解为mybatis中可以用调用dao层接口而不用考虑具体实现一样。

  - 智能负载均衡

    当我们对同一个业务有多个服务器被垂直拆分了之后，可以对这几台服务器实现负载均衡。可以类比成nginx中的负载均衡实现。

  - 服务自动注册发现

    当服务A需要服务B的时候，可以自动帮我们找到该服务所在的服务器

    ![image-20200409230156814](D:\App\Typora\resources\md-image\dubbo\image-20200409230156814.png)

    这个的具体是由注册中心实现的，注册中心维护一个功能--服务器对应的清单，负载均衡也是在这里实现，所以这个就相当于是Nginx服务器中的那个被最先访问的服务器。

  - 运行期流量调度

    这个主要是手动配置路由规则，然后实现灰度发布（就是当我们服务需要升级时，可以让流量先访问升级后的服务器，再把旧版本升级后再投入使用）

  - 可视化的服务治理与运维

    有可视化的管理界面

#### 1.3.2 高性能RPC框架

上面说了那么多，可以得出一个结论，Dubbo最根本还是逃不了RPC框架。那么先来了解一下RPC的大致组成框架：消费者--注册中心--服务提供者----监控者

![image-20200409231534530](D:\App\Typora\resources\md-image\dubbo\image-20200409231534530.png)

我们先简单了解一下，Dubbo具体是怎么调用的。首先，注册中心注册**服务提供者**；并且让**消费者**订阅；在**监控者**中注入消费者和服务者的信息。这些是在初始化的时候我们需要做完的。然后在运行时，只需要做唯一一件事，就是消费者向注册中心发送信息请求，并且服务提供者提供相应的服务给注册中心转交给消费者。

---

## 二、RPC框架的具体实现

到这儿，我们已经知道了RPC框架的是由 ***消费者--注册中心--服务提供者---监控者*---元数据空间** 来实现的。所以我们分别来具体实现这五部分。

### 2.1 注册中心

实际上，Dubbo支持多种注册中心的，但是在参考手册的介绍中明确标明，真的很明确，因为就只有一行字——**“推荐使用 [Zookeeper 注册中心](http://dubbo.apache.org/zh-cn/docs/user/references/registry/zookeeper.html)”**。所以，我们还是听从它的官方文档来做。具体的在`Zookeeper`官方手册可以查看。

![image-20200409232212399](D:\App\Typora\resources\md-image\dubbo\image-20200409232212399.png)

### 2.2 监控中心

便于管理各个服务器，需要一个**管理控制台**来管理监控器，这也相应了它的可视化界面。

- 安装

  在[GitHub](https://github.com/apache/dubbo-admin)网站上去查找相应的源码下载

- 启动

  打成jar包，启动
  
  ![image-20200423205047047](D:\App\Typora\resources\md-image\dubbo\image-20200423205047047.png)
  
- 访问管理控制台

  ![image-20200423205130745](D:\App\Typora\resources\md-image\dubbo\image-20200423205130745.png)

  密码和用户名都默认为**root** 

### 2.3  具体实现（Spring为例）

- 注册服务到服务中心

  1. 在服务端引入dubbo的依赖

     ```Java
     <!-- https://mvnrepository.com/artifact/com.alibaba/dubbo -->
     <dependency>
         <groupId>com.alibaba</groupId>
         <artifactId>dubbo</artifactId>
         <version>2.6.6</version>
     </dependency>
     ```

  2. 引入注册中心操作客户端

     ```Java
     <dependency>
         <groupId>org.apache.curator</groupId>
         <artifactId>curator-framework</artifactId>
         <version>2.12.0</version>
     </dependency>
     ```

  3. 配置服务方的基本配置

     ```Java
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
            xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
     
         <!-- 提供方应用信息(主要是名字)，用于计算依赖关系 -->
         <dubbo:application name="hello-world-app"  />
     
         <!-- 使用zookeeper广播注册中心暴露服务地址 -->
         <!--<dubbo:registry address="zookeeper://127.0.0.1:2181" />-->
         <dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"/>
     
         <!-- 指定通信协议，用dubbo协议在20880端口暴露服务 -->
         <dubbo:protocol name="dubbo" port="20880" />
     
         <!-- 声明需要暴露的服务接口,ref填写服务的真正实现-->
         <dubbo:service interface="com.zxc.test.service.UserService" ref="demoService" />
     
         <!-- 将服务的真正实现类加入到bean管理中 -->
         <bean id="demoService" class="com.zxc.service.UserServiceImpl" />
     </beans>
     ```

  4. 启动类

     ```Java
     public class MainApplication {
         public static void main(String[] args) throws IOException {
             ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"provider.xml"});
             context.start();
             System.in.read();
     //        DemoService demoService = (DemoService)context.getBean("demoService"); // 获取远程服务代理
     //        String hello = demoService.sayHello("world"); // 执行远程方法
     //        System.out.println( hello ); // 显示调用结果
         }
     
     }
     ```

- 消费者端

  1. 引入Dubbo和zookeeper依赖（同上）

  2. 配置文件，声明自己需要的接口

     ```Java
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:dubbo="http://dubbo.apache.org/schema/dubbo" xmlns:context="http://www.springframework.org/schema/context"
            xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
     
         <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
         <dubbo:application name="consumer-of-helloworld-app"  />
     
         <!-- 使用multicast广播注册中心暴露发现服务地址 -->
         <dubbo:registry address="zookeeper://127.0.0.1:2181" />
     
         <!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
         <dubbo:reference id="UserService" interface="com.zxc.test.service.UserService" />
         <context:component-scan base-package="com.zxc.test.service.impl"/>
     </beans>
     ```

- 监控中心

  1. 在上面的`github`中下载，并打包运行（在`conf`目录下填写正确的`zookeeper`地址），

  ![image-20200412160622733](D:\App\Typora\resources\md-image\dubbo\image-20200412160622733.png)

  2. 将消费者和提供者两端注册到相应的监控中心

     这里只以消费者为例：

     ```Java
     <!--    <dubbo:monitor protocol="registry"/>-->//这是在注册中心自动发现
         <dubbo:monitor address="127.0.0.1:7070"/>//这是指定了注册中心
     ```

     - - - - - - - - - - - - - - - - - - - - - - - - - - -

其实到这步，最基本的`RPC`框架就已经搭建好了，消费者一端也可以成功的调用到提供者一端的接口。但这以上都是使用的`Spring`框架，那么我们该如何整合`Dubbo`和`SpringBoot`框架呢？接下去就列出使用中的几项不同点-：

1. 在消费者和使用者中导入`Dubbo`依赖不同（在[git](https://github.com/apache/dubbo-spring-boot-project)中就有明确指出）

2. 然后配置（主要是注册到注册中心，和声明，）可以用yml，properties

   

   方法1.

3. `service`使用`dubbo`的**`@service`**来暴露

4. 然后在主类上加**`@EnableDubbo`**开启`Dubbo `的注解支持

5. 当我们需要调用远程接口的时候，需要注入的时候，需要加上**`@Reference`**注解

   

   方法2.（这个方法可以使注解到方法级别的）

6. 保留`dubbo.xm`l配置文件

7. 只需要`@ImportResource(location="XXX.XXX.XXX.calss")`导入配置文件

    

   方法3.（使用API注解的方式，将每一个组件手动地注册到Bean中，然后远程调用的时候只需要像本地方法一样使用）

8. 可以设置一个配置类，然后将不同的类注册进IOC容器（就是相当于是）

   ```java
   // 当前应用配置
   ApplicationConfig application = new ApplicationConfig();
   application.setName("yyy");
    
   // 连接注册中心配置
   RegistryConfig registry = new RegistryConfig();
   registry.setAddress("10.20.130.230:9090");
   registry.setUsername("aaa");
   registry.setPassword("bbb");
    
   // 注意：ReferenceConfig为重对象，内部封装了与注册中心的连接，以及与服务提供方的连接
    
   // 引用远程服务
   ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
   reference.setApplication(application);
   reference.setRegistry(registry); // 多个注册中心可以用setRegistries()
   reference.setInterface(XxxService.class);
   reference.setVersion("1.0.0");
    
   // 和本地bean一样使用xxxService
   XxxService xxxService = reference.get(); // 注意：此代理对象内部封装了所有通讯细节，对象较重，请缓存复用
   ```

9. tips:

   1. 配置文件优先级：

      方法级优先；接口次之；全局配置再次之；

      （这能使我们方便修改其中一个服务端的配置而不影响其他的端口

      消费方优先；服务端次之；
      
   2. 灰度发布，这是为了解决每个服务方的版本不一致，以及正常版本的更迭来解决的，也就是给每个服务端和消费端设定一个属于/要调用的版本

10. 本地存根：

    我们在消费者本地创建一个本地的代码实现，方便先在本地实现一些逻辑

    **具体实现**：

    ​	1> 在本地继承需要调用的服务端接口

    ​	2> 实现一个有参构造器，传入一个远程的代理对象

    ​	3> 在配置文件中，远程调用的对象中的**stub**属性来确定需要的存根类

    ![image-20200423223242375](D:\App\Typora\resources\md-image\dubbo\image-20200423223242375.png)

    

- - - - - - - - - - - - - - - - - - - - - - - - - - - -



## 三、高可用

什么是高可用？总的来说，高可用就是通过一些设计，减少不能提供服务的时间。 

### 3.1 健壮性：

- Zookeeper宕机
1. 注册中心有主备从机
1. 如果注册中心的机器全部宕机则我们仍可以在一段时间内继续消费服务，因为有本地缓存，所以消费者就不需要去访问注册中心寻找所需要的服务提供者

2. 即使我们缓存到期或者没有缓存，我们也可以通过**dubbo直连**（具体实现是通过在**@Reference(url = 服务端的主机号：端口号)**）来直接绕过注册中心连接服务端

### 3.2 负载均衡(默认采用随机)

- **负载均衡机制** 

  1. 权重随机

     即为每一个服务器分配一个权重，按照所占比例大小来将其进行分配使用

  2. 基于权重的轮询

     在依次调用的基础上考虑权重的机制，总的百分比和权重接近

  3. fair机制

     挑一个最快的服务器来响应，谁能力大，就责任多

  4. hash

     根据hash值去到相应的服务器

- **设置机制** 

  1. 通过**@Reference**注解设置**LoadBalance**属性
  2. 设置权重的话，可以在**@Service**(weight="")中设置

### 3.3 服务降级

什么是服务降级？当服务器压力过大的情况下，让一些情况不严重的服务，让资源给被频繁访问的服务。这部分是针对消费者一端的。

- **降级机制方法**
  1. 禁用：对该服务的调用返回都为空值
  2. 容错：在对服务调用失败后，不抛出异常，返回为空

### 3.4 集群容错

> 集群是对消费者做了一层封装，封装成被调用者（Invoker），然后Invoker使用可调用的真正执行者们（Directory），Router是根据路由规则选出可以被调用的执行者，然后`LoadBalance`根据配置负载均衡算法/调用失败算法选出唯一一个真正的执行者

![image-20210406213633919](D:\App\Typora\resources\md-image\dubbo\image-20210406213633919.png)

**集群调用失败的机制**

1. Failover Cluster

   失败自动切换，出现失败，重试其他服务器，通常用于读操作，这个是缺省配置

   ```java
    retries="2" 
   ```

2. Failfast Cluster

   快速失败，只发起一次调用，失败立即报错

3. Failsafe Cluster

   失败安全，出现异常时，直接忽略

4. Failback Cluster

   失败自动恢复，后台记录失败记录

5. Forking Cluster

   并行调用多个服务器，只要一个成功即返回，forks="2"，可设置最多并行数，用于实时性要求较高的读操作

6. Broadcast Cluster

   广播调用所有提供者，逐个调用，任意一台报错即报错，用于通知/保持一致性,需要设置相应的广播比例
   
   ```Java
   @Reference(version = "1.0.0",url = "dubbo://localhost:12346",parameters = {"broadcast.fail.percent", "20"})
   ```

**集群模式配置** 

```<dubbo:server cluster="failsafe"/>或<dubbo:reference cluster="failsafe"/>``` 

### 3.5 整合hystrix

hystrix是什么？在分布式环境中，许多服务依赖项中的一些必然会失败。Hystrix是一个库，通过添加延迟容忍和容错逻辑，帮助你控制这些分布式服务之间的交互。

- 具体实现

  1. 导入依赖

     ```Java
     <dependency>            <groupId>org.springframework.cloud</groupId>
                 <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
     </dependency>
     ```
     
2. 开启服务容错功能
  
   **@EnableHystrix**
  
3. 在可能出现异常的地方，加上**@HystrixCommand**，并且它的`fallback`属性用于在出错的时候进行调用它指定的方法

## 四、dubbo特性

### 1. 启动时检查

​	`dubbo.reference.check=false`，强制改变所有 reference 的 check 值，就算配置中有声明，也会被覆盖。

​	`dubbo.consumer.check=false`，是设置 check 的缺省值，如果配置中有显式的声明，如：`<dubbo:reference check="true"/>`，不会受影响。

​	`dubbo.registry.check=false`，前面两个都是指订阅成功，但提供者列表是否为空是否报错，如果注册订阅失败时，也允许启动，需使用此选项，将在后台定时重试

### 2. 只订阅

> 当这个可以让服务提供者开发方，只订阅服务(开发的服务可能依赖其它服务)，而不注册正在开发的服务，通过直连测试正在开发的服务。

### 3. 多协议

> Dubbo默认使用的协议如下：
>
> dubbo://（推荐）、rmi://、hessian://、http://、webservice://、thrift://、memcached://、redis://、rest://

- 通讯协议之间的差别比较：https://www.jianshu.com/p/eb4960467e00

### 4. 静态服务

> 服务提供者初次注册时为禁用状态，需人工启用。断线时，将不会被自动删除，需人工禁用。

## 五、dubbo原理

> 实际上`Dubbo`2.7之前是将全部的外部化配置和元数据都存放在注册中心之中，但是这样会导致注册中心压力过大，然后就额外将这2个功能模块划分出来，

### 4.1 注册中心

> 官方文档的解释：基于注册中心下发地址的集群方式，以及对各种注册中心的抽象。因为注册中心有多种，而这个模块可以理解为是对那许多注册中心的抽象模板。

### 4.2 元数据空间



### 4.3 配置中心

> 这个配置中心是用于配置相应的服务方和应用方的相关连接和相关的属性设置，主要功能有2个：**外部化配置**和**服务治理**.

- 外部化配置

  这个可以简单理解为，把`application.propeties`外部化存储，方便进行统一化的管理，其实这里的配置还可以是元数据中心和注册中心等等的相关配置

- 服务治理

  这个其实就是管理各个服务之间的调用，以及帮助消费方

具体的详细配置如下：

| `dubbo:service`     | 服务配置     | 用于暴露一个服务，定义服务的元信息，一个服务可以用多个协议暴露，一个服务也可以注册到多个注册中心 |
| ------------------- | ------------ | ------------------------------------------------------------ |
| `dubbo:reference`   | 引用配置     | 用于创建一个远程服务代理，一个引用可以指向多个注册中心       |
| `dubbo:protocol`    | 协议配置     | 用于配置提供服务的协议信息，协议由提供方指定，消费方被动接受 |
| `dubbo:application` | 应用配置     | 用于配置当前应用信息，不管该应用是提供者还是消费者           |
| `dubbo:module`      | 模块配置     | 用于配置当前模块信息，可选                                   |
| `dubbo:registry`    | 注册中心配置 | 用于配置连接注册中心相关信息                                 |
| `dubbo:monitor`     | 监控中心配置 | 用于配置连接监控中心相关信息，可选                           |
| `dubbo:provider`    | 提供方配置   | 当 ProtocolConfig 和 ServiceConfig 某属性没有配置时，采用此缺省值，可选 |
| `dubbo:consumer`    | 消费方配置   | 当 ReferenceConfig 某属性没有配置时，采用此缺省值，可选      |
| `dubbo:method`      | 方法配置     | 用于 ServiceConfig 和 ReferenceConfig 指定方法级的配置信息   |
| `dubbo:argument`    | 参数配置     | 用于指定方法参数配置                                         |



**tips:** 

1. 引用缺省是延迟初始化的，只有引用被注入到其它 Bean，或被 `getBean()` 获取，才会初始化。如果需要饥饿加载，即没有人引用也立即生成动态代理，可以配置：`<dubbo:reference ... init="true"`
2. 外部化配置默认较本地配置有更高的优先级，因此这里配置的内容会覆盖本地配置值















- Dubbo
- 前后端分离
- SpringBoot
- Shiro
- RBAC
- 网关、NAT、Spring Clouds Gateway
- Zookeeper
- Redis\Mysql
- Swagger
- jpa