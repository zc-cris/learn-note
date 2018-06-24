

# SpringCloud 完整案例第一季

## 一. 项目构建及准备

### 1. 环境准备

​	**构建微服务工程（Maven分包分模块）：**

1. microservicecloud-api（封装的整体entity/接口/公共配置等）
2. microservicecloud-provider-dept-8001（服务提供者） 
3. microservicecloud-consumer-dept-80（消费者）

		- SP版本：Dalston.SR1; SB版本：1.5.9；
	- 开发工具：eclipse
	- 构建工具：Maven
	- java版本：1.8.162

### 2. 构建项目（约定 > 配置 > 编码）

#### ① 构建父模块

##### I. 将eclipse 视图改为Working Sets

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1.png)

##### II. 新建一个Working Sets

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2.png)

------



![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3.png)

##### III. 构建一个Maven 父工程

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4.png)

------

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5.png)

------

![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6.png)

##### IV. 父工程的pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<!-- GAV 坐标 -->
	<groupId>com.cris.springcloud</groupId>
	<artifactId>microservicecloud</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>pom</packaging>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<!-- 保证编译java8的版本 -->
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
		<junit.version>4.12</junit.version>
		<log4j.version>1.2.17</log4j.version>
		<!-- Lombok 是一种 Java™ 实用工具，可用来帮助开发人员消除 Java 的冗长， 尤其是对于简单的 Java 对象（POJO）。它通过注解实现这一目的。 -->
		<lombok.version>1.16.18</lombok.version>
	</properties>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Dalston.SR1</version>
				<!-- 将所有的jar包打包成一个pom，依赖了pom，即可以下载下来所有依赖的jar包 -->
				<type>pom</type>
				<!-- 表示从SpringCloud 官方标准项目中导入对应的dependency配置 -->
				<scope>import</scope>
			</dependency>
			
			<dependency>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-dependencies</artifactId>
				<version>1.5.9.RELEASE</version>
			</dependency>
			
			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
				<version>5.0.4</version>
			</dependency>
			
			<dependency>
				<groupId>com.alibaba</groupId>
				<artifactId>druid</artifactId>
				<version>1.0.31</version>
			</dependency>
			
			<dependency>
				<groupId>org.mybatis.spring.boot</groupId>
				<artifactId>mybatis-spring-boot-starter</artifactId>
				<version>1.3.0</version>
			</dependency>
			
			<dependency>
				<groupId>ch.qos.logback</groupId>
				<artifactId>logback-core</artifactId>
				<version>1.2.3</version>
			</dependency>
			
			<dependency>
				<groupId>junit</groupId>
				<artifactId>junit</artifactId>
				<version>${junit.version}</version>
				<scope>test</scope>
			</dependency>
			
			<dependency>
				<groupId>log4j</groupId>
				<artifactId>log4j</artifactId>
				<version>${log4j.version}</version>
			</dependency>
			
		</dependencies>
	</dependencyManagement>
</project>
```

#### ② 构建公共子模块

##### I. 构建Maven module

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7.png)

------

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8.png)



##### II. 工程结构如下

![9](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/9.png)



##### III. 公共子模块pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<!-- 子模块需要显示声明，才能有明确的继承表现，依赖默认使用父工程定义，除非自己明确定义 -->
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<!-- 当前子模块的名字 -->
	<artifactId>microservice-api</artifactId>

	<dependencies>
		<!-- 当前Module需要用到的jar包，按自己需求添加，如果父类包含了，可以不用写版本号 -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
		</dependency>
	</dependencies>
</project>
```

此时父模块的pom.xml 需要显示如下：

```xml
	<modules>
		<module>microservice-api</module>
	</modules>
```



#### ③ 为公共子模块定义部门Entity，结合lombok 使用

##### I. [为 eclipse 安装 lombok 插件](https://blog.csdn.net/qq_32447321/article/details/60570051)

##### II. 定义公共类Dept 并结合lombok 自动生成模板代码

```java
// 使用lombok 插件和jar包自动生成模板代码
@SuppressWarnings("serial")
@AllArgsConstructor // 全参构造器
@NoArgsConstructor  // 无参构造器
@Data               // 自定生成getter和setter...
@Accessors(chain=true)  // 可以链式调用自动生成的模板方法
public class Dept implements Serializable{  // 具有类表关系映射的javaBean，必须序列化
    private Integer id;     //主键id
    private String name;    //部门名称
    private String db_source;   // 来自于哪个数据库，微服务架构中一个服务可以对应一个数据库，同一信息可以存储到不同数据库
    
    // 自定义的有参构造器
    public Dept(String name) {
        super();
        this.name = name;
    }
    
    /*  测试lombok 是否可用以及链式调用注解是否生效   ok
    public static void main(String[] args) {
        Dept dept = new Dept();
        dept.setName("人力资源部").setId(2).setDb_source("db1");
        System.out.println(dept);
    }
     */
}
```

##### III. 效果图

![10](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/10.png)

------

![11](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/11.png)

##### IV. 运行run as install 命令，将公共子模块导入maven 库；后续如果其他模块需要使用到公共模块的内容，直接在pom.xml中导入公共模块的 GAV 即可

```xml
<groupId>com.cris.springcloud</groupId>
<artifactId>microservice-api</artifactId>
<version>0.0.1-SNAPSHOT</version>
```



#### ④ 构建服务提供者模块

##### I. pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-provider-dept-8001</artifactId>

	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>

		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
		</dependency>

		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
		</dependency>

		<!-- 使用内嵌的jetty 容器 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>
		
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
		
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

##### II. 结构图

![12](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/12.png)

##### III. application.yml

```yaml
server:
  port: 8001    # 端口设置    
  
mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml    # mybatis总配置文件所在目录
  type-aliases-package: com.cris.springcloud.entity     # 所有Entity别名类所在包
  mapper-locations:
  - classpath:mybatis/mapper/**/*.xml                   # mapper映射文件所在路径
  
spring:
  application:
    name: microservicecloud-dept                        # 注册到Eureka 以及对外暴露的微服务名字（重要）              
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource        # 当前数据源操作类型（Druid）
    driver-class-name: org.gjt.mm.mysql.Driver          # mysql驱动包
    url: jdbc:mysql://localhost:3306/cloudDB01          # 连接的数据库名称
    username: root
    password: 123456
    dbcp2:
      min-idle: 5                                       # 数据库连接池的最小维持连接数
      max-total: 5                                      # 数据库连接池最大连接数
      initial-size: 5                                   # 数据库连接池初始化连接数
      max-wait-millis: 200                              # 等待获取连接的最大超时时间
```

##### IV. mybatis.cfg.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration> 
	 <settings>
	 	<setting name="cacheEnabled" value="true"/><!-- 开启二级缓存 -->
	 	<setting name="mapUnderscoreToCamelCase" value="true"/><!-- 开启驼峰命令匹配 -->
	 </settings>
</configuration>
```

##### V. 数据库脚本

```sql
DROP DATABASE IF EXISTS cloudDB01;
CREATE DATABASE cloudDB01 CHARACTER SET UTF8;
USE cloudDB01;
CREATE TABLE dept
(
	id INT PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(60),
	db_source VARCHAR(60)
);

INSERT INTO dept(name, db_source) VALUES ("人事部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("前台部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("开发部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("市场部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("运维部", DATABASE());

SELECT * FROM dept;
```

##### VI. xxxMapper 接口

```java
@Mapper
public interface DeptMapper {

    public boolean saveDept(Dept dept);
    
    public Dept getDeptById(Integer id);
    
    public List<Dept> listDepts();
    
}
```

##### VII. xxxMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cris.springcloud.dao.DeptMapper">

  <select id="getDeptById" resultType="Dept" parameterType="int">
    select id,name,db_source from dept where id = #{id};
  </select>
  
  <select id="listDepts" resultType="Dept">
  	select id,name,db_source from dept;
  </select>
  
  <insert id="saveDept" parameterType="Dept">
  	insert into dept(name, db_source) values (#{name}, DATABASE());
  </insert>
  
</mapper>
```

##### VIII. service 层

```java
public interface DeptService {

    public boolean save(Dept dept);
    
    public Dept get(Integer id);
    
    public List<Dept> list();
    
}

// 为什么service 层的方法不和dao 层的方法保持一致，主要是考虑到和controller 层的rest 风格一致
@Service
public class DeptServiceImpl implements DeptService {

    @Autowired
    private DeptMapper deptMapper;
    
    @Override
    public boolean save(Dept dept) {
        return deptMapper.saveDept(dept);
    }

    @Override
    public Dept get(Integer id) {
        return deptMapper.getDeptById(id);
    }

    @Override
    public List<Dept> list() {
        return deptMapper.listDepts();
    }
}
```

##### IX. controller层

```java
@RestController
public class DeptController {

    @Autowired
    private DeptService service;

    @PostMapping("/dept/save")
    public boolean save(@RequestBody Dept dept) {
        return service.save(dept);
    }
    
    @GetMapping("/dept/get/{id}")
    public Dept get(@PathVariable("id") Integer id) {
        return service.get(id);
    }
    
    @GetMapping("/dept/list")
    public List<Dept> list(){
        return service.list();
    } 
}
```

##### X. 主程序类和测试类

- 测试类

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest
  public class SpringCloudTest {

      @Autowired
      DeptMapper mapper;
      
      @Autowired
      DeptService service;
      
      @Autowired
      DataSource source;
      
      @Test
      public void contextLoads() throws SQLException {
  //        System.out.println(mapper);
  //        System.out.println(service);
          System.out.println(source.getClass());
          System.out.println(source.getConnection());
      }
  }
  ```

- 主程序类

  ```java
  @SpringBootApplication
  public class DeptProvider8001_App {

      public static void main(String[] args) {
          SpringApplication.run(DeptProvider8001_App.class, args);
      }
  }
  ```

- 测试成功，最后启动模块如图

  ![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526474344215.png)

  ------

  ![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526474374429.png)

#### ⑤ 构建消费者模块

##### I. 结构图

![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526475374716.png)

##### II. pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-consumer-dept-80</artifactId>

	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

##### III. application.yml

```yaml
server:
  port: 80    # 端口设置    
```

##### IV. 注入RestTemplate

```java
@Configuration              // @Configuration == applicationContext.xml
public class BeanConfig {
    
    // 往容器里面放入一个专门用于发送rest请求的模板类
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

##### V. controller层

```java
@RestController
public class DeptController_Consumer {
    
    @Autowired
    private RestTemplate restTemplate;
    
    private static final String REST_URL_PREFIX = "http://localhost:8001";
    
    @PostMapping("/consumer/dept/save")
    // 这里如果不用@RequestBody注解，那么前台发送过来的数据不能是json 格式，否则无法解析
    public boolean save(Dept dept) {
        return restTemplate.postForObject(REST_URL_PREFIX + "/dept/save", dept, Boolean.class);
    }
    
    @GetMapping("/consumer/dept/get/{id}")
    public Dept get(@PathVariable("id") Integer id) {
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/get/" + id, Dept.class);
    }
    
    @SuppressWarnings("unchecked")
    @GetMapping("/consumer/dept/list")
    public List<Dept> list(){
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/list", List.class);
    }
}
```

##### VI. 主程序类

```java
@SpringBootApplication
public class DeptConsumer80_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer80_App.class, args);
    }
}
```

##### VII. 启动并测试（通过访问消费者模块的url 请求能否获取到服务者提供的数据）

- [chrome 格式化json 插件](https://www.cnblogs.com/iyangyuan/p/5064810.html)

  ![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526486035327.png)

- ==关于访问 localhost:8001/druid 无法出现druid 数据源管理页面的情况是因为还有druid 数据源的配置类没有配，详情可以参考我的另外一篇整合[demo](https://github.com/zc-cris/springboot-data-jdbc)==


#### ⑥ 引入 Swagger2 测试模块（亮点）

- 为了更加方便后台开发人员对自己的接口进行测试，这里引入了Swagger2 测试模块，更加方便的一站式测试流程大大提高了测试效率和准确性（如果使用postman 测试也可以，但是感觉没有Swagger2 来的方便）

- 父工程pom.xml

  ```xml
  		<!-- 引入swagger 依赖 -->
  			<dependency>
  				<groupId>io.springfox</groupId>
  				<artifactId>springfox-swagger2</artifactId>
  				<version>2.8.0</version>
  			</dependency>
  			<!-- 引入swagger ui 方便页面测试 -->
  			<dependency>
  				<groupId>io.springfox</groupId>
  				<artifactId>springfox-swagger-ui</artifactId>
  				<version>2.8.0</version>
  			</dependency>
  ```

- 子模块pom.xml

  ```xml
  		<!-- 引入swagger 依赖 -->
  		<dependency>
  			<groupId>io.springfox</groupId>
  			<artifactId>springfox-swagger2</artifactId>
  		</dependency>
  		<!-- 引入swagger ui 方便页面测试 -->
  		<dependency>
  			<groupId>io.springfox</groupId>
  			<artifactId>springfox-swagger-ui</artifactId>
  		</dependency>
  ```

- 主启动类

  ```java
  @EnableSwagger2
  @SpringBootApplication
  public class DeptProvider8001_App {

      public static void main(String[] args) {
          SpringApplication.run(DeptProvider8001_App.class, args);
      }
  }
  ```

  ​

- 测试效果图

  ![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526527128433.png)

  ------

  ​

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526527438895.png)

- 消费者模块的Swagger2 测试模块同上

  ![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526528609049.png)

## 二. Eureka 服务注册与发现

### 1. Eureka 简介

**Eureka 类似于Zookeeper，是SpringCloud 微服务架构中非常重要的一环，即服务注册发现中心，采用C-S架构，Eureka Server 和Eureka Client（服务模块客户端）；Eureka Server 提供服务注册功能，每个服务模块启动后，会在Eureka Server 中进行注册，并且显示在服务注册列表中，我们可以通过Eureka 管理界面进行方便的管理**

### 2. Eureka 技术架构中的三大角色

1. Eureka Server：提供服务注册和发现
2. Service Provider：服务提供方将自身服务注册到Eureka，方便消费者使用服务
3. Service Consumer：消费者从Eureka 获取注册服务列表，找到指定的服务提供者并消费服务

### 3. Eureka 服务模块构建

#### ① 结构图

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526488120690.png)

#### ② pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-eureka-7001</artifactId>

	<dependencies>
		<!-- Eureka-Server 服务端 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
		</dependency>
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

#### ③ application.yml

```yaml
server:
  port: 7001    # 端口设置    
  
eureka:
  instance:
    hostname: localhost   # eureka 服务端的实例名称
  client:
    register-with-eureka: false   # 不向注册中心注册自己
    fetch-registry: false   # 不需要去检索服务，自己就是服务中心，职责是维护服务
    service-url:
      dufaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址
```

#### ④ 主启动类

```java
@SpringBootApplication
@EnableEurekaServer     // 表明这是一个Eureka Server启动类
public class EurekaServer7001_App {

    
    public static void main(String[] args) {
        SpringApplication.run(EurekaServer7001_App.class, args);
    }
}
```

#### ⑤ 启动Eureka 模块

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526489515363.png)



### 4. 注册服务模块进Eureka

#### ① 服务模块pom.xml

```xml
		<!-- 将服务模块注册进Eureka -->
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
```

#### ② 服务模块 application.yml

```yaml
eureka:
  client: # eureka 客户端注册进eureka 服务列表内
    service-url:
      defaultZone: http://localhost:7001/eureka  #我们自己的服务中心地址
```

#### ③ 主启动类

```java
@EnableEurekaClient // 这个服务模块启动后自动注册进Eureka Server
@EnableSwagger2
@SpringBootApplication
public class DeptProvider8001_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptProvider8001_App.class, args);
    }
}
```

#### ④ 启动并测试

- 先启动eureka 模块，再启动服务模块

- 效果图

  ![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526538753891.png)

	​	

### 5. actuator与微服务信息完善（追求细节的完美）

#### ① 主机名称:服务名称修改(见②)

#### ② 访问信息的ip信息显示

1. application.yml

```yaml
eureka:
  client: # eureka 客户端注册进eureka 服务列表内
    service-url:
      defaultZone: http://localhost:7001/eureka  #我们自己的服务中心地址
  instance:
    instance-id: microservicecloud-dept8001   # 显示在服务中心界面的服务名称（简洁明了）
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）
```

2. 改进后的效果图

   ![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526540389639.png)



#### ③ 微服务info 内容详细显示

##### I. 服务模块pom.xml

```xml
		<!-- actuator 监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
```

##### II. 父工程的pom.xml

```xml
	<build>
		<finalName>microservicecloud</finalName>
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
		</plugins>
	</build>
```

##### III. 服务模块的application.yml

```yaml
info:
  app.name: cris-microservicecloud
  company.name: www.cris.com
  app.programmer: zc-cris
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```

##### IV. 测试效果

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526547296081.png)

------

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526547283661.png)

### 6. Eureka 的自我保护机制（Netflix 设计Eureka时遵循的AP原则）

#### ① 什么是Eureka 的自我保护机制？（重点）

​	**当服务节点在规定时间没有发送给Eureka Server 心跳时，Eureka Server会保护服务注册表中的信息，不会注销任何服务节点，如果服务节点发送给Eureka Server 的心跳数重新恢复到阈值以上时，该服务节点就会自动退出自我保护机制；设计理念就是：宁可保留错误的服务注册信息或者更改前的注册信息，也不盲目的注销任何可能健康的服务实例（网络堵塞造成的...）**

​	==一句话概括：某一时刻某一个微服务节点不可用时，eureka不会立即清理，依旧会对该服务节点的信息进行保留，使我们的Eureka 集群更加的健壮，稳定 ( Availability（可用性）、Partition tolerance（分区容错性）)==

#### ② 一般不建议修改Eureka 默认的自我保护机制设置

- 可以使用eureka.server.enable-self-preservation=false 来禁用自我保护机制

### 7. 服务模块对外暴露服务发现（提供服务信息）

#### ① controller 

```java
    // 对外暴露提供服务信息的客户端，方便服务发现
    @Autowired
    private DiscoveryClient discoveryClient;
    
    @GetMapping("/dept/discovery")
    public Object discovery() {
        List<String> services = discoveryClient.getServices();
        System.out.println("-----------" + services);
        services.forEach(System.out::println);
        
        List<ServiceInstance> instances = discoveryClient.getInstances("MICROSERVICECLOUD-DEPT");
        for (ServiceInstance serviceInstance : instances) {
            System.out.println(serviceInstance.getServiceId()+"\t"+serviceInstance.getHost()+"\t"+serviceInstance.getPort()+"\t"+serviceInstance.getUri());
        }
        return this.discoveryClient;
        
    }
```

#### ② 主启动类

```java
@EnableEurekaClient // 这个服务模块启动后自动注册进Eureka Server
@EnableSwagger2
@SpringBootApplication
@EnableDiscoveryClient  //启动服务发现
public class DeptProvider8001_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptProvider8001_App.class, args);
    }
}
```

#### ③ 测试如下

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526550230185.png)

------

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526550324502.png)

#### ④ 消费模块测试服务模块的服务信息接口

- controller

```java
    @GetMapping("/consumer/dept/discovery")
    public Object discovery() {
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/discovery", Object.class);
    }
```

- 效果图

  ![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526555763931.png)



### 8. Eureka 集群配置（重点）

#### ① 构建两个Eureka 服务模块，效仿我们的第一个Eureka 服务模块即可

- pom.xml 相同

- 主启动类

  ```java
  @SpringBootApplication
  @EnableEurekaServer     // 表明这是一个Eureka Server启动类
  public class EurekaServer7003_App {
      
      public static void main(String[] args) {
          SpringApplication.run(EurekaServer7003_App.class, args);
      }
  }

  @SpringBootApplication
  @EnableEurekaServer     // 表明这是一个Eureka Server启动类
  public class EurekaServer7002_App {
    
      public static void main(String[] args) {
          SpringApplication.run(EurekaServer7002_App.class, args);
      }
  }
  ```

#### ② 集群域名映射修改

##### I. 修改host 文件(添加指定的域名别名)

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526556899623.png)

------

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526556910160.png)



##### II. 3台eureka 服务器的application.yml 配置

```yaml
7001端口服务器
server:
  port: 7001    # 端口设置    
  
eureka:
  instance:
    hostname: eureka7001.com   # eureka 服务端的实例名称,单机版为localhost
  client:
    register-with-eureka: false   # 不向注册中心注册自己
    fetch-registry: false   # 不需要去检索服务，自己就是服务中心，职责是维护服务
    service-url:   
    # 集群配置
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址    
      
      # 单机版配置
#      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址    

7002端口服务器
server:
  port: 7002    # 端口设置    
  
eureka:
  instance:
    hostname: eureka7002.com   # eureka 服务端的实例名称,单机版为localhost
  client:
    register-with-eureka: false   # 不向注册中心注册自己
    fetch-registry: false   # 不需要去检索服务，自己就是服务中心，职责是维护服务
    service-url:   
    # 集群配置
      defaultZone: http://eureka7003.com:7003/eureka/,http://eureka7001.com:7001/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址    
      
      # 单机版配置
#      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址    

7003号端口服务器
server:
  port: 7003    # 端口设置    
  
eureka:
  instance:
    hostname: eureka7003.com   # eureka 服务端的实例名称,单机版为localhost
  client:
    register-with-eureka: false   # 不向注册中心注册自己
    fetch-registry: false   # 不需要去检索服务，自己就是服务中心，职责是维护服务
    service-url:   
    # 集群配置
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7001.com:7001/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址    
      
      # 单机版配置
#      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  # 设置与Eureka Server交互的地址查询查询和服务注册都需要依赖这个地址    
```

##### III. 服务模块的application.yml 修改

```yaml
#     defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7001.com:7001/eureka/,http://eureka7001.com:7001/eureka/  #我们自己的服务中心地址
```

##### IV. 效果图

![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6-1526568813135.png)

------

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526568822793.png)

------

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526568833110.png)

- **==注意：最开始测试Eureka 集群的时候，总是显示无法找到集群列表信息，即配置的eureka7001.com Eureka Server 无法找到eureka7002.com 和 eureka7003.com 这两台Eureka Server，特别是控制台还老是注册名为localhost  的Eureka Server，后来debug 了好久，发现是yml文件中的 defaultZone 写成了dufaultZone。。。简直无语了==**
- 以后的任何配置，能粘贴就粘贴，能自动提示就使用自动提示，千万不要再手写了

### 9. Eureka 和 Zookeeper 对比（绝对重点）

#### ① CAP

- RDBMS(mysql/oracle/sqlServer) --> ACID理论
  - Atomicity：原子性
  - Consistency：一致性
  - Isolation：独立性
  - Durability：持久性
- NOSQL(Redis/mongdb) --> CAP理论
  - Consistency：强一致性
  - Availability：可用性
  - Partition tolerance：分区容错性
- CAP理论只能三选二，所以传统型数据库都是CA；而分布式系统，必须满足P特性，所以通常是AP或者CP（NOSQL）


#### ② Eureka 比 Zookeeper 好在哪里？

##### I. Zookeeper 保证的是CP

> 当向注册中心查询服务列表时,我们可以容忍注册中心返回的是几分钟以前的注册信息,但不能接受服务直接down掉不可用。也就是说,服务注册功能对可用性的要求要高于一致性。但是zk会出现这样一种情况,当 master节点因为网络故障与其他节点失去联系时,剩余节点会重新进行 eader选举。问题在于,选举 pleader的时间太长,30~120s,且选举期间整个zk集群都是不可用的,这就导致在选举期间注册服务瘫痪。
>
> **在云部罟的环境下,因网络问题使得zk集群失去 master节点是较大概率会发生的事,虽然服务能够最终恢复,但是漫长的选举时间导致的注册长期不可用是不能容忍的**

##### II. Eureka 保证的是AP

> Eureka看明白了这一点,因此在设计时就优先保证可用性。 Eureka各个节点都是平等的,几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而 Eureka的客户端在向某个 Eureka注册或时如果发现连接失败,则会自动切换至其它节点,只要有一台 Eureka还在,就能保证注册服务可用(保证可用性),只不过查到的信息可能不是最新的(不保证强致性)。除此之外, Eureka还有一种自我保护机制,如果在15分钟内超过85%的节点都没有正常的心跳,那么 Eureka就认为客户端与注册中心出现了网络故障,此时会出现以下几种情况
>
> 1. Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
>
> 2. Eureka仍然能够接受新服务的注册和查询请求,但是不会被同步到其它节点上(即保证当前节点依然可用)
> 3. 当网络稳定时,当前实例新的注册信息会被同步到其它节点中
>
> **因此, Eureka可以很好的应对因网络故障导致部分节点失去联系的情况,而不会像 zookeeper那样使整个注册服务瘫痪**



## 三. Ribbon 负载均衡

### 1. 什么是Ribbon？

> Spring Cloud Ribbon是基于 Netflix Ribbon实现的一套==客户端负载均衡==的工具，简单的说, Ribbon是Netfⅸx发布的开源项目,主要功能是提供客户端的软件负载均衡算法,将 Netflix的中间层服务连接在一起。
>
> Ribbon客户端组件提供一系列完善的配置项如连接超时,重试等。简单的说,就是在配置文件中列出 Load Ba| ancer(简称LB)后面所有的机器, Ribbon会自动的帮助你基于某种规则(如简单轮询,随机连接等)去连接这些机器。我们也很容易使用 Ribbon实现自定义的负载均衡算法

### 2. LB（load balance）

> LB,即负载均衡 Load Balance),在微服务或分布式集群中经常用的一种应用。
> 负载均衡简单的说就是将用户的请求平摊的分配到多个服务上,从而达到系统的HA
> 常见的负载均衡有软件 Nginx,LVS,硬件F5等
> 相应的在中间件,例如: dubbo和 Spring Cloud中均给我们提供了负载均衡, ==Spring Cloud的负载均衡算法可以自定义==

#### ① 集中式LB

​	**即在服务的消费方和提供方之间使用独立的LB设施（可以是硬件，如F5，也可以是软件，如Nginx），由该设施负责把访问请求通过某种策略转发至服务的提供方**

#### ② 进程内LB

​	**将LB 逻辑集成到消费方，消费方从服务中心获取有哪些地址可以使用，自己从这些可用的服务地址选择一个合适的服务器，Ribbon 就属于进程内LB，只是一个类库，集成在消费者进程，消费方通过它来获取到服务地址**

### 3. Ribbon 配置

#### ① 消费模块pom.xml

```xml
		<!-- Ribbon相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
```

#### ② 消费模块application.yml

```yaml
eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  
```

#### ③ 消费者模块开启负载均衡注解

```java
@Configuration              // @Configuration == applicationContext.xml
public class BeanConfig {
    
    // 往容器里面放入一个专门用于发送rest请求的模板类
    @Bean
    @LoadBalanced   // Spring Cloud Ribbon 是基于Netflix Ribbon 实现的一套客户端 负载均衡的工具
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

#### ④ 消费者模块的主启动类

```java
@EnableSwagger2
@SpringBootApplication
@EnableEurekaClient     // Eureka 服务的客户端
public class DeptConsumer80_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer80_App.class, args);
    }
}
```

#### ⑤ 消费者模块的controller修改

```java
	// 消费者模块通过直接访问服务模块的地址进行服务消费，实际中不可能这样，都会通过Eureka Server集群和 负载均衡机制来调用服务模块的服务
//    private static final String REST_URL_PREFIX = "http://localhost:8001";
    // 通过微服务提供模块的微服务名字来进行访问（使用Ribbon 负载均衡和Eureka 集群）
    private static final String REST_URL_PREFIX = "http://MICROSERVICECLOUD-DEPT";  
```

#### ⑥ 测试

- 首先开启三个Eureka Server ，然后开启服务模块，最后开启消费模块

  ![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526647549415.png)

  **==总结：==**

  ​	**Ribbon 和Eureka 整合后，Consumer 可以直接根据微服务名称调用微服务模块的的服务而不用再关心地址和端口号了**

### 4. 构建多消费者模块（消费者模块集群）

#### ① 再构建两个消费者模块

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526648583376.png)

#### ② 新建消费者模块配置(以8003为例)

##### I. pom.xml 同原消费者模块

##### II. application.yml

![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526649274135.png)

##### III. 主启动类修改

```java
@EnableEurekaClient // 这个服务模块启动后自动注册进Eureka Server
@EnableSwagger2
@SpringBootApplication
@EnableDiscoveryClient  //启动服务发现
public class DeptProvider8003_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptProvider8003_App.class, args);
    }
}
```

##### IV. 数据库脚本

```sql
DROP DATABASE IF EXISTS cloudDB03;
CREATE DATABASE cloudDB03 CHARACTER SET UTF8;
USE cloudDB03;
CREATE TABLE dept
(
	id INT PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(60),
	db_source VARCHAR(60)
);

INSERT INTO dept(name, db_source) VALUES ("人事部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("前台部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("开发部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("市场部", DATABASE());
INSERT INTO dept(name, db_source) VALUES ("运维部", DATABASE());

SELECT * FROM dept;
```

- 效果图如下

  ![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526648998570.png)

  ​

#### ③ 启动Eureka Server 集群和微服务集群并测试

- 先启动我们的Eureka Server集群，然后启动微服务集群

  ![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6-1526649897490.png)

- 然后启动消费者模块

  ![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526650101271.png)

  ------

  ![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526650112032.png)

  ------

  ![9](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/9-1526650119861.png)



- **==可以发现，Ribbon 默认使用的是轮询算法==**

### 5. Ribbon 搭配 Eureka Server 架构图

![10](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/10-1526650242787.png)

### 6.Ribbon 核心组件IRule

#### ① Ribbon 工作流程

> Ribbon在工作时分成两步
> 第一步先选择 Eurekaserver,它优先选择在同一个区域内负载较少的 server
> 第二步再根据用户指定的策略,在从 server取到的服务注册列表中选择一个地址。
> 其中 Ribbon提供了多种策略:比如轮询、随机和根据响应时间加权等

#### ② 使用随机算法替代默认的轮询(消费模块)

```java
@Configuration              // @Configuration == applicationContext.xml
public class BeanConfig {
    
    // 往容器里面放入一个专门用于发送rest请求的模板类
    @Bean
    @LoadBalanced   // Spring Cloud Ribbon 是基于Netflix Ribbon 实现的一套客户端 负载均衡的工具
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    
    @Bean
    public IRule myRule() {
        return new RandomRule();    // 用我们自己选择的随机算法替代默认的轮询算法
    }
}
```

- 效果图

![11](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/11-1526651657137.png)

### 7. Ribbon 的七种负载均衡算法

![12](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/12-1526651837033.png)

- RetryRule 算法也是比较常用的（获取失败的服务自动剔除，转而访问可用服务模块）

### 8. 自定义Ribbon 的算法

#### ① 消费模块的主启动类

```java
@EnableSwagger2
@SpringBootApplication
@EnableEurekaClient     // Eureka 服务的客户端
// 针对指定访问的服务模块加载我们自定义的Ribbon 配置类
@RibbonClient(name = "MICROSERVICECLOUD-DEPT", configuration = MyRule.class)
public class DeptConsumer80_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer80_App.class, args);
    }
}
```

#### ② 配置注意

![13](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/13.png)

#### ③ 针对指定的服务模块自定义Ribbon 提供的负载均衡算法

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526653131938.png)

#### ④ 深度自定化负载均衡算法

##### I. 算法需求

按照顺序，每台服务模块都被访问5次才切换到下一个服务模块

##### II. 修改RandomRule 的源码

```java
public class MyRandomRule extends AbstractLoadBalancerRule {
    
  /*  Random rand;

    public RandomRule() {
        rand = new Random();
    }*/

    // 当前服务模块的轮次
    private Integer currentCount = 0;
    // 调用当前服务模块的次数
    private Integer totalCount = 5;
    
    public Server choose(ILoadBalancer lb, Object key) {
        if (lb == null) {
            return null;
        }
        Server server = null;

        while (server == null) {
            if (Thread.interrupted()) {
                return null;
            }
            List<Server> upList = lb.getReachableServers();
            List<Server> allList = lb.getAllServers();

            int serverCount = allList.size();
            if (serverCount == 0) {
                /*
                 * No servers. End regardless of pass, because subsequent passes only get more
                 * restrictive.
                 */
                return null;
            }
            
            /*int index = rand.nextInt(serverCount);
            server = upList.get(index);*/
            
            // 算法逻辑
            if(totalCount > 0) {
                server = upList.get(currentCount);
                totalCount --;
            }else {
                totalCount = 5;
                ++currentCount;
                if (currentCount >= upList.size()) {
                    currentCount = 0;
                }
//                server = allList.get(currentCount);	// 这里不能再调用服务模块，否则就会调用6次，因为是在while循环中（多线程）
            }       

            if (server == null) {
                /*
                 * The only time this should happen is if the server list were somehow trimmed.
                 * This is a transient condition. Retry after yielding.
                 */
                Thread.yield();
                continue;
            }

            if (server.isAlive()) {
                return (server);
            }

            // Shouldn't actually happen.. but must be transient or a bug.
            server = null;
            Thread.yield();
        }

        return server;
    }

    @Override
    public Server choose(Object key) {
        return choose(getLoadBalancer(), key);
    }

    @Override
    public void initWithNiwsConfig(IClientConfig clientConfig) {
        // TODO Auto-generated method stub

    }
}
```

##### III. 修改自定义的Ribbon 配置类

```java
@Configuration
public class MyRule {
    
    @Bean
    public IRule rule() {
//        return new RandomRule();
        return new MyRandomRule();
    }
}
```

##### IV. 效果图

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526656039847.png)





## 四. Feign 负载均衡 

### 1. 什么是Feign

> 官网解释：
>
> Feign 是一个声明式的Web服务客户端，使得编写Web服务客户端变得非常容易；只需要创建一个接口并添加相应注解即可，同时支持JAX-RS 标准的注解。Feign 也支持可插拔式的编码器和解码器，Spring Cloud 对Feign进行了封装，使其支持了Spring MVC 标准注解和HttpMessageConverters。Feign可以和Eureka 和Ribbon 组合使用以支持负载均衡

### 2. Feign 出现的目的

​	**之前我们的消费模块都是通过微服务的地址（后来进化为微服务名称）来完成服务模块的调用，这其实就是面向微服务编程；但是为了保持我们惯有的面向接口编程，我们希望通过接口+注解的形式调用微服务，所以Feign就诞生了，只是将通过微服务名称调用服务模块改为了通过接口+注解的方式来调用服务模块**

### 3. Feign 带来的好处

> Feign旨在使编写 java Http客户端变得更容易
> 	前面在使用 Ribbon+ Resttemplate时,利用 Resttemplate对http请求的封装处理,形成了一套模版化的调用方法。但是在实际开发中往往一个接口会被多处调用,所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以, Feign在此基础上做了进一步封装,由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下,我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口上面标注 Mapper注解，现在是一个微服务接口上面标注一个 Feign注解即可)，即可完成对服务提供方的接口绑定,简化了使用 Spring cloud Ribbon 时，自动封装服务调用客户端的开发量。

### 4. 使用Feign流程

#### ① 创建一个消费模块（使用Feign）

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526690607306.png)

#### ② 修改pom.xml(添加对feign的支持)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-consumer-dept-feign</artifactId>


	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
		<!-- 引入swagger 依赖 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
		</dependency>
		<!-- 引入swagger ui 方便页面测试 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
		</dependency>

		<!-- Feign相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-feign</artifactId>
		</dependency>
		
		<!-- Ribbon相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
	</dependencies>
</project>
```

#### ③ 修改主启动类

```java
@EnableSwagger2
@SpringBootApplication
public class DeptConsumer80_Feign_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer80_Feign_App.class, args);
    }
}
```

### 5. 修改公共模块（添加服务模块的Feign接口）

#### ① 修改pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<!-- 子模块需要显示声明，才能有明确的继承表现，依赖默认使用父工程定义，除非自己明确定义 -->
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<!-- 当前子模块的名字 -->
	<artifactId>microservice-api</artifactId>

	<dependencies>
		<!-- 当前Module需要用到的jar包，按自己需求添加，如果父类包含了，可以不用写版本号 -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
		</dependency>

		<!-- Feign相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-feign</artifactId>
		</dependency>
	</dependencies>

</project>
```

#### ② 创建我们的Feign 服务接口

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526691380413.png)

```java
@FeignClient(value = "MICROSERVICECLOUD-DEPT")
public interface DeptClientService {
    
    @PostMapping("/dept/save")
    public boolean save(Dept dept);
    
    @GetMapping("/dept/get/{id}")
    public Dept get(@PathVariable("id") Integer id);
    
    @GetMapping("/dept/list")
    public List<Dept> list();

}
```

- 就是在公共模块定义我们服务模块的Service接口，但是方法上需要使用SpringMVC 的注解以及使用Feign 的注解来表明具体是哪个服务模块的客户端接口

#### ③ mvn install

### 6. Feign 消费模块

#### ① controller层

```java
@RestController
public class DeptController_Consumer {
    
    @Autowired
    private DeptClientService service;
    
//    private static final String REST_URL_PREFIX = "http://localhost:8001";
    // 通过微服务提供模块的微服务名字来进行访问（使用Ribbon 负载均衡和Eureka 集群）
//    private static final String REST_URL_PREFIX = "http://MICROSERVICECLOUD-DEPT";  
    
    
    @PostMapping("/consumer/dept/save")
 // 这里如果不用@RequestBody注解，那么前台发送过来的数据不能是json 格式，否则无法解析
    public boolean save(Dept dept) {
//        return restTemplate.postForObject(REST_URL_PREFIX + "/dept/save", dept, Boolean.class);
        return service.save(dept);
    }
    
    @GetMapping("/consumer/dept/get/{id}")
    public Dept get(@PathVariable("id") Integer id) {
//        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/get/" + id, Dept.class);
        return service.get(id);
    }
    
    @SuppressWarnings("unchecked")
    @GetMapping("/consumer/dept/list")
    public List<Dept> list(){
//        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/list", List.class);
        return service.list();
    }
}
```

#### ② 修改主启动类

```java
@EnableSwagger2
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients(basePackages = {"com.cris.springcloud"})
@ComponentScan("com.cris.springcloud")
public class DeptConsumer80_Feign_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer80_Feign_App.class, args);
    }
}
```

#### ③ 注释掉我们自定义的负载均衡算法

```java
@Configuration              // @Configuration == applicationContext.xml
public class BeanConfig {
    
    // 往容器里面放入一个专门用于发送rest请求的模板类
    @Bean
    @LoadBalanced   // Spring Cloud Ribbon 是基于Netflix Ribbon 实现的一套客户端 负载均衡的工具
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    
   /* @Bean
    public IRule myRule() {
        return new RandomRule();    // 用我们自己选择的随机算法替代默认的轮询算法
//        return new RetryRule();
    }*/
}
```

### 7. 测试

- 启动三个eureka server 集群

- 启动三个微服务8001/8002/8003

- 启动Feign 消费者模块

- 测试

  ![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526694605815.png)

### 8. Feign 如何实现负载均衡的？

> ==Feign集成了 Ribbon==
> 利用 Ribbon维护了 Microservicecloud-Dept
> 的服务列表信息,并且通过轮询实现了客户端的负载均衡。而与 Ribbon不同的是,通过 feign只需要定义服务绑定接口且以声明式的方法,优雅而简单的实现了服务调用

### 9. 总结

​	**Feign通过接口的方法调用Rest服务(之前是 Ribbon+ Resttemplate)，Feign从eureka 服务器根据服务名找到对应的服务接口,由于在进行服务调用的时候融合了 Ribbon技术,所以也支持负载均街作用**

### 10. 使用Feign 和使用Ribbon + RestTemplate的对比

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526696264253.png)

------

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526696045446.png)

- ==Feign 组件改变了我们调用微服务接口的方式，更加优雅简洁，并且整合Ribbon 实现负载均衡，可以很好的实现服务接口公用（任何想要使用服务模块的消费模块只需要注入Feign 服务接口即可），强力推荐！==



## 五. Hystrix 断路器

### 1. 分布式系统中的服务雪崩效应

> 多个微服务之间调用的时候,假设微服务A调用微服务B和微服务C,微服务B和微服务C又调用其它的微服务,这就是所谓的“扇出“，如果扇出的链路上某个微服务的调用响应时间过长或者不可用,对微服务A的调用就会占用越来越多的系统资源,进而引起系统崩溃,这就是所谓的“雪崩效应”
>
> 对于高流量的应用来说,单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是,这些应用程序还可能导致服务之间的延迟增加,备份队列,线程和其他系统资源紧张,导致整个系统发生更多的级联故障。所以需要对故障和延迟进行隔离和管理,以便单个依赖关系的失败,不能崩盘整个应用程序或系统

### 2. 什么是Hystrix

> Hystrix是个用于处理分布式系统的延迟和容错的开源库,在分布式系统里,许多依赖不可避免的会调用失败,比如超时、异常等，Hystriⅸx能够保证在一个依赖出问题的情况下,不会导致整体服务失败,避免级联故障,以提高分布式系统的弹性。
>
> “断路器”本身是种开关装置,当某个服务单元发生故障之后,通过断路器的故障监控(类似熔断保险丝),向调用方返回一个符合预期的、可处理的备选响应( Fallback),而不是长时间的等待或者抛出调用方无法处理的异常，
> 这样就保证了服务调用方的线程不会被长时间、不必要地占用,从而避免了故障在分布式系统中的曼延,乃至雪崩。

### 3. 服务熔断

> 熔断机制是应对雪崩效应的一种微服务链路保护机制。
> 当扇出链路的某个微服务不可用或者响应时间太长时,会进行服务的降级,进而熔断该节点微服务的调用,快速返回错误的响应信息。当检测到该节点微服务调用响应正常后恢复调用链路。
> 在 Sprlingcloud框架里熔断机制通过 Hystrix实现。 Hystriⅸ会监控微服务间调用的状况,当失败的调用到一定阈值,缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是@HystrixCommand

### 4. 使用Hystrix 熔断器组件

#### ① 创建一个使用Hystrix 组件的服务模块

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526698023429.png)

#### ② pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.cris.springcloud</groupId>
    <artifactId>microservicecloud</artifactId>
    <version>0.0.1-SNAPSHOT</version>
  </parent>
  <artifactId>microservicecloud-provider-hystrix-dept-8001</artifactId>


	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>

		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
		</dependency>

		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
		</dependency>

		<!-- 使用内嵌的jetty 容器 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
		
		<!-- 引入hystrix 熔断器组件 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix</artifactId>
		</dependency>

		<!-- 将服务模块注册进Eureka -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>

		<!-- actuator 监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>

		<!-- 引入swagger 依赖 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
		</dependency>
		<!-- 引入swagger ui 方便页面测试 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
		</dependency>

		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

#### ③ 修改 application.yml的instance-id

```yaml
eureka:
  client: # eureka 客户端注册进eureka 服务列表内
    service-url:
#      defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  #我们自己的服务中心地址
  instance:
    instance-id: microservicecloud-dept8001-hystrix   # 自定义显示在服务中心界面的使用了hystrix 组件的服务名称（简洁明了）
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）
```

#### ④ 修改controller 层

```java
    @GetMapping("/dept/get/{id}")
    // 一旦调用服务方法失败并抛出异常后，会自动调用@HystrixCommand 标注好的fallbackMehod 调用类中指定的方法
    @HystrixCommand(fallbackMethod = "processHystrix_Get")
    public Dept get(@PathVariable("id") Integer id) {
        Dept dept = service.get(id);
        if(dept == null) {
            throw new RuntimeException("该ID："+ id + "没有对应的记录");
        }
        return dept;
    }
    public Dept processHystrix_Get(@PathVariable("Id") Integer id) {
        return new Dept().setId(id).setName("该id对应的数据没有在数据库中找到----@HystrixCommond").setDb_source("no database in mysql");
    }
```

#### ⑤ 主启动类

```java
@EnableEurekaClient // 这个服务模块启动后自动注册进Eureka Server
@EnableSwagger2
@SpringBootApplication
@EnableDiscoveryClient  //启动服务发现
@EnableCircuitBreaker   // 对hystrix 熔断器的支持
public class DeptProvider8001_hystrix_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptProvider8001_hystrix_App.class, args);
    }
}
```

#### ⑥ 测试Hystrix

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526699682042.png)

------

![9](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/9-1526699791777.png)

### 5. 服务降级

- 一句话：整体资源的分配，忍痛将某些服务先关掉，全力支持其他服务，待度过难关，再开启回来；同时让客户端知道当前服务不可用，而不是挂起请求耗死服务器

#### ① 将业务方法和Hystrix 处理方法解耦分开（Spring 的AOP 思想）

##### I. 在公共模块中新建DeptClientServiceFallbackFactory（专门处理服务超时，异常，降级等）

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526702972241.png)

```java
@Component
public class DeptClientServiceFallbackFactory implements FallbackFactory<DeptClientService>{

    @Override
    public DeptClientService create(Throwable arg0) {
        
        return new DeptClientService() {
            
            @Override
            public boolean save(Dept dept) {
                
                return false;
            }
            
            @Override
            public List<Dept> list() {
                return null;
            }
            
            @Override
            public Dept get(Integer id) {
                // 降级处理
                return new Dept().setId(id).setDb_source(null).setName("服务当前不可用");
            }
            
            @Override
            public Object discovery() {
                
                return null;
            }
        };
    }
}
```

##### II. Feign 接口改进

```java
//@FeignClient(value = "MICROSERVICECLOUD-DEPT")
@FeignClient(value = "MICROSERVICECLOUD-DEPT", fallbackFactory = DeptClientServiceFallbackFactory.class)
public interface DeptClientService {
    
    @PostMapping("/dept/save")
    public boolean save(Dept dept);
    
    @GetMapping("/dept/get/{id}")
    public Dept get(@PathVariable("id") Integer id);
    
    @GetMapping("/dept/list")
    public List<Dept> list();
    
    @GetMapping("/dept/discovery")
    public Object discovery();
}
```

##### III. 客户端yml文件修改（服务降级主要是针对客户端）

```yaml
server:
  port: 80    # 端口设置    

feign:
  hystrix: 
      enabled: true

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```

##### IV. 测试（注意，这里需要启动调用了Feign 接口的客户端）

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526703237909.png)

- 当我们故意关闭掉服务模块时（模拟服务降级）

  ![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526703352515.png)

##### V. 将服务熔断和服务降级整合进 DeptClientServiceFallbackFactory (绝对重点)

- 公共模块的 DeptClientServiceFallbackFactory

  ```java
  @Component
  public class DeptClientServiceFallbackFactory implements FallbackFactory<DeptClientService>{

      @Override
      public DeptClientService create(Throwable arg0) {
          
          return new DeptClientService() {
              
              @Override
              public boolean save(Dept dept) {
                  
                  return false;
              }
              
              @Override
              public List<Dept> list() {
                  return null;
              }
              
              @Override
              public Dept get(Integer id) {
                 	// 测试服务熔断
                  if(arg0 instanceof RuntimeException) {
                      return new Dept().setId(id).setName("该id对应的数据没有在数据库中找到----DeptClientServiceFallbackFactory").setDb_source("no database in mysql");
                  }
                  // 测试服务降级
                  return new Dept().setId(id).setDb_source(null).setName("服务当前不可用");
              }
              
              @Override
              public Object discovery() {
                  
                  return null;
              }
          };
      }
  }
  ```

- mvn install

- 启动测试（带feign 组件的服务端和客户端）

  ![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526703934632.png)

- 测试效果

  ![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526704061244.png)

  ------

  ![12](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/12-1526704186439.png)

### 6. 服务熔断和服务降级总结

- 服务熔断

  **一般是某个服务故障或者异常引起,类似现实世界中的“保险丝“,当某个异常条件被触发,直接熔断整个服务,而不是一直等到此服务超时**

- 服务降级

  **所的降级,一般是从整体负荷考虑。就是当某个服务熔断之后,服务器将不再被调用,**
  **此时客户端可以自己准备一个本地的fa11back回调,返回一个缺省值。**
  **这样做,虽然服务水平下降,但好歹可用,比直接挂掉要强**

### 7. hystrixDashboard

#### ① 什么是hystrixDashboard

> 除了隔离依赖服务的调用以外, Hystrix还提供了准实时的调用监控( Hystrix Dashboard), Hystrix会持续地记录所有通过Hystrix发起的请求的执行信息,并以统计报表和图形的形式展示给用户,包括每秒执行多少请求多少成功,多少失败等。
> Netflix通过 hystrix- metrics- event- stream项目实现了对以上指标的监控。 Spring Cloud也提供了 Hystrix Dashboard的整合，对监控内容转化成可视化界面。

#### ② 使用流程

##### I. 创建一个hystrixDashboard 项目

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526717196853.png)

##### II. 修改pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.cris.springcloud</groupId>
    <artifactId>microservicecloud</artifactId>
    <version>0.0.1-SNAPSHOT</version>
  </parent>
  <artifactId>microservicecloud-consumer-hystrix-dashboard</artifactId>
  
	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>

		<!-- Ribbon相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		
		<!-- hystrix和hystrixDashboard -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
		</dependency>	
	</dependencies>
</project>
```

##### III. application.yml

```yaml
server:
  port: 9001    # 端口设置    
```

##### IV. 主启动类

```java
@SpringBootApplication
@EnableHystrixDashboard
public class DeptConsumer_Dystrix_Dashboard_App {

    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer_Dystrix_Dashboard_App.class, args);
    }
}
```

##### V. 所有服务模块(8001/8002/8003)的pom.xml都必须添加actuator 监控依赖

```xml
		<!-- actuator 监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
```

##### VI. 启动dashboard 模块自测

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526717745795.png)

##### VII. 完整测试dashboard 模块及介绍

- 启动模块

  ![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526718346334.png)

- 测试hystrix 能否监控8001端口

  ![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526718388630.png)

- 来到hystrix dashboard 界面

  ![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526718638015.png)

  ------

  ![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6-1526718890729.png)

  ------

  ![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526719297180.png)

- dashboard 简答介绍![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526719516082.png)

  ![9](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/9-1526719529450.png)

## 六. 路由网关（Zuul）

### 1. 什么是zuul

> zuul包含了对请求的路由和过滤两个最主要的功能:
> 其中路由功能负责将外部请求转发到具体的微服务实例上,是实现外部访问统一入口的基础
> 而过滤器功能则负责对请求的处理过程进行干预,是实现请求校验、服务聚合等功能的基础,zuu|和 Eureka进行整合,将Zuul自身注册为 Eureka服务治理下的应用
> 同时从 Eureka中获得其他微服务的消息,也即以后的访问微服务都是通过zuu跳转后获得。
> 注意:zuul服务最终还是会注册进 Eureka
>
> 提供：代理+路由+过滤三大功能

### 2. 官网图

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526720023346.png)

### 3. 使用流程（基本配置）

#### ① 创建一个zuul 模块

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526720748812.png)

#### ② 修改pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-zull-gateway-9527</artifactId>

	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>
        <!-- 引入zuul 相关依赖 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-zuul</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix</artifactId>
		</dependency>
		
		<!-- 将服务模块注册进Eureka -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		<!-- actuator 监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<!-- 使用内嵌的jetty 容器 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>

		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

#### ③ application.yml

```yaml
server:
  port: 9527    # 端口设置    
 
  
spring:
  application:
    name: microservicecloud-zuul-gateway                # 注册到Eureka 以及对外暴露的微服务名字（重要）              
      
eureka:
  client:                # eureka 客户端注册进eureka 服务列表内
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  #我们自己的服务中心地址
  instance:
    instance-id: gateway-9527.com             # 显示在服务中心界面的服务名称（简洁明了）
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）
    
info:
  app.name: cris-microservicecloud
  company.name: www.cris.com
  app.programmer: zc-cris
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```

#### ④ 修改host文件

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526721178984.png)

#### ⑤ 主启动类

```java
@SpringBootApplication
@EnableZuulProxy    // 开启zuul网关代理
public class Zull_9527_StarterSpringCloudApp {

    public static void main(String[] args) {
        SpringApplication.run(Zull_9527_StarterSpringCloudApp.class, args);
    }
}
```

#### ⑥ 启动测试

![11](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/11-1526722345944.png)

------

![9](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/9-1526722466839.png)

------

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526722480231.png)

------

![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6-1526722499963.png)



### 4. zuul 路由访问映射规则

#### ① 修改yml

```yaml
# zuul代理指定服务模块，将其对外暴露的服务名隐藏换个马甲      
zuul:
  routes:
    mydept.serviceId: microservicecloud-dept
    mydept.path: /mydept/**
```

#### ② 启动测试

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526730240419.png)

------

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526730556040.png)

------

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526730562570.png)

#### ③ 映射改进（忽略服务模块真实名）

##### I. yml 文件

```yaml
# zuul代理指定服务模块，将其对外暴露的服务名隐藏换个马甲      
zuul:
  ignored-services: microservicecloud-dept
  routes:
    mydept.serviceId: microservicecloud-dept
    mydept.path: /mydept/**
```

##### II. 测试

![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526730900274.png)

##### III. 如果想要隐藏所有服务模块的真实名称

```yaml
# zuul代理指定服务模块，将其对外暴露的服务名隐藏换个马甲      
zuul:
  ignored-services: "*"
  routes:
    mydept.serviceId: microservicecloud-dept
    mydept.path: /mydept/**
```

##### IV. 添加公共的访问前缀

```yaml
# zuul代理指定服务模块，将其对外暴露的服务名隐藏换个马甲      
zuul:
  prefix: /cris   # 设置公共的访问前缀
  ignored-services: microservicecloud-dept
  routes:
    mydept.serviceId: microservicecloud-dept
    mydept.path: /mydept/**
```

![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526731428818.png)

## 七. Spring Cloud Config 分布式配置中心

### 1. 分布式系统面临的配置问题

​	**微服务意味着要将单体应用中的业务拆分成—个个子服务,每个服务的粒度相对较小,因此系统中会出现大量的服务**
	**由于每个服务都需要必要的配置信息才能运行,所以一套集中式的、动态的配置管理设施是必不可少的。 Spring Cloud提供了Configserver来解决这个问题,我们每一个微服务自己带着一个 application. yml,上百个配置文件的管理...**

### 2. Spring Cloud Config 组件实现图

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526732097554.png)

### 3. Spring Cloud Config 是什么？

> SpringCloud Config 组件为微服务架构中的所有微服务提供集中化的配置管理，配置服务器为各个不同的微服务应用的所有运行环境提供了一个中心化的外部配置

### 4. Spring Cloud Config 原理介绍

> Spring Cloud Config分为服务端和客户端两部分。
> 服务端也称为分布式配置中心，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息,加密/解密信息等访问接口
>
> 客户端则是通过指定的配置中心来管理应用资源,以及与业务相关的配置内容,并在启动的时候从配置中心获取和加载配置信息
> 配置服务器默认采用git来存储配置信息,这样就有助于对环境配置进行版本管理,并且可以通过git客户端工具来方便的管理和访问配置内容。

### 5. Spring Cloud Config 的好处

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526732737790.png)

### 6. Spring Cloud Config 服务端配置

#### ① github 网站新建一个repository 专门用于配置信息管理

```tex
https://github.com/zc-cris/springcloud_server_config_demo.git
```

#### ② 将github 上的仓库扒到本地

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526733448375.png)

#### ③ 新建一个application.yml文件，并上传到github

```yaml
spring:
  profiles:
    active:
    - dev
---
spring:
  profiles: dev
  application:
    name: microservicecloud-config-cris-dev
---
spring:
  profiles: test
  application:
    name: microservicecloud-config-cris-test
# 一定要保存为UTF-8形式
```

------

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526733959685.png)

------

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526734089243.png)

------

![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6-1526734134703.png)

#### ④ 新建Spring Cloud Config 的服务端模块

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526741534455.png)

#### ⑤ 修改pom.xml和yml配置文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-config-3344</artifactId>

	<dependencies>

		<!-- SpringCloud Config 的server 端 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		
		<!-- 避免Config的Git插件报错：org/eclipse/jgit/api/TransportConfigCallback -->
<!-- 		<dependency>
			<groupId>org.eclipse.jgit</groupId>
			<artifactId>org.eclipse.jgit</artifactId>
			<version>4.10.0.201712302008-r</version>
		</dependency> -->

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix</artifactId>
		</dependency>
		<!-- 将服务模块注册进Eureka -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		<!-- actuator 监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<!-- 使用内嵌的jetty 容器 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

```yaml
server:
  port: 3344
  
spring:
  application:
    name: microservicecloud-config
  cloud:
    config:
      server:
        git:
          uri: git@github.com:zc-cris/springcloud_server_config_demo.git   #github上的仓库名字
```

#### ⑥ 主启动类

```java
@SpringBootApplication
@EnableConfigServer
public class Config_3344_StartSpringCloudApp {

    public static void main(String[] args) {
        SpringApplication.run(Config_3344_StartSpringCloudApp.class, args);
    }
}
```

#### ⑦ 修改hosts文件

```tex
127.0.0.1 config-3344.com
```

#### ⑧ 启动Config 模块并测试

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526742417374.png)



------

![10](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/10-1526742442130.png)

------

![9](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/9-1526742468280.png)

------

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526781275093.png)

- 至此，我们成功实现了用SpringCloud Config 通过GitHub 获取配置信息

### 7. Spring Cloud 的客户端的配置和测试

#### ① 本地git 仓库新建一个microservicecloud-config-client.yml 文件

```yaml
spring
  profiles:
    active:
    - dev

---
server:
  port: 8201
spring: 
  profiles: dev
  application: 
    name: microservicecloud-config-client
eureka:
  client:
    service-url:
      defaultZone: http://eureka-dev.com:7001/eureka/
---
server:
  port: 8202
spring:
  profiles: test
  application: 
    name: microservicecloud-config-client
eureka:
  client:
    service-url:
      defaultZone: http://eureka-test.com:7001/eureka/
```

#### ② 上传至github

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526782274315.png)

#### ③ 新建一个Spring Cloud Config 的客户端

![11](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/11-1526783120561.png)

#### ④ 新建bootstrap.yml

```yaml
spring:
  cloud:
    config:
      name: microservicecloud-config-client # 需要从github上读取的资源名称，注意没有yml后缀名
      profile: dev # 本次访问的配置环境
      label: master
      uri: http://config-3344.com:3344  #客户端微服务启动后，先去3344端口寻找服务端，通过服务端获取github的repository 地址
```

![3](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/3-1526783473411.png)

#### ⑤ application.yml

```yaml
spring:
  application:
    name: microservicecloud-config-client
```

#### ⑥ hosts

```tex
127.0.0.1 client-config.com
```

#### ⑦ 新建主启动类和测试类

```java
@SpringBootApplication
public class ConfigClient_3355_StartSpringCloudApp {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClient_3355_StartSpringCloudApp.class, args);
    }
}
```

```java
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
        String string = "applicationName:"+ applicationName +",eurekaServers:"+eurekaServers+",port:"+port;
        System.out.println(string);
        return string;
    }
}
```

#### ⑧ 测试

![4](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/4-1526785861947.png)

------

![5](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/5-1526786033751.png)

![6](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/6-1526786042978.png)



## 八. Spring Cloud Config 配置实战

### 1. 实战要求

![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526786880664.png)

### 2. 本地git仓库新建配置文件并提交

![8](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/8-1526788766512.png)



- microservicecloud-config-dept-client.yml

```yaml
 spring:
  profiles:
    active:
    - dev

---
server:
  port: 8001
spring: 
  profiles: dev
  application: 
    name: microservicecloud-config-dept-client
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource        # 当前数据源操作类型（Druid）
    driver-class-name: org.gjt.mm.mysql.Driver          # mysql驱动包
    url: jdbc:mysql://localhost:3306/cloudDB01          # 连接的数据库名称
    username: root
    password: 123456
    dbcp2:
      min-idle: 5                                       # 数据库连接池的最小维持连接数
      max-total: 5                                      # 数据库连接池最大连接数
      initial-size: 5                                   # 数据库连接池初始化连接数
      max-wait-millis: 200                              # 等待获取连接的最大超时时间
mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml    # mybatis总配置文件所在目录
  type-aliases-package: com.cris.springcloud.entity     # 所有Entity别名类所在包
  mapper-locations:
  - classpath:mybatis/mapper/**/*.xml                   # mapper映射文件所在路径

eureka:
  client: # eureka 客户端注册进eureka 服务列表内
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka
#      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  #我们自己的服务中心地址
  instance:
    instance-id: dept-8001.com   # 显示在服务中心界面的服务名称（简洁明了）
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）
    
info:
  app.name: cris-microservicecloud-springcloudconfig01
  company.name: www.cris.com
  app.programmer: zc-cris
  build.artifactId: $project.artifactId$
  build.version: $project.version$
---
server:
  port: 8001
spring: 
  profiles: test
  application: 
    name: microservicecloud-config-dept-client
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource        # 当前数据源操作类型（Druid）
    driver-class-name: org.gjt.mm.mysql.Driver          # mysql驱动包
    url: jdbc:mysql://localhost:3306/cloudDB02          # 连接的数据库名称
    username: root
    password: 123456
    dbcp2:
      min-idle: 5                                       # 数据库连接池的最小维持连接数
      max-total: 5                                      # 数据库连接池最大连接数
      initial-size: 5                                   # 数据库连接池初始化连接数
      max-wait-millis: 200                              # 等待获取连接的最大超时时间
mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml    # mybatis总配置文件所在目录
  type-aliases-package: com.cris.springcloud.entity     # 所有Entity别名类所在包
  mapper-locations:
  - classpath:mybatis/mapper/**/*.xml                   # mapper映射文件所在路径

eureka:
  client: # eureka 客户端注册进eureka 服务列表内
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka
#      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/  #我们自己的服务中心地址
  instance:
    instance-id: dept-8001.com   # 显示在服务中心界面的服务名称（简洁明了）
    prefer-ip-address: true                   #注册到服务中心使用ip进行注册（服务名称显示的ip详情）
    
info:
  app.name: cris-microservicecloud-springcloudconfig01
  company.name: www.cris.com
  app.programmer: zc-cris
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```

- microservicecloud-config-eureka-client.yml

```yaml
 spring:
  profiles:
    active:
    - dev

---
server:
  port: 7001
spring: 
  profiles: dev
  application: 
    name: microservicecloud-config-eureka-client
eureka:
  instance:
    hostname: eureka7001.com
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
---
server:
  port: 7001
spring:
  profiles: test
  application: 
    name: microservicecloud-config-eureka-client
eureka:
  instance:
    hostname: eureka7001.com
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```



### 3. 新建config 版的eureka server 模块

![12](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/12-1526789350776.png)

#### ① 修改配置和主启动类

```java
@SpringBootApplication
@EnableEurekaServer     // 表明这是一个Eureka Server启动类
public class Config_GitHub_EurekaServer7001_App {

    
    public static void main(String[] args) {
        SpringApplication.run(Config_GitHub_EurekaServer7001_App.class, args);
    }
}
```

- application.yml

```yaml
spring:
  application:
    name: microservicecloud-config-eureka-client
```

- bootstrap.yml

```yaml
spring:
  cloud:
    config:
      name: microservicecloud-config-eureka-client   # 需要从github上读取的资源名称，注意没有yml后缀名
      profile: dev # 本次访问的配置环境
      label: master
      uri: http://config-3344.com:3344  #客户端微服务启动后，先去3344端口寻找服务端，通过服务端获取github的repository 地址
```

#### ② 启动config 服务端以及config 版的eureka server 端进行测试

![14](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/14.png)

------

![13](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/13-1526789543128.png)

- 测试成功，我们的config 版的eureka server 模块成功通过config server 端成功从github 上读取到配置文件并启动成功！

### 4. 新建config 版的provider dept 模块

![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526790374303.png)

① pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.cris.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>microservicecloud-config-provider-dept-client-8001</artifactId>

	<dependencies>
		<dependency>	<!-- 引入我们自己定义的api通用包，可以使用通用包的Dept部门Entity -->
			<groupId>com.cris.springcloud</groupId>
			<artifactId>microservice-api</artifactId>
			<version>${project.version}</version>
		</dependency>
		<!-- config 的客户端 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>

		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
		</dependency>

		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
		</dependency>

		<!-- 使用内嵌的jetty 容器 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>

		<!-- 将服务模块注册进Eureka -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>

		<!-- actuator 监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>

		<!-- 引入swagger 依赖 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
		</dependency>
		<!-- 引入swagger ui 方便页面测试 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
		</dependency>

		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>
</project>
```

### ② bootsrap.yml

```yaml
spring:
  cloud:
    config:
      name: microservicecloud-config-dept-client   # 需要从github上读取的资源名称，注意没有yml后缀名
      profile: test # 本次访问的配置环境
      # profile: dev # 本次访问的配置环境
      label: master
      uri: http://config-3344.com:3344  #客户端微服务启动后，先去3344端口寻找服务端，通过服务端获取github的repository 地址
```

### ③ application.yml

```yaml
spring:
  application:
    name: microservicecloud-config-dept-client  # 必须和bootstrap.yml 文件中的name 一致
```

### ④ 主启动类

```java
@EnableEurekaClient // 这个服务模块启动后自动注册进Eureka Server
@EnableSwagger2
@SpringBootApplication
@EnableDiscoveryClient  //启动服务发现
public class Config_GitHub_DeptProvider8001_App {

    public static void main(String[] args) {
        SpringApplication.run(Config_GitHub_DeptProvider8001_App.class, args);
    }

}
```

### ⑤ dao，service，controller 层以及mapper文件同最开始的dept-8001工程，这里就略了

### ⑥ 测试

![111](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/111.png)



![123](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/123-1526790864683.png)



![1234](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1234.png)



![54](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/54.png)



### ⑦ 注意事项

> ​	事实上，以上更换环境的方法严格意义上是一种快捷的”偏门“，最正规的操作是运维工程师从本地git仓库修改配置文件上传到github，我们开发人员一般不会修改项目的配置文件，而是自动同步到github 上的配置信息即可



## 九. 总结

### 1. Spring Cloud 案例总结：

### ① 技术栈

构建工具：Maven+SpringBoot

版本控制：git/GitHub

数据库：mysql

java版本：1.8.162

ide：eclipse

持久层框架：mybatis

插件：lombok

服务注册和发现组件：Eureka（集群）

负载均衡组件：Ribbon（集成在Feign）

服务模块接口公有化组件：Feign

服务熔断和降级组件：Hystrix（结合Feign）

网关组件：Zuul

分布式配置服务组件：Spring Cloud Config 组件

### ② 案例结构图

![123](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/123-1526805579948.png)

- 案例流程图

  ![7](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/7-1526827602701.png)

### ③ 微服务架构流程图



![1](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/1-1526804871209.png)

------

### ④ 微服务分层设计思路

![2](C:\File\Typora\SpringCloud 整体实现案例\SpringCloud 整体实现案例.assets/2-1526804885153.png)