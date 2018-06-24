

# SpringCloud 完整案例第二季

## 一、开发环境准备

idea：IDEA ULTIMATE 2018.1

java：1.8

maven：3.5



## 二、创建工程（注意有坑）

### 1. 首先创建一个空工程

![1527061559111](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets/1527061559111.png)



![1527061633206](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets/1527061633206.png)



### 2. 创建并测试Eureka Server 的module

#### 2.1 创建module

![1527062156109](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets/1527062156109.png)



![1527062251874](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets/1527062251874.png)



![1527062349888](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets/1527062349888.png)



#### 2.2 yml

```yaml
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

#### 2.3 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>eureka</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>eureka</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>
```

#### 2.4 主启动类

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```



#### 2.5 启动测试

![1527063398748](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527063398748.png)



- [IDEA 安装lombok插件](https://blog.csdn.net/qq_32447321/article/details/60570051)

  ​

### 4. 创建provider 模块（添加web和eureka discovery 依赖）



![1527131303000](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527131303000.png)



#### 4.1 application.yml

```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/     # 注册到指定的eureka server 服务器上
server:
  port: 8762
spring:
  application:
    name: service-provider                  # 服务名（很重要）
```

#### 4.2 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>provider</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>
```

#### 4.3 service

```java
/**
 * @ClassName UserService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class UserService {
    public String get(){
        return "cris";
    }
}
```

#### 4.4 controller

```java
/**
 * @ClassName UserController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class UserController {

    @Autowired
    private UserService service;

    @Value("${server.port}")
    private String port;

    @GetMapping("/provider/get")
    public String get(){
        return service.get() + port;
    }
}
```

#### 4.5 主启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class ProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }
}
```

#### 4.5 启动并测试

![1527065454644](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527065454644.png)





### 5. 快速启动多个不同端口的Provider 模块

![1527066091423](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527066091423.png)



- 启动并测试

  ![1527131699834](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527131699834.png)



![1527131717378](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527131717378.png)



![1527066163847](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527066163847.png)



### 6. 引入Feign + hystrix组件的公共模块

> 注意：这里不建议使用Ribbon 组件+RestTemplate 的方式来构建消费模块，原因在第一季已经阐述，这里直接上手Feign 组件以及hystrix

#### 6.1 创建公共模块（添加Feign 依赖）

![1527131826360](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527131826360.png)

#### 6.2 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>common</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>

```

- 注意，这里创建公共模块的时候直接使用spring initial工具创建，其他的模块（消费模块，生产模块）需要引用公共模块直接导入GAV 即可，而不是传统意义上的maven模块

#### 6.2 新建映射到服务模块controller 的client并且进行hystrix 处理

```java
/**
 * @ClassName UserClientService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@FeignClient(value = "SERVICE-PROVIDER", fallbackFactory = UserFallbackFactory.class)
public interface UserClientService {

    @GetMapping("/provider/get")
    public String get();

}
```

#### 6.3 hystrix 处理逻辑(AOP)

```java
/**
 * @ClassName UserFallbackFactory
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Component
public class UserFallbackFactory implements FallbackFactory<UserClientService> {

    @Override
    public UserClientService create(Throwable throwable) {
        return new UserClientService() {
            @Override
            public String get() {
                return "当前服务模块不可用";
            }
        };
    }
}
```

#### 6.4 主启动类

```java
@SpringBootApplication
public class CommonApplication {

    public static void main(String[] args) {
        SpringApplication.run(CommonApplication.class, args);
    }
}
```



#### 6.5 mvn install



### 7. 新建消费模块（添加Feign，web，eureka discovery 依赖）

![1527132353824](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527132353824.png)



#### 7.1 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>consumer</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>consumer</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.cris</groupId>
            <artifactId>common</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>
```

#### 7.2 application.yml

```yaml
eureka:
  client:
    register-with-eureka: false		# 试试不把自己注册到eureka server 中，是否可以拿到eureka server上的服务信息列表（答案是可以的）
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
server:
  port: 8765
spring:
  application:
    name: service-feign
feign:
  hystrix:
      enabled: true		// 支持熔断处理
```

#### 7.3 controller

```java
/**
 * @ClassName UserController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class UserController {

    @Autowired
    private UserClientService service;

    @GetMapping("/consumer/user/get")
    public String get(){
        return service.get();
    }

    @GetMapping("/consumer/get")
    public String getUser(){
        return "cris";
    }

}
```

#### 7.4 主启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients(basePackages = {"com.cris"})
@ComponentScan("com.cris")
public class ConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

#### 7.5 启动并测试

- 先启动eureka server，然后启动我们集成了feign 组件的消费模块

  ![1527078762968](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527078762968.png)



![1527132649240](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527132649240.png)



- 启动我们的服务模块（多端口）再测试

  ![1527132678086](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527132678086.png)

  ![1527132689694](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527132689694.png)

  ![1527132704990](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527132704990.png)

  ​

  ![1527133170430](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527133170430.png)



### 8. 新建监控模块（引入hystrix Dashboard依赖）

![1527144534676](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527144534676.png)



#### 8.1 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>dashboard</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>dashboard</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>
```

#### 8.2 主启动类

```java
@SpringBootApplication
@EnableHystrixDashboard
@EnableDiscoveryClient  // 其实和@EnableEurekaClient 功能相似，但是多用于其他服务中心
public class DashboardApplication {

    public static void main(String[] args) {
        SpringApplication.run(DashboardApplication.class, args);
    }
}
```

#### 8.3 yml文件

```yaml
server:
  port: 8766
spring:
  application:
    name: service-dashboard
```

#### 8.4 启动并自测

![1527145597466](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527145597466.png)

#### 8.5 服务模块添加actuator 监控组件和hystrix 依赖

```xml
        <!-- actuator 监控信息完善 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!-- 引入hystrix 熔断器组件 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
```

#### 8.6 启动服务模块测试hystrix dashboard 组件（有坑）

- 因为使用的是最新版本的SpringCloud（Finchley.BUILD-SNAPSHOT）和SpringBoot（2.0.2.RELEASE）

  和之前使用1.5版本的时候不一样，所以填坑如下

  ![1527162272953](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527162272953.png)

  - 如果说访问以上路径出现404，[请参考这篇文章](https://blog.csdn.net/ddxd0406/article/details/79643059)

  - 我的解决方案是：

    服务模块的yml文件

    ```yaml
    eureka:
      client:
        serviceUrl:
          defaultZone: http://localhost:8761/eureka/
    server:
      port: 8762
    spring:
      application:
        name: service-provider

    management:
      endpoints:
        web:
          exposure:
            include: "*"
    ```

- 然后如果出现进入dashboard 管理界面一直loading 的情况，[请参考这篇文章](https://www.cnblogs.com/hejianjun/p/8670693.html)

- 但是问题又来了，因为我是把熔断，降级处理全部抽取出来放在公共的feign 代理接口中进行处理的，所以我配置完以后还是一直loading... --！

  ![1527162785361](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527162785361.png)

  ![1527162876428](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527162876428.png)

  ​

  - 解决方案：其实很简单，直接修改服务模块的服务方法，将其标记为@HystrixCommand(),因为我们的hystrix dashboard 不是所有业务方法都会监控的，只有标记了需要熔断或是降级处理的方法才会被监控起来

    ```java
    /**
     * @ClassName UserController
     * @Description TODO
     * @Author zc-cris
     * @Version 1.0
     **/
    @RestController
    public class UserController {

        @Autowired
        private UserService service;

        @Value("${server.port}")
        private String port;

        @GetMapping("/provider/get")
        @HystrixCommand()
        public String get(){
            return service.get() + port;
        }
    }
    ```

  - 然后修改provider 模块的主启动类

    ```java
    @EnableEurekaClient // 这个服务模块启动后自动注册进Eureka Server
    @SpringBootApplication
    @EnableCircuitBreaker // 对hystrix 熔断器的支持
    @EnableHystrix
    public class ProviderApplication {

        public static void main(String[] args) {
            SpringApplication.run(ProviderApplication.class, args);
        }
    }
    ```

    - 再修改provider 模块的pom.xml

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
          <modelVersion>4.0.0</modelVersion>

          <groupId>com.cris</groupId>
          <artifactId>provider</artifactId>
          <version>0.0.1-SNAPSHOT</version>
          <packaging>jar</packaging>

          <name>provider</name>
          <description>Demo project for Spring Boot</description>

          <parent>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-parent</artifactId>
              <version>2.0.2.RELEASE</version>
              <relativePath/> <!-- lookup parent from repository -->
          </parent>

          <properties>
              <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
              <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
              <java.version>1.8</java.version>
              <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
          </properties>

          <dependencies>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-web</artifactId>
              </dependency>
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
              </dependency>
      ```


              <!-- 引入hystrix 熔断器组件 -->
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
              </dependency>
    
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-web</artifactId>
              </dependency>
    
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-test</artifactId>
              </dependency>
              <!-- actuator 监控信息完善 -->
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-actuator</artifactId>
              </dependency>

          </dependencies>
    
          <dependencyManagement>
              <dependencies>
                  <dependency>
                      <groupId>org.springframework.cloud</groupId>
                      <artifactId>spring-cloud-dependencies</artifactId>
                      <version>${spring-cloud.version}</version>
                      <type>pom</type>
                      <scope>import</scope>
                  </dependency>
              </dependencies>
          </dependencyManagement>
    
          <build>
              <plugins>
                  <plugin>
                      <groupId>org.springframework.boot</groupId>
                      <artifactId>spring-boot-maven-plugin</artifactId>
                  </plugin>
              </plugins>
          </build>
    
          <repositories>
              <repository>
                  <id>spring-snapshots</id>
                  <name>Spring Snapshots</name>
                  <url>https://repo.spring.io/snapshot</url>
                  <snapshots>
                      <enabled>true</enabled>
                  </snapshots>
              </repository>
              <repository>
                  <id>spring-milestones</id>
                  <name>Spring Milestones</name>
                  <url>https://repo.spring.io/milestone</url>
                  <snapshots>
                      <enabled>false</enabled>
                  </snapshots>
              </repository>
          </repositories>
      </project>
      ```
    
      ​

- 最后启动eureka server 模块，customer 模块，provider 模块，dashboard 模块

  ![1527163310350](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527163310350.png)



![1527163349636](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527163349636.png)

![1527163374481](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527163374481.png)

![1527163447389](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527163447389.png)

- 进入dashboard 管理界面，如果出现loading... ，刷新消费模块调用服务模块的url即可

  ![1527163623792](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527163623792.png)



![1527163667485](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527163667485.png)



### 9. 总结

至此，我们完成了公共模块（feign代理接口 + hystrix集中处理）+ 服务模块（hystrix标记） + eureka server（集群） + 消费模块（注入feign代理接口）+ hystrix dashboard 监控模块的整体搭建；踩的坑如实记录下来，方便以后查看。

多说一句，最开始使用IDEA 搭建这个项目的时候，本来是按照eclipse 上的模式（Maven 多模块构建的方式）进行搭建，但是始终有问题，不是配置文件读取失败，就是classNotFind。。。测试了好久，最后使用如下方式进行SP 的项目搭建

I. 新建一个空工程（顶级工程）

II. 添加模块（全部使用spring initialzr插件）

III. 公共组件等放在common 模块，其他模块如果需要可以引用这个common 模块（消费模块或者服务模块等），每个模块各自独立，而引用jar包全部放在空工程下，特别方便开发

![1527164743550](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527164743550.png)

> 最后，本节的第三点没有，因为排版失误 ^_^



## 三、其他组件添加和完善



### 1. Zull（网关组件）

![1527217349660](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527217349660.png)

#### 1.1 新建一个Zuul 模块（引入web，zuul，eureka discovery 依赖）

![1527217474855](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527217474855.png)

#### 1.2 pom.xml(亮点：添加actuator 依赖和maven-resource 处理插件，完成该模块的具体信息展示)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>zuul</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>zuul</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- actuator 监控信息完善 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <finalName>zuul</finalName>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>true</filtering>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <configuration>
                    <delimiters>
                        <delimiter>$</delimiter>
                    </delimiters>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

</project>
```

#### 1.3 yml文件

```yaml
server:
  port: 8766    # 端口设置

spring:
  application:
    name: microservicecloud-zuul-gateway                # 注册到Eureka 以及对外暴露的微服务名字（重要）

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/    # 将我们的zuul服务注册到eureka server 的地址
  instance:
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）


info:
  app.name: cris-microservicecloud
  company.name: www.cris.com
  app.programmer: zc-cris
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```

#### 1.4 主启动类

```java
@SpringBootApplication
@EnableZuulProxy            // 开启zuul 代理
@EnableEurekaClient     // 将服务注册到eureka server
public class ZuulApplication {

    public static void main(String[] args) {
        SpringApplication.run(ZuulApplication.class, args);
    }
}
```

#### 1.5 启动测试

![1527219796125](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527219796125.png)

![1527219869132](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527219869132.png)

![1527219934326](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527219934326.png)



#### 1.6 zuul 的路由功能

- 修改yml文件，定义服务访问规则

  ```yaml
  # zuul代理指定服务模块，将其对外暴露的服务名隐藏换个马甲      
  zuul:
  	prefix: /cris   # 设置公共的访问前缀
    ignored-services: "*"		# 隐藏所有服务路径
    routes:
      user.serviceId: service-provider	# 指定注册在eureka server 上的服务名
      user.path: /user/**			# 必须通过以下路径访问
  ```

  ​

![1527229502511](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527229502511.png)

![1527229625963](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527229625963.png)

**注意：zuul主要针对的是外界访问我们的微服务架构，及其进行访问控制和过滤，但是我们自己微服务架构内的各个模块之间的调用不受影响**

![1527229785108](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527229785108.png)

![1527229832683](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527229832683.png)

#### 1.7 zuul 的过滤功能

​	针对用户的请求，我们还可以使用zuul 做权限认证

- 新建一个过滤器做认证

```java
/**
 * @ClassName MyFilter
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Component
public class MyFilter extends ZuulFilter {

    // 过滤规则
    @Override
    public String filterType() {
        return "pre";
    }

    // 过滤顺序
    @Override
    public int filterOrder() {
        return 0;
    }

    // 是否需要过滤，一般开启
    @Override
    public boolean shouldFilter() {
        return true;
    }

    // 认证规则
    @Override
    public Object run() throws ZuulException {
        RequestContext currentContext = RequestContext.getCurrentContext();
        HttpServletRequest request = currentContext.getRequest();
        String token = request.getParameter("token");
        if (token == null){
            currentContext.setSendZuulResponse(false);
            currentContext.setResponseStatusCode(401);
            try {
                currentContext.getResponse().getWriter().write("token is missing!,please login or call administrator");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return null;
    }
}
```

![1527231956768](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527231956768.png)

![1527231995386](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527231995386.png)

- 注意：

  - filterType：返回一个字符串代表过滤器的类型，在zuul中定义了四种不同生命周期的过滤器类型，具体如下：

    	- pre：路由之前


    	- routing：路由之时


    	- post： 路由之后


     - error：发送错误调用

  - filterOrder：过滤的顺序，越小优先级越高

  - shouldFilter：这里可以写逻辑判断，是否要过滤

  - run：过滤器的具体逻辑。可用很复杂，包括查sql，nosql去判断该请求到底有没有权限访问。

  ​

### 2. config 分布式配置组件（结合git/github）

#### 2.1 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.cris</groupId>
	<artifactId>config-server</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>config-server</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.2.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>
</project>
```

#### 2.2 yml

```yaml
server:
  port: 8767

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/    # 将我们的zuul服务注册到eureka server 的地址
  instance:
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）

spring:
  application:
    name: microservicecloud-config
  cloud:
    config:
      server:
        git:
          uri: git@github.com:zc-cris/springcloud_server_config_demo.git   #github上的仓库名字
```

#### 2.3 主启动类

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```



#### 2.4 启动并测试

![1527234197536](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527234197536.png)



![1527233715780](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527233715780.png)

![1527233796940](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527233796940.png)

![1527233973959](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527233973959.png)

#### 2.5 测试config client 模块能否通过config server端读取到github上的配置信息

##### 1、新建一个config 版的customer 模块（引入eureka discovery，web，config client 依赖）

![1527235413600](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527235413600.png)

##### 2、pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>config-client</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>config-client</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.BUILD-SNAPSHOT</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>
```

##### 3、yml

```yaml
spring:
  cloud:
    config:
      name: microservicecloud-config-client # 需要从github上读取的资源名称，注意没有yml后缀名
      profile: dev # 本次访问的配置环境
      label: master
      uri: http://localhost:8767    #客户端微服务启动后，先去8767端口寻找服务端，通过服务端获取github的repository 地址
  application:
    name: config-client

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/    # 将我们的zuul服务注册到eureka server 的地址
  instance:
    prefer-ip-address: true   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）
server:
  port: 8768                  
```

##### 4、主启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class ConfigClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClientApplication.class, args);
    }
}
```

##### 5、测试用的controller

```java
/**
 * @ClassName ConfigClientrest
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class ConfigClientRest {

    @Value("${spring.application.name}")
    private String applicationName;

    @Value("${eureka.client.service-url.defaultZone}")
    private String eurekaServers;

    @Value("${server.port}")
    private String port;

    @GetMapping("/config")
    public String getConfig() {
        String string = "applicationName:" + applicationName + ",eurekaServers:" + eurekaServers + ",port:" + port;
        System.out.println(string);
        return string;
    }
}
```

##### 6、往github上传配置文件

![1527237875844](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527237875844.png)

##### 7. 启动测试

![1527237770084](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527237770084.png)

![1527237982388](C:\File\Typora\SpringCloud 完整案例第二季\SpringCloud 完整案例第二季.assets\1527237982388.png)



##### 8. *如果对SpringCloud 的config 组件还想有个综合的实战练习，可以参考我的第一季SC整合案例，这里就不在进行重复性操作了，只要config server 可以从github上读取配置，我们的微服务模块可以通过server 端读取到各自的配置信息（配置集中化管理），即可*







