# 一. 通过 Maven 多模块构建 SSM

## 1.  构建目的

> 通过eclipse IDE 工具，以及Maven 构建工具为我们的 SSM 项目分层次的构建多个模块，通过目前普遍的  MVC 开发结构，使我们的项目搭建更加具有逻辑性以及更好的适应分工，使得每个开发人员的开发职责明确，通过版本工具使用 SVN，然后同步到 GitHub

## 2. 开发环境

> eclipse: Oxygen.3 Release (4.7.3);
>
> maven: apache-maven-3.5.0
>
> svn: VisualSVN-Server-3.8.1-x64
>
> java: jdk1.8.0_162
>
> Spring: 4.3.7.RELEASE
>
> Mybatis: 3.4.2
>
> Mysql：5.7.4
>
> 至于其它的jar 包版本这里就不一一列举了，通过 pom.xml 文件即可查看

## 3. SSM构建步骤

#### 1. Maven 设置：为Maven 的setting 文件进行如下配置

```xml
	  <mirror>  
        <id>alimaven</id>  
        <mirrorOf>central</mirrorOf>  
        <name>aliyun maven</name>  
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
    </mirror> 
	...
	<profile>
				<id>jdk18</id>
				<activation>
					<activeByDefault>true</activeByDefault>
					<jdk>1.8</jdk>
				</activation>
				<properties>
					<maven.compiler.source>1.8</maven.compiler.source>
					<maven.compiler.target>1.8</maven.compiler.target>
					<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
				</properties>
	</profile>
```

进行如上配置，主要是为了可以访问maven 的中央仓库更快（通过阿里镜像）以及统一maven 项目的jdk 编译版本

#### 2. eclipse 中进行Maven 配置

![1](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/1.png)

#### 3. 新建一个 Maven 工程作为多模块的根模块（我们后面的子模块需要依赖）

![2](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/2.png)

![3](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/3.png)



#### 4. 新建子模块

![4](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/4.png)

![5](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/5.png)

![7](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/7.png)

依次建立 common，dao，service，web四个子模块

==说明：==	common 子模块：用于工具类等

​		dao 子模块：用于实体类以及mapper文件等（如果不是使用Mybatis 可以考虑再建一个 Model 子模块专门放实体类）

​		service 子模块：用于service 层的设计

​		web 模块：用于SpringMVC 的controller 设计

**其中在建立web 子模块的时候，有两种建法：**

 1. 和上面建立普通子模块步骤一样，但是 Packaging 需要选择为 war；如果子模块建好后报错，有可能是没有web.xml，可以[参考](http://note.youdao.com/noteshare?id=424363a322bd8f5f2edfbd7f86eb3031&sub=0792FE00E7AF4C89A1113820BB1930A6)

	2.  直接建立 Maven 内置的web 项目

     ![8](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/8-1524668975436.png)

     ![9](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/9-1524668975436.png)

*个人建议使用第一种*

#### 5. 建立各个子模块之间的依赖关系以及jar 包引入

```sequence
Title: 子模块关系图
participant web
participant service
participant dao
participant common
web->service: 依赖
service->dao: 依赖
dao->common: 依赖
```



web: pom.xml

```xml
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.zc.cris</groupId>
		<artifactId>system.root</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
	<artifactId>system.web</artifactId>
	<packaging>war</packaging>
	<dependencies>

		<dependency>
			<groupId>com.zc.cris</groupId>
			<artifactId>system.service</artifactId>
			<version>${project.version}</version>
		</dependency>
	</dependencies>
```

service: pom.xml

```xml
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.zc.cris</groupId>
    <artifactId>system.root</artifactId>
    <version>0.0.1-SNAPSHOT</version>
  </parent>
  <artifactId>system.service</artifactId>
  <dependencies>
  	<dependency>
  		<groupId>com.zc.cris</groupId>
  		<artifactId>system.dao</artifactId>
  		<version>${project.version}</version>
  	</dependency>
  </dependencies>
```

dao: pom.xml

```xml
  <parent>
    <groupId>com.zc.cris</groupId>
    <artifactId>system.root</artifactId>
    <version>0.0.1-SNAPSHOT</version>
  </parent>
  <artifactId>system.dao</artifactId>
  
  <properties>
		<!-- 通用mapper设置 -->
		<!-- ${basedir}引用工程根目录 -->
		<!-- targetJavaProject：声明存放源码的目录位置 -->
		<targetJavaProject>${basedir}/src/main/java</targetJavaProject>

		<!-- targetMapperPackage：声明MBG生成XxxMapper接口后存放的package位置 -->
		<targetMapperPackage>com.zc.cris.dao.mapper</targetMapperPackage>

		<!-- targetModelPackage：声明MBG生成实体类后存放的package位置 -->
		<targetModelPackage>com.zc.cris.dao.model</targetModelPackage>

		<!-- targetResourcesProject：声明存放资源文件和XML配置文件的目录位置 -->
		<targetResourcesProject>${basedir}/src/main/resources</targetResourcesProject>

		<!-- targetXMLPackage：声明存放具体XxxMapper.xml文件的目录位置 -->
		<targetXMLPackage>mapper</targetXMLPackage>

		<!-- 通用Mapper的版本号 -->
		<mapper.version>4.0.0-beta3</mapper.version>

		<!-- MySQL驱动版本号 -->
		<mysql.version>5.1.41</mysql.version>
	</properties>
	
	<!-- 通用mapper 插件的MBG 插件设置 -->
	<build>
		<plugins>
			<plugin>
				<groupId>org.mybatis.generator</groupId>
				<artifactId>mybatis-generator-maven-plugin</artifactId>
				<version>1.3.2</version>

				<!-- 配置generatorConfig.xml配置文件的路径 -->
				<configuration>
                    <configurationFile>              		  	${basedir}/src/main/resources/generator/generatorConfig.xml
                    </configurationFile>
					<overwrite>true</overwrite>
					<verbose>true</verbose>
				</configuration>

				<!-- MBG插件的依赖信息 -->
				<dependencies>
					<dependency>
						<groupId>mysql</groupId>
						<artifactId>mysql-connector-java</artifactId>
						<version>${mysql.version}</version>
					</dependency>
					<dependency>
						<groupId>tk.mybatis</groupId>
						<artifactId>mapper</artifactId>
						<version>${mapper.version}</version>
					</dependency>
				</dependencies>
			</plugin>
		</plugins>
	</build>
  
  <dependencies>
  	<dependency>
  		<groupId>com.zc.cris</groupId>
  		<artifactId>system.common</artifactId>
  		<version>${project.version}</version>
  	</dependency>
  </dependencies>
```

common: pom.xml

```xml
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.zc.cris</groupId>
    <artifactId>system.root</artifactId>
    <version>0.0.1-SNAPSHOT</version>
  </parent>
  <artifactId>system.common</artifactId>
```

root: pom.xml(这里选择将项目的所有jar 包放在root 项目，子项目都可以统一的引用jar 包)

```xml
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.zc.cris</groupId>
	<artifactId>system.root</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>pom</packaging>
	<modules>
		<module>system.common</module>
		<module>system.dao</module>
		<module>system.service</module>
		<module>system.web</module>
	</modules>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<spring_version>4.3.7.RELEASE</spring_version>
	</properties>

	<!-- 引入项目依赖的 jar 包 -->
	<dependencies>
		<!-- springMVC -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring_version}</version>
		</dependency>

		<!-- spring JDBC 事务控制 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${spring_version}</version>
		</dependency>

		<!-- spring 面向切面工程 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aspects</artifactId>
			<version>${spring_version}</version>
		</dependency>

		<!-- SpringTest 单元测试 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring_version}</version>
			<scope>test</scope>
		</dependency>

		<!-- 引入 jackson 的jar 包，使 SpringMVC 根据注解自动解析数据为 Json 类型 -->
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.9.4</version>
		</dependency>

		<!-- JSR303 数据校验 tomcat 7 及以上服务器正常支持，但是tomcat 7 以下服务器需要为服务器的lib 包中导入新的el 
			包 -->
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-validator</artifactId>
			<version>5.4.1.Final</version>
		</dependency>


		<dependency>
			<groupId>tk.mybatis</groupId>
			<artifactId>mapper</artifactId>
			<version>4.0.0-beta3</version>
		</dependency>

		<!-- Mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.2</version>
		</dependency>

		<!-- Mybatis_Spring -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.3.1</version>
		</dependency>

		<!-- MBG -->
		<dependency>
			<groupId>org.mybatis.generator</groupId>
			<artifactId>mybatis-generator-core</artifactId>
			<version>1.3.5</version>
		</dependency>

		<!-- pagehelper 插件 -->
		<!-- <dependency> <groupId>com.github.pagehelper</groupId> <artifactId>pagehelper</artifactId> 
			<version>5.0.0</version> </dependency> -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper</artifactId>
			<version>5.1.3</version>
		</dependency>



		<!-- 数据库连接池 -->
		<dependency>
			<groupId>c3p0</groupId>
			<artifactId>c3p0</artifactId>
			<version>0.9.1</version>
		</dependency>

		<!-- mysql 驱动 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.41</version>
		</dependency>

		<!-- jstl,servlet-api,junit -->
		<dependency>
			<groupId>jstl</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>

		<!-- 为了方便 SpringTest 测试，我们需要使用 3.0 的 servlet 规范 -->
		<!-- <dependency> <groupId>javax.servlet</groupId> <artifactId>servlet-api</artifactId> 
			<version>2.5</version> <scope>provided</scope> </dependency> -->

		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>


		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>

	</dependencies>
```

至此，我们的基础jar 包就已经搭建好了，后续如果继续添加jar 包也会展示出来

#### 6. Spring，SpringMVC，Mybatis 配置文件



![1](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/1-1524670882828.png)



##### web.xml

```xml
		<!-- 启动Spring 容器 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:config/applicationContext.xml</param-value>
	</context-param>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<!-- springMVC 前端控制器，拦截所有请求 -->
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<!-- Map all requests to the DispatcherServlet for handling -->
	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<!-- 字符编码过滤器，放在所有过滤器前面 -->
	<filter>
		<filter-name>CharacterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceRequestEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
		<init-param>
			<param-name>forceResponseEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>CharacterEncodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	
	<!-- rest风格的uri 处理器 -->
	<filter>
		<filter-name>HiddenHttpMethodFilter</filter-name>
		<filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>HiddenHttpMethodFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	
	<!-- 专门用来处理 ajax 发送 PUT 类型的请求的数据转换 -->
	<filter>
		<filter-name>HttpPutFormContentFilter</filter-name>
		<filter-class>org.springframework.web.filter.HttpPutFormContentFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>HttpPutFormContentFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```

##### spring 配置文件

```xml
	<context:component-scan base-package="com.zc.cris.service">
		<!-- <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
		<context:exclude-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/> -->
	</context:component-scan>

	<!-- Spring 配置文件(数据源，与 Mybatis 的整合，事务控制) -->
	<!-- 1. 数据源 -->
	<context:property-placeholder location="classpath:config/dbconfig.properties"/>
	<bean id="pooledDataSource"  class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
		<property name="driverClass" value="${jdbc.driverClass}"></property>
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
	</bean>
	
	<!-- 2. 配置和 Mybatis 的整合 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 指定mybatis 的核心配置文件所在位置 -->
		<property name="configLocation" value="classpath:config/mybatis-config.xml"></property>
		<property name="dataSource" ref="pooledDataSource"></property>
		<property name="mapperLocations" value="classpath:com/zc/cris/dao/*/*.xml"></property>
	</bean>
	
	<!-- 配置扫描器，将 Mybatis 接口的实现加入到 ioc 容器中 -->
	<bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.zc.cris.dao"></property>
	</bean>
	
	<!-- 配置一个可以执行批量操作的 sqlSession -->
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
		<constructor-arg name="executorType" value="BATCH"></constructor-arg>
	</bean>
	
	
	<!-- 3. 事务控制器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="pooledDataSource"></property>
	</bean>
	
	<!-- 开启基于注解的事务，或者使用 xml 配置式的事务（比较重要的都是使用配置式） -->
	<aop:config>
		<!-- 切入点表达式 -->
		<aop:pointcut expression="execution(* com.zc.cris.service.*.impl..*(..))" id="txPoint"/>
		<!-- 配置事务增强 -->
		<aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/>
	</aop:config>
	<!-- 具体配置事务如何增强 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 所有方法都是事务方法 -->
			<tx:method name="*"/>
			<!-- 以get 和list 开头的方法都是只读事务，可以进行优化 -->
			<tx:method name="get*" read-only="true"/>
			<tx:method name="list*" read-only="true"/>
			<tx:method name="count*" read-only="true"/>
		</tx:attributes>
	</tx:advice>
```

##### SpringMVC 配置文件

```xml
	<!-- SpringMVC 的配置文件 -->
	<context:component-scan base-package="com.zc.cris.controller">
		<!-- 只扫描控制器 -->
		<!-- <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/> -->
	</context:component-scan>
	
	<!-- 配置视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/views/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
	
	<!-- 两个标准配置 -->
	<!-- 将springMVC 不能处理的请求交给 tomcat -->
	<mvc:default-servlet-handler/>
	<!-- 可以支持springMVC 更加高级的功能，JSR303 校验，快捷的 ajax，映射动态请求 -->
	<mvc:annotation-driven></mvc:annotation-driven>
```

##### dbconfig.properties

```properties
jdbc.jdbcUrl=jdbc:mysql:///system
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.user=root
jdbc.password=123456
```

##### mybatis-config.xml

```xml
<configuration>
	<settings>
		<!-- 开启驼峰命名对应规则 -->
		<setting name="mapUnderscoreToCamelCase" value="true"/>
	</settings>
	
	<plugins>
	    <!-- com.github.pagehelper为PageHelper类所在包名 -->
	    <plugin interceptor="com.github.pagehelper.PageInterceptor">
	    	<!-- 调整分页数据合理化 -->
	    	<property name="reasonable" value="true"/>
	    </plugin>
	</plugins>
	
</configuration> 
```

##### 引入通用Mapper 组件以及 PageHelper 组件（详见dao 子模块的pom.xml），然后配置



![2](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/2-1524671502323.png)

1. generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<!-- 引入外部属性文件 -->
	<properties resource="config.properties" />

	<context id="Mysql" targetRuntime="MyBatis3Simple"
		defaultModelType="flat">
		<property name="beginningDelimiter" value="`" />
		<property name="endingDelimiter" value="`" />

		<!-- 配置通用Mapper的MBG插件相关信息 -->
		<plugin type="${mapper.plugin}">
			<property name="mappers" value="${mapper.Mapper}" />
		</plugin>

		<!-- 配置连接数据库的基本信息 -->
		<jdbcConnection 
			driverClass="${jdbc.driverClass}"
			connectionURL="${jdbc.url}" 
			userId="${jdbc.user}" 
			password="${jdbc.password}">
		</jdbcConnection>
	
		<!-- 配置Java实体类存放位置 -->
		<javaModelGenerator 
			targetPackage="${targetModelPackage}"
			targetProject="${targetJavaProject}" />

		<!-- 配置XxxMapper.xml存放位置 -->
		<sqlMapGenerator 
			targetPackage="${targetXMLPackage}"
			targetProject="${targetResourcesProject}" />

		<!-- 配置XxxMapper.java存放位置 -->
		<javaClientGenerator 
			targetPackage="${targetMapperPackage}"
			targetProject="${targetJavaProject}" 
			type="XMLMAPPER" />

		<!-- 根据数据库表生成Java文件的相关规则 -->
		<!-- tableName="%"表示数据库中所有表都参与逆向工程，此时使用默认规则 -->
		<!-- 默认规则：table_dept→TableDept -->
		<!-- 不符合默认规则时需要使用tableName和domainObjectName两个属性明确指定 -->
		<table tableName="tb_user" domainObjectName="UserPO">
			<!-- 配置主键生成策略 -->
			<generatedKey column="id" sqlStatement="Mysql" identity="true" />
		</table>
	</context>
</generatorConfiguration>
```
2. config.properties

```properties
# Database connection information
jdbc.driverClass = com.mysql.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/system
jdbc.user = root
jdbc.password = 123456

#c3p0
jdbc.maxPoolSize=50
jdbc.minPoolSize=10
jdbc.maxStatements=100
jdbc.testConnection=true

# mapper
mapper.plugin = tk.mybatis.mapper.generator.MapperPlugin
mapper.Mapper = tk.mybatis.mapper.common.Mapper
```



#### 7. 数据库

![4](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/4-1524671735905.png)

​

#### 8. 测试 dao, service, web 模块之间的协同以及通用Mapper 插件的MBG 工程

- dao 模块的pom.xml 执行 mybatis-generator:generate 命令

  生成的mapper 接口，实体类，mapper映射如下：

  ```java
  public interface UserPOMapper extends Mapper<UserPO> {
  }

  @Table(name = "tb_user")
  public class UserPO {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Integer id;

      @Column(name = "user_name")
      private String userName;

      @Column(name = "user_age")
      private Integer userAge;

      /**
       * @return id
       */
      public Integer getId() {
          return id;
      }

      /**
       * @param id
       */
      public void setId(Integer id) {
          this.id = id;
      }

      /**
       * @return user_name
       */
      public String getUserName() {
          return userName;
      }

      /**
       * @param userName
       */
      public void setUserName(String userName) {
          this.userName = userName;
      }

      /**
       * @return user_age
       */
      public Integer getUserAge() {
          return userAge;
      }

      /**
       * @param userAge
       */
      public void setUserAge(Integer userAge) {
          this.userAge = userAge;
      }

      @Override
      public String toString() {
          return "UserPO [id=" + id + ", userName=" + userName + ", userAge=" + userAge + "]";
      }
  }

  ```

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
  <mapper namespace="com.zc.cris.dao.mapper.UserPOMapper" >
    <resultMap id="BaseResultMap" type="com.zc.cris.dao.model.UserPO" >
      <!--
        WARNING - @mbg.generated
      -->
      <id column="id" property="id" jdbcType="INTEGER" />
      <result column="user_name" property="userName" jdbcType="VARCHAR" />
      <result column="user_age" property="userAge" jdbcType="INTEGER" />
    </resultMap>
  </mapper>
  ```

  结构图如下：

  ![6](C:\File\Typora\Maven_SSM 多模块构建\Maven多模块构建SSM.assets/6.png)

​



- service 模块生成接口实现类（略），web 模块生成controller 并测试

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {

      @Autowired
      private UserService userService;
      
      @RequestMapping("/insert")
      public String insert(Model model) {
          
          UserPO user = new UserPO();
          user.setUserAge(23);;
          user.setUserName("老二爹");
          
          try {
              userService.insertUser(user);
          } catch (Exception e) {
              
              e.printStackTrace();
          }
          
          model.addAttribute("user", user);
          return "user/test";
      }
  }
  ```

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations= {"classpath:config/applicationContext.xml"})
  public class MapperTest {

      @Autowired
      UserPOMapper userPOMapper;
      @Autowired
      SqlSession sqlSession;
      @Autowired
      UserService userService;
      
      // 测试mybatis 的函数式接口 ok
      @Test
      public void test() {
          System.out.println(userPOMapper);
          System.out.println(sqlSession);
      }
      
      // 测试批处理是否可以使用  ok
      @Test
      public void testBatch() {
  //        UserDao userMapper = sqlSession.getMapper(UserDao.class);
  //        for (int i = 0; i < 3; i++) {
  //            userMapper.insertUser(new UserVO("cris"+i, i+23));
  //            
  //        }
      }
      
      // 测试通用 Mapper 插件是否可用 ok
      @Test
      public void testMapper() throws Exception {
          UserPO user = userService.getByMapper();
          System.out.println(user);
      }

  }
  ```

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @WebAppConfiguration
  @ContextConfiguration(locations = { "classpath:config/applicationContext.xml",
          "file:src/main/webapp/WEB-INF/springDispatcherServlet-servlet.xml" })
  public class MVCTest {

      // 注入 SpringMVC 的ioc 容器，必须使用 @WebAppConfiguration 注解
      @Autowired
      WebApplicationContext context;

      // 虚拟 mvc 请求，获取到处理结果
      MockMvc mockMvc;
      
      @Before
      public void initMockMvc() {
          mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
      }

      // 模拟用户请求测试controller  ok
      @Test
      public void test() throws Exception {
          MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/user/insert")).andReturn();
          MockHttpServletRequest request = result.getRequest();
          UserPO userVo = (UserPO) request.getAttribute("user");
          System.out.println(userVo);
          
      }

  }
  ```

  ==**经过测试，没有问题之后才可以进行后面的开发**==

  至此，使用 Maven 多模块构建 SSM 项目就搞定了，[源代码](https://github.com/zc-cris/Maven_SSM)

  关于svn 中的项目如何上传到 GitHub 可以参考[这篇文章](https://www.2cto.com/kf/201712/708272.html)

  ​

  ​