# Tomcat实现原理

## 一、总述

`Tomcat`是一个`Servlet`的容器，它是用于将每个`servlet`存放着，并接受发来的请求，根据请求的路径决定它交给哪个`servlet`来进行处理。

![image-20200404154454204](D:\App\Typora\resources\md-image\image-20200404154454204.png)

源码的部署以及运行事项详见：https://www.cnblogs.com/grasp/p/10061577.html

## 二、架构

### 2.1 HTTP的工作原理

![image-20200404211216506](D:\App\Typora\resources\md-image\image-20200404211216506.png)

### 2.2 具体架构

1. 为什么需要`servlet`

   为了`Http`请求服务器发送请求过来，由**servlet容器**（这里其实就是指Tomcat其中一部分，另外一部分是指请求的接受与反馈的部分）转发给不同的实现类，实现解耦。`Servlet`容器通过各种的映射对不同请求进行相应的转发，让不同的`servlet`进行相应的代码实现之后，再返回相应的结果

   ![image-20200404211606609](D:\App\Typora\resources\md-image\image-20200404211606609.png)

   ![img](https://images0.cnblogs.com/blog2015/449064/201506/041934454411587.png)

2. Tomcat具体组成

   主要2部分为连接器（处理请求和返回结果）和容器（利用各个servlet代码实现业务处理）

   ![image-20200404212733237](D:\App\Typora\resources\md-image\image-20200404212733237.png)

   

   ![image-20200404221837771](D:\App\Typora\resources\md-image\image-20200404221837771.png)

   

#### - 连接器（Coyote）

> `Coyote`是提供给外部请求访问的接口，也就是提供给`Servlet`容器和外界的连接的途径，将`Request`封装成`ServletRequest`交给Catalina处理，并且接受`ServletResponse`转换成`Response`作为返回结果。总之，它就是负责`IO`和网络交互.

![image-20200404213454149](D:\App\Typora\resources\md-image\image-20200404213454149.png)、

**连接器**主要由以下部分组成：

> `ProtocolHandler` 是用来接受并处理不同的连接类型，主要有 `Http11Protocol `使用的是普通`Socket `来连接的， `Http 11 NioProtocol `使用的是`NioSocket `来连接的

- `EndPoint`：是用来监听前端发来的`HTTPServlet`请求的，然后将其转发给`Processor` 

  

- `Processor`：用来将请求封装成一个`request`对象，然后转发给适配器

  `Connector`在处理`HTTP`请求时，会使用不同的`protocol`。不同的`Tomcat`版本支持的`protocol`不同，其中最典型的`protocol`包括`BIO`、`NIO和APR`（`Tomcat7`中支持这`3`种，`Tomcat8`增加了对`NIO2`的支持，而到了`Tomcat8.5`和`Tomcat9.0`，则去掉了对`BIO`的支持）

- `Adapter`：适配器将`request`对象变为`Servlet`请求，转发给容器进行处理

![image-20200404214915856](D:\App\Typora\resources\md-image\image-20200404214915856.png)

#### - 容器（Catalina）

`Tomcat`是由一系列可以配置的组件构成的`Web`容器，而`Catalina`是`Tomcat`的`servlet`组件，其实就是因为之前的轻量级`Tomcat`。通常在`Catalina`中有多个`server`，`server`中有多个`service`,一个`service`中有多个`Connector`,一个`Container` 

![image-20200404222318633](D:\App\Typora\resources\md-image\image-20200404222318633.png)

- `Catalina`： 负责`Tomcat`配置文件的解析，对`Server`进行管理

  主要做了解析`server.xml`文件，读取组件的一些设置，并且设置相应的`server-catalina`的关联的关系

- `Server`：负责整个`Servlet`的启动

- `Conector`：负责与客户端的通信，也就是接受`ServletRequest`,然后转发给绑定的`Container` 

- `Container`：负责处理用户的`Servlet`请求，并返回给用户数据



#### - 容器中的容器（Container）

​	组成：`Engine、Host、Context、Wrapper`四种容器，是父子关系![image-20200404224229675](D:\App\Typora\resources\md-image\image-20200404224229675.png)

​	具体作用：

![image-20200404224602007](D:\App\Typora\resources\md-image\image-20200404224602007.png)

​	继承关系：![image-20200404230510055](D:\App\Typora\resources\md-image\image-20200404230510055.png)

## 三、启动流程

> 主要是通过父组件的init()和Start()方法来调用子组件的该方法，链式 初始化和启动，最后才实现监听request的请求

![image-20200404231401148](D:\App\Typora\resources\md-image\image-20200404231401148.png)

- **Bootstrap** 

  > 这个是引导类，主要用于启动Tomcat的初始化

  

  ```Java
  //这个四个是Bootstrap类的四个主要属性，后面逐一补充
  private Object catalinaDaemon = null;
  
  ClassLoader commonLoader = null;
  ClassLoader catalinaLoader = null;
  ClassLoader sharedLoader = null;
  
  private static volatile Bootstrap daemon = null;
  ```

  `main`函数

  ```Java
  public static void main(String[] args) {
  	//确保线程安全
      synchronized (daemonLock) {
          if (daemon == null) {
              Bootstrap bootstrap = new Bootstrap();
              try {
                  //调用本类的init方法，见下
                  bootstrap.init();
              } catch (Throwable t) {
                  handleThrowable(t);
                  t.printStackTrace();
                  return;
              }
              //创建的bootstrap对象赋值给daemon
              daemon = bootstrap;
          } else {
              //设置线程上下文类加载器
              Thread.currentThread().setContextClassLoader(daemon.catalinaLoader);
          }
      }
  
      try {
          //一开始就默认设置为start（默认要启动Tomcat）
          String command = "start";
          if (args.length > 0) {
              //如果参数数组有的话，就赋值最后一个值
              command = args[args.length - 1];
          }
  		//要修改参数的相应的状态：stared-->start-->
  		//        			   stopd-->stop-->
          //					   configtest-->
          //所以主要是调用load(args)方法，见下
          //如果Tomcat已经启动了，则就修改args参数最后为start，并执行load方法（以start）
          if (command.equals("startd")) {
              args[args.length - 1] = "start";
              daemon.load(args);
              daemon.start();
              //如果状态是已经停止的话，则修改参数为停止，并执行停止的方法
          } else if (command.equals("stopd")) {
              args[args.length - 1] = "stop";
              daemon.stop();
              //如果是期望start Tomcat
          } else if (command.equals("start")) {
              daemon.setAwait(true);//设置这个是否为守护进程相关
              daemon.load(args);//进行相应的加载
              daemon.start();//继续start方法，后续介绍
              if (null == daemon.getServer()) {
                  System.exit(1);
              }
              //如果是期望stop，则停止相应的服务
          } else if (command.equals("stop")) {
              daemon.stopServer(args);
              //配置测试
          } else if (command.equals("configtest")) {
              daemon.load(args);
              if (null == daemon.getServer()) {
                  System.exit(1);
              }
              System.exit(0);
          } else {
              //错误的命令
              log.warn("Bootstrap: command \"" + command + "\" does not exist.");
          }
      } catch (Throwable t) {
          ...
      }
  }
  ```

  1.  `Bootstrap`的`init()`方法

  ```java 
      public void init() throws Exception {
  		//初始化类加载器，主要就是针对上面列举出来的类加载器，进行相应的初始化
          initClassLoaders();
        Thread.currentThread().setContextClassLoader(catalinaLoader);
  		//利用该类加载器，加载了相应的类（全是org.apache.tomcat下的
        SecurityClassLoad.securityClassLoad(catalinaLoader);
  
          if (log.isDebugEnabled())
              log.debug("Loading startup class");
          //利用该类加载器，加载Catalina类
          Class<?> startupClass = catalinaLoader.loadClass("org.apache.catalina.startup.Catalina");
          //实例化Catalina类
          Object startupInstance = startupClass.getConstructor().newInstance();
  
          //这个日志也说明这里设置Catalina的属性
          if (log.isDebugEnabled())
              log.debug("Setting startup class properties");
          //用于下面取得Catalina类的方法的名字
          String methodName = "setParentClassLoader";
          //下面这2个数组似乎是对应起来的
          //一个只有ClassLoader类的数组
          Class<?>[] paramTypes = new Class[1];
          paramTypes[0] = Class.forName("java.lang.ClassLoader");
          //用于存放sharedLoader来待用的
          Object[] paramValues = new Object[1];
          paramValues[0] = sharedLoader;
  		//获取到Catalina类的setParentClassLoader设置父类加载器的方法
          Method method =
              startupInstance.getClass().getMethod(methodName, paramTypes);
          //然后调用setParentClassLoader的方法,是那个shared类加载器（方法见下）
          method.invoke(startupInstance, paramValues);
  		//然后将Catalina对象赋值给catalinaDaemon
          catalinaDaemon = startupInstance;
      }
  //所以其实这个方法只是创建了一个Catalina对象
  ```

  2. `main`方法中调用的`load`方法

  ```java
  private void load(String[] arguments) throws Exception {
          // 要获得的方法名
          String methodName = "load";
    	//用于存放String[]参数的数组
          Object param[];
    	//以类对象的形式存放参数类型的数组
          Class<?> paramTypes[];
      	//如果传入的参数为空的话，则上面2个对象置空
          if (arguments==null || arguments.length==0) {
              paramTypes = null;
              param = null;
          } else {
              //否则就获取相应的参数并且取得相应的类对象
              paramTypes = new Class[1];
              paramTypes[0] = arguments.getClass();
              param = new Object[1];
              param[0] = arguments;
          }
      	//获取到Catalina对象中要调用的方法
          Method method =
              catalinaDaemon.getClass().getMethod(methodName, paramTypes);
          if (log.isDebugEnabled()) {
              log.debug("Calling startup class " + method);
          }
      	//然后调用Catalina的load方法
          method.invoke(catalinaDaemon, param);
      }
  
  ```

  3. 1 `catalina`的`start`方法(启动`Tomcat` 

  ```java 
  public void start() {
  	//这个server对象主要是获取一些基础的服务配置（端口\ip\是否启动）所用，不具体展开，但是他们都是有生命周期的
      //如果server对象不存在，则未对Catalina进行初始化，进行load方法
        if (getServer() == null) {
              load();
        }
  		
          if (getServer() == null) {
              log.fatal("Cannot start server. Server instance is not configured.");
              return;
          }
  
          long t1 = System.nanoTime();
  
          // Start the new server
          try {
              getServer().start();
          } catch (LifecycleException e) {
              log.fatal(sm.getString("catalina.serverStartFail"), e);
              try {
                  getServer().destroy();
              } catch (LifecycleException e1) {
                  log.debug("destroy() failed for failed Server ", e1);
              }
              return;
          }
  
          long t2 = System.nanoTime();
          if(log.isInfoEnabled()) {
              log.info("Server startup in " + ((t2 - t1) / 1000000) + " ms");
          }
  
          // Register shutdown hook
          if (useShutdownHook) {
              if (shutdownHook == null) {
                  shutdownHook = new CatalinaShutdownHook();
              }
              Runtime.getRuntime().addShutdownHook(shutdownHook);
  
              // If JULI is being used, disable JULI's shutdown hook since
              // shutdown hooks run in parallel and log messages may be lost
              // if JULI's hook completes before the CatalinaShutdownHook()
              LogManager logManager = LogManager.getLogManager();
              if (logManager instanceof ClassLoaderLogManager) {
                  ((ClassLoaderLogManager) logManager).setUseShutdownHook(
                          false);
              }
          }
  
          if (await) {
              await();
              stop();
          }
      }
  ```

  3.1.1 `catalina`的`load` 方法

  ```Java
  public void load() {
  	//如果已经记载过了，则直接退出
    if (loaded) {
          return;
    }
      //标识已经加载过了
      loaded = true;
  	//获取当前的时间戳
      long t1 = System.nanoTime();
  
      initDirs();//初始化操作系统缓存的临时目录的路径
  
      // 设置一些系统变量
      initNaming();
  
      // 用于解析XML文件的
      Digester digester = createStartDigester();
  
      InputSource inputSource = null;
      InputStream inputStream = null;
      File file = null;
      try {
          try {
              file = configFile();
              inputStream = new FileInputStream(file);
              inputSource = new InputSource(file.toURI().toURL().toString());
          } catch (Exception e) {
              if (log.isDebugEnabled()) {
                  log.debug(sm.getString("catalina.configFail", file), e);
              }
          }
          
          //一些为了确保加载到相应的配置文件所用
  		...
              
          try {
              inputSource.setByteStream(inputStream);
              //交给它进行XML文件的解析
              digester.push(this);
              digester.parse(inputSource);
          } 
          //关闭输入流
          ...
      }
  
      // 设置Server关联这个Catalina和相关的配置
      getServer().setCatalina(this);
      getServer().setCatalinaHome(Bootstrap.getCatalinaHomeFile());
      getServer().setCatalinaBase(Bootstrap.getCatalinaBaseFile());
  
      ...
  
      // 初始化相应的服务，这里具体的实现在StandardServer类中见下
      try {
          getServer().init();
      } catch (LifecycleException e) {
          if (Boolean.getBoolean("org.apache.catalina.startup.EXIT_ON_INIT_FAILURE")) {
              throw new java.lang.Error(e);
          } else {
              log.error("Catalina.start", e);
          }
      }
  
      //输出所用时间
      long t2 = System.nanoTime();
      if(log.isInfoEnabled()) {
          log.info("Initialization processed in " + ((t2 - t1) / 1000000) + " ms");
      }
  }
  ```

  3.1.2 `Server`的初始化 `StandarServer`下的`init`方法

  ```Java
  //用于存放service的地方，可以看到这里也是有很多的Service
  private Service services[] = new Service[0];
  protected void initInternal() throws LifecycleException {
      super.initInternal();
      
    ...
    
    // 初始化/定义service，并调用相应的初始化方法
    for (int i = 0; i < services.length; i++) {
        services[i].init();
    }
  }
  
  ```

    3.1.3 `Service`的初始化 `StandarServer`的`init`方法

  ```Java
      
  //每个service下都有一个engine
    private Engine engine = null;
  
  protected void initInternal() throws LifecycleException {
      super.initInternal();
    
    	//调用相应的engine下的初始化方法，见下
        if (engine != null) {
            engine.init();
        }
    
       // 初始化线程池其实也就是为每个线程池，设置域名，所以可以看出来，一般是一个service有多个连接池
        for (Executor executor : findExecutors()) {
            if (executor instanceof JmxEnabled) {
                ((JmxEnabled) executor).setDomain(getDomain());
            }
            //对每个线程池进行初始化
            executor.init();
        }
    
        // 这里主要是做了，读取了Web.xml中的部分映射（默认的+配置的
        mapperListener.init();
    
        synchronized (connectorsLock) {
            for (Connector connector : connectors) {
                try {
                    //初始化了connector
                    connector.init();
                }
                ...
            }
        }
    }
  ```

  3.1.4 `engine`主要负责`Realm`域的调用相关的方法

  3.2 `Connector`需要

- sd 







在**Lifecycle**接口中，有init stop start destroy



```Java
public static void main(String args[]) {

    synchronized (daemonLock) {
        if (daemon == null) {
            // Don't set daemon until init() has completed
            Bootstrap bootstrap = new Bootstrap();
            try {
                bootstrap.init();//调用初始化的方法，将容器赋值给
            } catch (Throwable t) {
                handleThrowable(t);
                t.printStackTrace();
                return;
            }
            daemon = bootstrap;
        } else {
            // When running as a service the call to stop will be on a new
            // thread so make sure the correct class loader is used to
            // prevent a range of class not found exceptions.
            Thread.currentThread().setContextClassLoader(daemon.catalinaLoader);
        }
    }

    try {
        String command = "start";
        if (args.length > 0) {
            command = args[args.length - 1];
        }

        if (command.equals("startd")) {
            args[args.length - 1] = "start";
            daemon.load(args);
            daemon.start();
        } else if (command.equals("stopd")) {
            args[args.length - 1] = "stop";
            daemon.stop();
        } else if (command.equals("start")) {
            daemon.setAwait(true);
            daemon.load(args);
            daemon.start();
            if (null == daemon.getServer()) {
                System.exit(1);
            }
        } else if (command.equals("stop")) {
            daemon.stopServer(args);
        } else if (command.equals("configtest")) {
            daemon.load(args);
            if (null == daemon.getServer()) {
                System.exit(1);
            }
            System.exit(0);
        } else {
            log.warn("Bootstrap: command \"" + command + "\" does not exist.");
        }
    } catch (Throwable t) {
        // Unwrap the Exception for clearer error reporting
        if (t instanceof InvocationTargetException &&
                t.getCause() != null) {
            t = t.getCause();
        }
        handleThrowable(t);
        t.printStackTrace();
        System.exit(1);
    }
}
```

```Java
/**
 * Initialize daemon.初始化了Catalina的
 * @throws Exception Fatal initialization error
 */
public void init() throws Exception {

    initClassLoaders();//类加载

   	...

    catalinaDaemon = startupInstance;//startupInstance是Catalina进程通过反射创建实例对象，并赋值给catalinaDaemon
}
```

```Java
/**
 * Load daemon.加载Catalina的Load方法
 */
private void load(String[] arguments) throws Exception {

    // Call the load() method
    String methodName = "load";
    Object param[];
    Class<?> paramTypes[];
    if (arguments==null || arguments.length==0) {
        paramTypes = null;
        param = null;
    } else {
        paramTypes = new Class[1];
        paramTypes[0] = arguments.getClass();
        param = new Object[1];
        param[0] = arguments;
    }
    Method method =
        catalinaDaemon.getClass().getMethod(methodName, paramTypes);
    if (log.isDebugEnabled()) {
        log.debug("Calling startup class " + method);
    }
    method.invoke(catalinaDaemon, param);//这里调用的是Catalinna里面的invoke方法
}
```

## 四、请求处理流程

一个Wrapper就是一个Servlet

![image-20201114130748036](D:\App\Typora\resources\md-image\image-20201114130748036.png)

![image-20200406231257320](D:\App\Typora\resources\md-image\image-20200406230455371.png)



![image-20201114133629881](D:\App\Typora\resources\md-image\image-20201114133629881.png)

## 五、Jasper

### 5.1 简介

是JSP的核心引擎，JSP本质是一个Servlet，Tomcat使用Jasper解析JSP语法，生成Servlet和Class字节码，本质访问的是Servlet

### 5.2 编译方式

​	 运行时编译

​	预编译

![image-20200407145603784](D:\App\Typora\resources\md-image\image-20200407145603784.png)



---

## 六、目录结构

> Tomcat的配置文件主要存放于根目录下的conf文件,主要为以下

- **server.xml** 

  主要存放`Tomcat`的相关组件的设置

- **tomcat-users.xml** 

  存放相关用户的定义与权限

- **web.xml** 

  一些主要的相关设置，和基本的映射

- **logging.properties** 

  关于日志的相关设置


> Tomcat的一些命令主要存放于bin目录下

- **startup.bat ** 

  启动Tomcat服务

- **shutdown.bat ** 

  关闭Tomcat服务

> Tomcat的相关web项目都存放于webapps目录下

这里存放的就是相当于`Context`，就比如localhost:8080/test/xxxx，这里的`test` 

> Tomcat的相关依赖jar包存放于lib目录，相关日志存放于logs目录，一些临时文件存放于tmp目录，jsp解析的Java字节码之类的存放于work目录下

## 七、类的生命周期

`Tomcat`相关的一些类，都继承了一个`LifeCycle`接口,这个接口是用于管理`Tomcat`类的生命周期的，具体如下：

```Java
/**
 *            start()
 *  -----------------------------
 *  |                           |
 *  | init()                    |
 * NEW -»-- INITIALIZING        |
 * | |           |              |     ------------------«-----------------------
 * | |           |auto          |     |                                        |
 * | |          \|/    start() \|/   \|/     auto          auto         stop() |
 * | |      INITIALIZED --»-- STARTING_PREP --»- STARTING --»- STARTED --»---  |
 * | |         |                                                            |  |
 * | |destroy()|                                                            |  |
 * | --»-----«--    ------------------------«--------------------------------  ^
 * |     |          |                                                          |
 * |     |         \|/          auto                 auto              start() |
 * |     |     STOPPING_PREP ----»---- STOPPING ------»----- STOPPED -----»-----
 * |    \|/                               ^                     |  ^
 * |     |               stop()           |                     |  |
 * |     |       --------------------------                     |  |
 * |     |       |                                              |  |
 * |     |       |    destroy()                       destroy() |  |
 * |     |    FAILED ----»------ DESTROYING ---«-----------------  |
 * |     |                        ^     |                          |
 * |     |     destroy()          |     |auto                      |
 * |     --------»-----------------    \|/                         |
 * |                                 DESTROYED                     |
 * |                                                               |
 * |                            stop()                             |
 * ----»-----------------------------»------------------------------
 */
public interface Lifecycle {
    
	/**
     * 这部分是用于标记这个类正处于那种状态，一共有13种状态
     */
    public static final String BEFORE_INIT_EVENT = "before_init";
    public static final String AFTER_INIT_EVENT = "after_init";
    public static final String START_EVENT = "start";
    public static final String BEFORE_START_EVENT = "before_start";
    public static final String AFTER_START_EVENT = "after_start";
    public static final String STOP_EVENT = "stop";
    public static final String BEFORE_STOP_EVENT = "before_stop";
    public static final String AFTER_STOP_EVENT = "after_stop";
    public static final String AFTER_DESTROY_EVENT = "after_destroy";
    public static final String BEFORE_DESTROY_EVENT = "before_destroy";
    public static final String PERIODIC_EVENT = "periodic";
    public static final String CONFIGURE_START_EVENT = "configure_start";
    public static final String CONFIGURE_STOP_EVENT = "configure_stop";
    
    /**
     * 这是用于管理类的生命周期监听器的
     */
    public void addLifecycleListener(LifecycleListener listener);
    public LifecycleListener[] findLifecycleListeners();
    public void removeLifecycleListener(LifecycleListener listener);
    
    /**
     * 用于进行类的生命周期状态转变的
     */
    public void init() throws LifecycleException;
    public void start() throws LifecycleException;
    public void stop() throws LifecycleException;
    public void destroy() throws LifecycleException;
    
    /**
     * 用于获取当前的生命周期的状态相关方法
     */
    public LifecycleState getState();
    public String getStateName();//这里就是返回相应的方法状态
    
    /**
     * 标记接口用来表示该实例只能使用一次。 调用stop()上的一个实例支持该界面会自动调用destroy()后stop()完成
     */
    public interface SingleUse {
    }
}

```

- **事件监听机制** 

  `Tomcat`针对不同的事件维护了一套事件监听的机制，主要对象有3个：**事件源**、**监听接口**、**事件对象** 

  1. 事件源

     通过参数来生成事件对象，然后将事件对象扔给事件监听器来进行,所以事件源里存有相应的**事件对象**和**监听器** 

     ```java 
     public abstract class LifecycleBase implements Lifecycle {
         	...
         //这是源组件的当前状态
         private volatile LifecycleState state = LifecycleState.NEW;
         
         //这是LifecycleBase所持有的所有监听器，是线程安全的
         private final List<LifecycleListener> lifecycleListeners = new CopyOnWriteArrayList<>();
     	
         //将事件抛入所有监听器
         protected void fireLifecycleEvent(String type, Object data) {
              //这是根据参数创建事件对象LifecycleEvent，type和data
         	LifecycleEvent event = new LifecycleEvent(this, type, data);
             //对每个监听器传入相应的事件，使其做出反应
         	for (LifecycleListener listener : lifecycleListeners) {
             	listener.lifecycleEvent(event);
         	}
     	}
         	...
     }
     ```

  2. 事件对象

     `Java`中有`EventObject`类是用于定义事件对象的，事件对象就相当于是用当某个条件发生的时候，它要做的事件

     ```Java
     public class EventObject implements java.io.Serializable {
     
         private static final long serialVersionUID = 5516075349620653480L;
     	//用于存放事件资源实例
         protected transient Object  source;
     	//必须source不为null
         public EventObject(Object source) {
             if (source == null)
                 throw new IllegalArgumentException("null source");
             this.source = source;
         }
     	//获取相应的资源
         public Object getSource() {
             return source;
         }
         public String toString() {
             return getClass().getName() + "[source=" + source + "]";
         }
     }
     ```

     `EventObject`的具体实现类

     ```Java
     public final class LifecycleEvent extends EventObject {
     
         private static final long serialVersionUID = 1L;
     	//这里调用了父类的构造函数，并且将相应的资源赋值进去，设置当前事件的类型和数据
         public LifecycleEvent(Lifecycle lifecycle, String type, Object data) {
             super(lifecycle);
             this.type = type;
             this.data = data;
         }
     	//和这个事件对象相关的数据
         private final Object data;
     	//这个事件对象的类型
         private final String type;
     	...
         //一些常用的get方法
     }
     ```

  3. 监听器

     这个就是相当于事件源里面维护的队列的节点元素，监听接口是对应着一个监听对象的。它在监听到相应的事件后，便会做相应的事件对象所指的事情（这里其实类似于观察者模式

     ```java 
     public interface LifecycleListener {
     	//用于相映相应的LifecycleEvent
         public void lifecycleEvent(LifecycleEvent event);
     }
     ```

## 八、Pipeline和Valve

对每个`Container`内部的容器其实都绑定着一个`Pipeline`，称之为管道，在管道的内部维护着一个`valve`的队列，对每个管道都有一个默认的`valve`的实现，它是默认调用内部容器的`pipeline` 的。这是因为容器内部的所有类都继承了`ContainerBase`,而这个类内部如下：

```Java
public abstract class ContainerBase extends LifecycleMBeanBase
        implements Container {
    ...
      //用于存放这个容器的子类的
      protected final HashMap<String, Container> children = new HashMap<>();
    
      //绑定该容器的pipeline的默认实现
      protected final Pipeline pipeline = new StandardPipeline(this);
    
      //这个是每个容器的Realm域，在下面有提及
      private volatile Realm realm = null;
    ...
}
```

以及在`StandardPipeline`中有维护这个链表的部分代码

```java 
public void addValve(Valve valve) {

    // Validate that we can add this Valve
    if (valve instanceof Contained)
        ((Contained) valve).setContainer(this.container);

    // Start the new component if necessary
    if (getState().isAvailable()) {
        if (valve instanceof Lifecycle) {
            try {
                ((Lifecycle) valve).start();
            } catch (LifecycleException e) {
                log.error(sm.getString("standardPipeline.valve.start"), e);
            }
        }
    }

    // 这里维护了链表，做头部插入法
    if (first == null) {
        first = valve;
        valve.setNext(basic);
    } else {
        Valve current = first;
        while (current != null) {
            if (current.getNext() == basic) {
                current.setNext(valve);
                valve.setNext(basic);
                break;
            }
            current = current.getNext();
        }
    }
    //调用容器的的处理事件的方法，在上面提到了这和类的生命周期相关
    container.fireContainerEvent(Container.ADD_VALVE_EVENT, valve);
}
```

这样链式的调用`valve`是为了让各个容器之间，这样每个上层valve不仅具有request对象，同时还能拿到response对象，其实也运用了责任链模式，调用每个`valve`,完成不用的功能

## 九、Realm域

> `Realm`是用于管理用户权限的，因为`Tomcat`是基于角色的权限访问控制方式,采用的一般是`用户--角色--权限--应用`的模式，也即是一个用户有多个角色，角色有多个权限，一个应用有多种功能上划分的不同权限

`Realm`域的相关设置，在`server.xml`中有设置域如下（这个是被设置在`engine`下的）：

```java 
  <Realm className="org.apache.catalina.realm.LockOutRealm">
        <!-- This Realm uses the UserDatabase configured in the global JNDI
             resources under the key "UserDatabase".  Any edits
             that are performed against this UserDatabase are immediately
             available for use by the Realm.  -->
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>
```

- 其实`Realm`域可以被设置在多个位置，并且作用域是不同的：

1. 在<Engine>元素内-除非被嵌套在从属<Host>或<Context>元素中的Realm元素覆盖，否则该领域将在所有虚拟主机上的所有web应用程序之间共享。

2. 在<Host>元素内-此领域将在该虚拟主机的所有web应用程序中共享，除非它被嵌套在从属<Context>元素中的Realm元素覆盖。

3. 在<Context>元素中-此域将仅用于此web应用程序。

   

- 不同的域 作用也不同，主要有如下几种：(详细差别见文档：http://tomcat.apache.org/tomcat-7.0-doc/realm-howto.html)

1. ## UserDatabaseRealm

2. ## JDBCRealm

3. ## JNDIRealm

4. ## JAASRealm

5. ## CombinedRealm

6. ## LockOutRealm



- 还有几种比较核心的类（其实这里有点类似于`shiro`的认证方式

  1. `CombinedRealm` 

     |	在其内部包含有一个或多个`Realm` ,需要按照配置顺序执行身份顺序（责任链模式），任一验证成功了，则|表| |	示成功了

  2. `LockOutRealm` 

     |	提供用户锁定的功能，防止在一定的时间段内有过多的身份验证失败

  3. `Authenticator ` 

     |	不同身份验证方法的接口，如`BASIC、DIGEST、FORM、SSL`等不同的验证方式

     ```Java
     public interface Authenticator {
     	//根据请求进行认证的方法
         public boolean authenticate(Request request, HttpServletResponse response)
                 throws IOException;
     	//进行登录
         public void login(String userName, String password, Request request)
                 throws ServletException;
     	//登出
         public void logout(Request request);
     }
     ```

     下面来看看详细的认证过程，这是`AuthenticatorBase`的`invoke`方法

     ```Java
     @Override
     public void invoke(Request request, Response response) throws IOException, ServletException {
     
         if (log.isDebugEnabled()) {
             log.debug("Security checking request " + request.getMethod() + " " +
                     request.getRequestURI());
         }
     
         // Have we got a cached authenticated Principal to record?
         //如果要取得缓存的话，就直接获取request的principal，这个应该是前端缓存了的，然后封装在request里传过来的？？？？如果这样的话，那么我们修改下前端传来的参数不就可以跳过鉴权了？
         if (cache) {
             Principal principal = request.getUserPrincipal();
             //如果没有做缓存的话，就从session里取
             if (principal == null) {
                 Session session = request.getSessionInternal(false);
                 if (session != null) {
                     principal = session.getPrincipal();
                     //如果从session里取出来的不为空
                     if (principal != null) {
                         if (log.isDebugEnabled()) {
                             log.debug("We have cached auth type " + session.getAuthType() +
                                     " for principal " + principal);
                         }
                         //设置请求方式
                         request.setAuthType(session.getAuthType());
                         //设置用户权限
                         request.setUserPrincipal(principal);
                     }
                 }
             }
         }
         
     	//这个用于判断这个请求是否需要
         boolean authRequired = isContinuationRequired(request);
     	
         //从context组件里获取相应的Realm域，默认的是LockOutRealm
         Realm realm = this.context.getRealm();
         //根据请求 URI 尝试获取配置的安全约束这个是根据Realm域以及请求获取到相应资源所需要的约束
         SecurityConstraint[] constraints = realm.findSecurityConstraints(request, this.context);
     	//获取到认证器的提供者，并将需要认证设置为
         AuthConfigProvider jaspicProvider = getJaspicProvider();
         
         if (jaspicProvider != null) {
             authRequired = true;
         }
     	//判断约束是否为空，并且访问的是否不是不受约束的资源（对外公开），并且是需要请求认证的
         if (constraints == null && !context.getPreemptiveAuthentication() && !authRequired) {
             if (log.isDebugEnabled()) {
                 log.debug("Not subject to any constraint");
             }
             // 为 null 表示访问的资源没有安全约束，直接访问下一个阀门
             getNext().invoke(request, response);
             return;
         }
         
     	//设置请求体，以及部分日志处理
        ...
     	
         //有授权约束的标识
         boolean hasAuthConstraint = false;
         //如果有约束
         if (constraints != null) {
             //则约束设为true
             hasAuthConstraint = true;
             //对每个约束进行判断，为这个安全约束返回授权约束存在标志
             for (int i = 0; i < constraints.length && hasAuthConstraint; i++) {
                 //如果一但没有目的资源需要授权，则就将需要授权标识false
                 if (!constraints[i].getAuthConstraint()) {
                     hasAuthConstraint = false;
                 } else if (!constraints[i].getAllRoles() &&
                         !constraints[i].getAuthenticatedUsers()) {
                     //寻找相应的授权约束所拥有的认证角色
                     String[] roles = constraints[i].findAuthRoles();
                     //如果没有
                     if (roles == null || roles.length == 0) {
                         hasAuthConstraint = false;
                     }
                 }
             }
         }
     
         if (!authRequired && hasAuthConstraint) {
             authRequired = true;
         }
     
         if (!authRequired && context.getPreemptiveAuthentication()) {
             authRequired =
                     request.getCoyoteRequest().getMimeHeaders().getValue("authorization") != null;
         }
     
         if (!authRequired && context.getPreemptiveAuthentication() &&
                 HttpServletRequest.CLIENT_CERT_AUTH.equals(getAuthMethod())) {
             X509Certificate[] certs = getRequestCertificates(request);
             authRequired = certs != null && certs.length > 0;
         }
     
         JaspicState jaspicState = null;
     
         if ((authRequired || constraints != null) && allowCorsPreflightBypass(request)) {
             if (log.isDebugEnabled()) {
                 log.debug("CORS Preflight request bypassing authentication");
             }
             getNext().invoke(request, response);
             return;
         }
     
         if (authRequired) {
             if (log.isDebugEnabled()) {
                 log.debug("Calling authenticate()");
             }
     
             if (jaspicProvider != null) {
                 jaspicState = getJaspicState(jaspicProvider, request, response, hasAuthConstraint);
                 if (jaspicState == null) {
                     return;
                 }
             }
     		//这里是确实验证用户名密码的地方
             if (jaspicProvider == null && !doAuthenticate(request, response) ||
                     jaspicProvider != null &&
                             !authenticateJaspic(request, response, jaspicState, false)) {
                 if (log.isDebugEnabled()) {
                     log.debug("Failed authenticate() test");
                 }
                 /*
                  * ASSERT: Authenticator already set the appropriate HTTP status
                  * code, so we do not have to do anything special
                  */
                 return;
             }
     
         }
     
         if (constraints != null) {
             if (log.isDebugEnabled()) {
                 log.debug("Calling accessControl()");
             }
             //验证用户是否包含授权的角色
             if (!realm.hasResourcePermission(request, response, constraints, this.context)) {
                 if (log.isDebugEnabled()) {
                     log.debug("Failed accessControl() test");
                 }
                 /*
                  * ASSERT: AccessControl method has already set the appropriate
                  * HTTP status code, so we do not have to do anything special
                  */
                 return;
             }
         }
     
         // Any and all specified constraints have been satisfied
         if (log.isDebugEnabled()) {
             log.debug("Successfully passed all security constraints");
         }
         //满足任何和所有指定的约束应该是链式调用认证器
         getNext().invoke(request, response);
     
         if (jaspicProvider != null) {
             secureResponseJspic(request, response, jaspicState);
         }
     }
     ```

     另外，`AuthenticatorBase `还有一个比较重要的 `register()` 方法，它会把认证后生成的 `Principal `对象设置到当前 `Session `中，如果配置了`SingleSignOn `单点登录的阀门，同时把用户身份、权限信息关联到 `SSO `中。

  4. `Principal`

     |	对认证主体的抽象，包含用户角色和权限

  5. `SingleSignOn ` 

     |	为了支持单点登录的

     

     
     
     
     
     
     
     
     
     
     
     
     
     
     
     
     
     