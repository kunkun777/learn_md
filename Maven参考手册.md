# Maven参考手册

---

## SpringBoot

- 分页

```java
<!-- 分页支持pageHelper -->
<dependency>
   <groupId>com.github.pagehelper</groupId>
   <artifactId>pagehelper-spring-boot-starter</artifactId>
   <version>${pagehelper.version}</version>
</dependency>
```

- SpringBoot父工厂

  

  ```java
  <!-- lookup parent from repository -->    
  <parent>
          <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.0.0.RELEASE</version>
          <relativePath/> 
      </parent>
  ```
  
- 修改参数（其他类比）

  ```java
  <properties>
  	<java.version>1.8</java.version>
  </properties>
  ```

- web支持\MVC

```java
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
```

- thymeleaf

  ```java
      <dependency>
          <groupId>org.thymeleaf</groupId>
          <artifactId>thymeleaf-spring5</artifactId>
      </dependency>
      <dependency>
          <groupId>org.thymeleaf.extras</groupId>
          <artifactId>thymeleaf-extras-java8time</artifactId>
          <version>3.0.4.RELEASE</version>
      </dependency>
  ```

- Shiro与Spring的整合依赖

```java
<dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-spring</artifactId>
        <version>1.4.1</version>
    </dependency>
```

- 数据库相关的maven

  ```java
  <!--mysql驱动-->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.19</version>
  </dependency>
  <!--数据源-->
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.12</version>
  </dependency>
  <!--mybatis-->
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.1.0</version>
  </dependency>
  ```

- lombok插件

  ```Java
  <!--lombok插件-->
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.16.10</version>
  </dependency>
  ```
  
- Log4j

  ```Java
  <!--log4j的包-->
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
  </dependency>
  ```

- 测试单元

  ```java
  <dependency>
          <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
          </dependency>
  ```

- web启动器

  ```java
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
  ```

- 热部署devtool

  ```java 
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-maven-plugin</artifactId>
      </dependency>
  
  <build>
      <plugins>
          <plugin>          <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
              <configuration>
                  <fork>
                      true
                  </fork>
              </configuration>
          </plugin>
      </plugins>
  </build>
  ```

- Jsp的标签库

  ```java
   <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>jstl</artifactId>
      </dependency>
      <!--内置容器支持jsp-->
       <dependency>
          <groupId>org.apache.tomcat.embed</groupId>
          <artifactId>tomcat-embed-jasper</artifactId>
      </dependency>
  ```

- 数据库druid连接池

  ```java
  <!--阿里巴巴连接池-->
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid</artifactId>
              <version>1.0.9</version>
          </dependency>
  ```

- Swagger

  ```java
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.9.2</version>
  </dependency>
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.9.2</version>
  </dependency>
  
  ```

- 可以监控的druid数据源

  ```java
  <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid-spring-boot-starter</artifactId>
              <version>1.1.9</version>
          </dependency>
  ```

- redis-SpringBoot

  ```Java
         <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-data-redis</artifactId>
          </dependency>
  <!--将对象转换成String类型，符合阿里巴巴的规则-->
         <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>fastjson</artifactId>
              <version>1.2.38</version>
          </dependency>
  ```

- Jedis客户端

  ```java
          <dependency>
              <groupId>redis.clients</groupId>
              <artifactId>jedis</artifactId>
          </dependency>
  ```

- Redis客户端（默认是Lettuce）

```java
<!--默认是lettuce客户端-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
<!--        redis依赖的包，一定要添加-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
```

- rabbitmq

  ```java
      <dependency>
          <groupId>com.rabbitmq</groupId>
          <artifactId>amqp-client</artifactId>
          <version>4.0.2</version>
      </dependency>
  ```

  