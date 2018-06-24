## SpringBoot 整合Mybatis和Swagger2 及部署（Docker）

### 1. 构建项目

#### ① 结构图

![1](C:\File\Typora\SpringBoot 整合Mybatis和Swagger2 及部署（Docker）\SpringBoot 整合Mybatis和Swagger2 及部署（Docker）.assets/1.png)

#### ② pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.cris</groupId>
	<artifactId>springboot-ssm-demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>springboot-ssm-demo</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.13.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<!--指定远程docker的位置;开启远程docker的2375  -->
		<dockerHost>http://120.78.138.11:2375</dockerHost>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.2</version>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

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

	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			
			<!-- docker maven打包插件；可以将应用做成docker镜像 -->
			<plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>0.4.12</version>
                <configuration>
                    <!-- 注意imageName一定要是符合正则[a-z0-9-_.]的，否则构建不会成功 -->
                    <!-- 详见：https://github.com/spotify/docker-maven-plugin    Invalid repository name ... only [a-z0-9-_.] are allowed-->
                    <imageName>springboot-ssm-demo</imageName>
                    <baseImage>java</baseImage>
                    <entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]</entryPoint>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
            </plugin>
		</plugins>
	</build>
</project>
```

#### ③ application.yml

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://120.78.138.11:3307/mybatis?useSSL=false
    driver-class-name: com.mysql.jdbc.Driver 
    
mybatis:
  config-location: classpath:/mybatis/mybatis-config.xml
```

#### ④ 主启动类

```java
/**
 *  整合Swagger 方便restful 测试 
 */
@EnableSwagger2 // 开启swagger2 的功能
@SpringBootApplication
public class SpringbootSsmDemoApplication {

    // 将应用打包成一个镜像发布在远程的docker容器中
	public static void main(String[] args) {
		SpringApplication.run(SpringbootSsmDemoApplication.class, args);
	}
}
```

#### ⑤ mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>
```

#### ⑥ javaBean

```java
public class Employee {

    private Integer id;
    private String lastName;
    private String email;
    private Integer gender;
    private Integer dId;
    ...
}
```

#### ⑦ mapper接口

```java
@Mapper
public interface EmployeeMapper {

    @Select("select * from employee where id = #{id}")
    public Employee getById(Integer id);
    
}
```

#### ⑧ controller

```java
@RestController
public class EmpController {
    
    @Autowired
    EmployeeMapper employeeMapper;
    
    @GetMapping("/emp/{id}")
    public Employee getEmp(@PathVariable("id") Integer id) {
        return employeeMapper.getById(id);
    }
}
```



### 2. 启动并测试（Swagger2）

- 启动应用，访问localhost:8080/swagger-ui.html 如果出现以下界面表示整合Swagger2 成功![2](C:\File\Typora\SpringBoot 整合Mybatis和Swagger2 及部署（Docker）\SpringBoot 整合Mybatis和Swagger2 及部署（Docker）.assets/2.png)

  ​

### 3. 部署到Docker

1、配置docker maven plugin并在properties中声明dockerHost

  <dockerHost>http://120.78.138.11:2375</dockerHost>

2、开启docker远程API操作端口2375；

vi /usr/lib/systemd/system/docker.service

将ExecStart这一行后面加上 -Htcp://0.0.0.0:2375 -H  unix:///var/run/docker.sock

3、重启docker服务；systemctl restart docker；

4、mvn clean package docker:build即可打包应用为docker镜像发布在远程服务器

5、远程服务器可以根据镜像快速启动docker容器

​	==注意：虽然工程可以成功部署到远程Docker，但是Docker 设置完并重启后就挂掉了。。。==