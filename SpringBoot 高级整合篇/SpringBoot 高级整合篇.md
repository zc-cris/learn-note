## SpringBoot 高级整合篇

### 零. 前言-总结：

```tex
SpringBoot 高级

1. 缓存（Spring缓存抽象）：@EnableCaching，@CacheConfig，@Caching，@Cacheable，@CachePut，@CacheEvict，缓存原理，自定义key的生成策略类
2. 整合redis 缓存：使用docker（连接国内的镜像）安装并开启redis，使用redis的客户端测试连接远程redis服务器是否ok，然后工程导入redis的dependency，配置文件配置redis；自定义redis的序列化规则类；
	自定义RedisCacheManager，使用自定义的序列化规则	；使用编码或者注解两种方式往redis缓存中放入数据

3. 消息中间件：JMS,AMQP,RabbitMQ
	1. 异步处理
	2. 应用解耦
	3. 流量削峰
	4. 日志处理
4. 整合RabbitMQ：docker pull registry.docker-cn.com/library/rabbitmq:3-management；docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq c51d1c73d028
	1. 进入到rabbitMQ的管理界面，添加三个不同类型的exchange（交换器）：direct,fanout,topic
	2. 创建四个消息队列（cris,cris.news,cris.emps,zc.news），并且和交换器绑定,测试交换器和队列之间通信规则
	3. SpringBoot 工程整合RabbitMQ，发送和取出rabbitmq服务器队列里消息（可以自定义json类型的数据转换器，这样发送到rabbitmq服务器的消息自动转换为json类型）
	4. 模拟应用解耦情景，使用rabbitmq的监听器注解（这里需要开启@EnableRabbit注解）
	5. 使用 编码的方式来创建exchage和queue，binding（绑定规则）

5. ElasticSearch：docker 下载elasticSearch并启动：docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES01 0ba66712c1f9
	- 基础概念：es集群，索引（google，apple），类型（Employee，product），文档（张三，牙刷），id
	- 使用restful格式的请求向elasticsearch服务器存储数据（json）put请求，获取数据（get请求），判断资源是否存在（head请求），删除数据（delete请求），更新数据（put请求）
	- 使用postman进行测试
	- _search 搜索所有员工数据；通过json格式的搜索信息进行强大的各种搜索（重点就是查询表达式）
	- springboot 整合elasticsearch
	- 两种方式操作es：1.jest 2. springdata 
	- jest客户端存储文档信息到es服务器；jest客户端通过查询表达式从es服务器获取数据
	- 通过springboot 提供的ElasticSearchRepository 接口或者ElasticSearchTemplate 类 来进行数据的检索

6.SpringBoot 和任务管理
	- 异步任务（@EnableAsync，@Async）：springboot开启另外一个线程异步处理任务
	- 定时任务：（@EnableScheduling，@Scheduled）：定时表达式
	- 邮件任务：导入starter，使用JavaMailSenderImpl 发送一封简单的邮件；发送一封复杂的邮件（引入html代码和附件）
	
7.SpringBoot 与安全
	- Shiro和Spring security
	- 搭建thymeleaf 环境和整合页面资源
	- 使用spring security 完成用户认证和授权（引入starter，编写spring security的配置类）；自定义授权规则，自定义用户认证（用户名和密码，roles）
	- 完成用户注销功能 
	- 整合thymeleaf 引擎模板为spring security 框架准备的jar 包（使用高级功能：判断用户身份显示页面信息和对应的权限模块）
	- 记住我和定制登录页（难点，重点）

8. SpringBoot 和分布式（绝对重点）
	- Docker 下载zookeeper和启动（docker run --name zk1 -p 2181:2181 --restart always -d 56d414270ae3）
	- SpringBoot 整合Dubbo（重点）；空工程里面创建两个模块（买票和用户模块）；彼此之间通过Dubbo 通信；服务提供模块引入dubbo和zookeeper，然后配置文件配置dubbo相关信息，最后发布出去
	- 消费者模块同样引入dubbo和zookeeper，同时引入服务者的接口（@Reference）进行服务的远程调用
	- 关于版本问题（SpringBoot2.x和1.5.x版本有些还是差别比较大，比如SpringBoot2.x底层使用的是Spring5.x版本，我们再写springMVC的拦截器的时候就需要将静态资源放行，而在SpringBoot1.5.x版本中
就不需要，并且通过dubbo注册服务到zookeeper中心两个大版本之间也有区别，这里使用1.5.x版本可以完成dubbo和zookeeper的整合，但是2.x版本就需要另外设置了）

9. SpringBoot 和 SpringCloud （一站式分布解决方案）：
	- 配置和启动Eureka 服务注册中心（配置文件和@EnableEurekaServer 开启服务）
	- 完成服务提供模块的配置和书写，并且使用不同的端口通过maven的package命令打包多个jar包，通过java -jar 的cmd窗口运行不同的服务模块的jar包
	- 完成消费者模块（配置文件的书写和@EnableDiscoveryClient， @LoadBalanced ，RestTemplate）

10. SpringBoot 开发热部署（Devtools）
	- 导入响应的启动器即可（并且针对java代码和html代码均有效，只需要ctrl+f9 即可，炒鸡方便，eclipse则是直接ctrl+s 保存即可）
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
        </dependency>

11. SpringBoot 监控管理（应用监控和管理功能）
	- 引入actuator启动器
	- 配置文件进行授权（management.security.enabled=false）
	- 监控端点测试
	- 定制端点信息
	- health 端点监控应用组件的健康状态（自定义组件健康状态监控器类）
```





### 一. SpringBoot 及其缓存抽象

#### 1. 引入对应的starter(web,cache,mybatis,mysql,druid)

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
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
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.6</version>
        </dependency>
    </dependencies>
```

#### 2.  application.yml

```yaml
spring:
  datasource:
#   数据源基本配置
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://120.78.138.11:3306/spring_cache?useSSL=false
    type: com.alibaba.druid.pool.DruidDataSource
#   数据源其他配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
#   配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
  redis:
    host: 120.78.138.11


# 直接将mybatis的配置写在总配置文件中（开启驼峰命名规则）
mybatis:
  configuration:
    map-underscore-to-camel-case: true

logging:
  level: debug

#    如果使用mybatis的配置文件来配置mybatis以及sql映射文件，就必须指定文件的位置
#mybatis:
#  config-location: classpath:/mybatis/mybatis-config.xml
#mapper-locations: classpath:/mybatis/mapper/*.xml

logging.level.com.cris.springboot.mapper=debug	# 这行配置写在application.properties
```

#### 3. 配置druid 数据源

```java
/**
 * @ClassName DruidConfig
 * @Description 配置Druid 数据源信息，方便监控
 * @Author zc-cris
 * @Version 1.0
 **/
@Configuration
public class DruidConfig {
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid() {
        return new DruidDataSource();
    }

    //配置Druid的监控
    //1、配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet() {
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        Map<String, String> initParams = new HashMap<>();

        initParams.put("loginUsername", "admin");
        initParams.put("loginPassword", "123456");
        initParams.put("allow", "");//默认就是允许所有访问
//        initParams.put("deny","");

        bean.setInitParameters(initParams);
        return bean;
    }


    //2、配置一个web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter() {
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String, String> initParams = new HashMap<>();
        initParams.put("exclusions", "*.js,*.css,/druid/*");

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return bean;
    }
}
```

#### 4. javaBean

```java
public class Employee implements Serializable{
	
	private Integer id;
	private String lastName;
	private String email;
	private Integer gender; //性别 1男  0女
	private Integer dId;
    ....
}
```

#### 5. Mapper

```java
// 这里使用注解版来标识mapper每个方法的执行sql，实际开发中基本使用mapper映射文件
public interface EmployeeMapper {

    // 插入数据完成后，将自增长的id值又放入当前department 对象中
    @Options(useGeneratedKeys = true, keyProperty = "id")
    @Insert("insert into employee (lastName, email, gender, d_id) values (#{lastName}, #{email}, #{gender}, #{dId})")
    public int saveEmp(Employee employee);

    @Delete("delete from employee where id = #{id}")
    public int removeEmpById(Integer id);

    @Update("update employee set lastName = #{lastName}, email = #{email}, gender = #{gender}, d_id = #{dId} where id = #{id}")
    public int updateEmp(Employee employee);

    @Select("select id, lastName, email, gender, d_id from employee where id = #{id}")
    public Employee getEmpById(Integer id);

    @Select("select id, lastName, email, gender, d_id from employee where lastName = #{lastName}")
    Employee getEmpByLastName(String lastName);
}

```

####  6.  测试druid 数据源

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootCacheApplicationTests {

    @Autowired
    EmployeeMapper employeeMapper;

    // 测试能否从远程服务器的mysql中获取数据（通过Druid数据源）
    @Test
    public void contextLoads() {
        Employee emp = employeeMapper.getEmpById(1);
        System.out.println(emp);
   }
}
```

#### 7. 主启动类

```java
/**
* @Author zc-cris
* @Description 1. 创建数据库 2. 创建对应的javaBean 3. 创建数据源（Druid）4. 使用Mybatis（注解扫描）4. 使用缓存
* ①。开启基于注解的缓存 ②。标注缓存注解即可(@Cachealbe, @CacheEvict, @CachePut)
* @Param
* @return
**/
@MapperScan("com.cris.springboot.mapper")
@SpringBootApplication
@EnableCaching
public class SpringbootCacheApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootCacheApplication.class, args);
    }
}
```

#### 8. service(重点)

```java
/**
 * @ClassName EmployeeService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
// 对于缓存的一些通用配置可以使用@CacheConfig 注解完成，例如通用的cache组件等
@CacheConfig(cacheNames = "emp", cacheManager = "EmpCacheManager")
@Service
public class EmployeeService {

    @Autowired
    EmployeeMapper employeeMapper;

    /**
     * @return com.cris.springboot.bean.Employee
     * @Author zc-cris
     * @Description 将方法的运行结果放入缓存，下次使用相同的数据直接从缓存中取，不再调用方法
     * - CacheManager 管理多个Cache 组件，对缓存的真正CRUD 操作在Cache组件中，每一个缓存组件都有一个唯一的名字
     * - 默认会配置一个SimpleCacheConfiguration；给容器中注册了一个CacheManager：ConcurrentMapCacheManager；
     * 可以获取和创建ConcurrentMapCache 类型的缓存组件：将数据保存在CocurrentMap 中
     * <p>
     * - 运行流程：@Cacheable
     * 1. 方法运行前：先去查询Cache（缓存组件），按照cacheNames 指定的名字获取（CacheManager先获取到对应的缓存组件，第一次获取组件如果没有就自动创建）
     * 2. 去Cache组件中查找缓存的内容，默认key就是方法的参数：
     * key是按照某种策略生成的：默认使用SimpleKeyGenerator 生成key
     * SimpleKeyGenerator生成key 的默认策略：
     * 如果没有参数：key=new SimpleKey();
     * 如果有一个参数：key=参数的值
     * 如果有多个参数：key=new SimpleKey(params)
     * 3. 没有查询到数据就调用目标方法（所以目标方法不一定会执行 ）
     * 4. 将目标方法的返回值放入到缓存组件中
     * <p>
     * - 流程总结：1. 使用CacheManager【ConcurrentMapCacheManager】按照名字得到Cache组件【ConcurrentMapCache】
     * 2. key使用keyGenerator生成，默认是SimpleKeyGenerator（我们可以自定义）
     * <p>
     * - 核心属性：
     * 1. cacheNames/value:指定缓存组件的名字
     * 2. key：缓存数据用的key，默认使用方法的参数值；可以使用SpEl；#id：参数id的值；#a0 #p0 #root.args[0]
     * 3. keyGenerator:key的生成器，可以自定义
     * key/keyGenerator : 二选一
     * 4. cacheManager：指定缓存管理器；或者使用cacheResolver指定解析器，两者选一个即可
     * 5. condition：指定符合条件才缓存数据，condition = "#id > 0"
     * 6. unless:否定缓存，unless 指定的条件为true，方法的返回值就不会被缓存，可以获取到结果进行判断
     * unless = "#result == null"
     * unless = "#a0 == 2":如果第一个参数的值为2，那么不缓存查询出来的数据
     * 7. sync：是否使用异步模式（默认为false）：如果为true，unless属性不支持
     * @Param [id]
     **/
    //@Cacheable(cacheNames = {"emp"}, key = "#root.methodName + '[' + #id + ']'")
    @Cacheable(cacheNames = {"emp"} /*, keyGenerator = "myKeyGenerator", condition = "#a0 > 1", unless = "#a0 == 2"*/)
    public Employee getEmp(Integer id) {
        System.out.println("查询" + id + "号员工...");
        return employeeMapper.getEmpById(id);
    }

    /**
     * @return com.cris.springboot.bean.Employee
     * @Author zc-cris
     * @Description @CachePut:先调用方法更新数据库数据，然后更新缓存，达到了同步更新缓存的目的，保证后面查询对应的数据都是缓存中最新的数据
     * 运行时机：和@Cacheable 不同，一定要先调用目标方法；然后将目标方法的返回结果缓存起来
     * 注意：缓存数据的key需要和@Cacheable 指定的key相同，保证更新和查询的数据都是同一个key指定的数据
     * key = "#employee.id":使用传入的employee对象的id
     * key = "#result.id":使用返回后的employee对象的id
     * 但是@Cacheable 中的key不能使用#result（因为是在目标方法执行前进行调用，拿不到目标方法的返回值）
     * @Param [employee]
     **/
    @CachePut(value = "emp", key = "#result.id")
    public Employee updateEmp(Employee employee) {
        employeeMapper.updateEmp(employee);
        return employee;
    }

    /**
     * @return void
     * @Author zc-cris
     * @Description @CacheEvict 代表缓存清除
     * key:指定要清除的缓存中的数据的key
     * allEntries = true：指定清除环村组件中的所有缓存；默认为false
     * beforeInvocation = false：缓存的清除是否在目标方法执行之前进行
     * 默认为false，即缓存清除在方法执行后进行；如果方法出现异常，那么缓存清理失败
     * 如果为true：代表缓存清理是在目标方法执行前就进行了，所以缓存肯定会清空
     * @Param [id]
     **/
    @CacheEvict(/*value = "emp", */key = "#id"/*, beforeInvocation = true, allEntries = true*/)
    public void deleteEmp(Integer id) {
        // 打印这句话代表数据已经被删除
        System.out.println("deleteEmp" + id);
//        employeeMapper.removeEmpById(id);
//        int i = 10/0;
    }

    /**
     * @return com.cris.springboot.bean.Employee
     * @Author zc-cris
     * @Description 多个缓存注解可以联合使用，以适应更加复杂的缓存需求
     * @Param [lastName]
     **/
    @Caching(cacheable = {
            @Cacheable(/*value = "emp",*/ key = "#lastName")
    },
            put = {
                    @CachePut(/*value = "emp",*/ key = "#result.id"),
                    @CachePut(/*value = "emp",*/ key = "#result.email")
            })
    public Employee getEmpByLastName(String lastName) {
        return employeeMapper.getEmpByLastName(lastName);
    }
}
```

#### 9. controller

```java
/**
 * @ClassName EmployeeController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class EmployeeController {

    @Autowired
    EmployeeService employeeService;

    @GetMapping("/emp/{id}")
    public Employee getEmp(@PathVariable("id") Integer id) {
        return employeeService.getEmp(id);
    }

    @GetMapping("/emp")
    public Employee updateEmp(Employee employee) {
        employeeService.updateEmp(employee);
        return employee;
    }

    @GetMapping("/delEmp/{id}")
    public void deleteEmp(@PathVariable("id") Integer id) {
        employeeService.deleteEmp(id);
    }

    @GetMapping("/emp/lastName/{lastName}")
    public Employee getEmpByLastName(@PathVariable("lastName") String lastName) {
        return employeeService.getEmpByLastName(lastName);
    }
}
```

#### 10. 自定义缓存key生成器

```java
/**
 * @ClassName MyCacheConfig
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
// 自定义的缓存key生成策略类
@Configuration
public class MyCacheConfig {

    @Bean("myKeyGenerator")
    public KeyGenerator keyGenerator() {
        return new KeyGenerator() {
            @Override
            public Object generate(Object target, Method method, Object... params) {
                return method.getName() + "[" + Arrays.asList(params).toString() + "]";
            }
        };
    }
}
```



## 二. SpringBoot 与 Redis整合

### 1. 整合redis 的dependency

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

### 2. 主启动类

```java
/**
 * @Author zc-cris
 * @Description 1. 创建数据库 2. 创建对应的javaBean 3. 创建数据源（Druid）4. 使用Mybatis（注解扫描）4. 使用缓存
 * ①。开启基于注解的缓存 ②。标注缓存注解即可(@Cachealbe, @CacheEvict, @CachePut)
 * 默认使用的是ConcurrentMapCacheManager，将数据保存到ConcurrentMap中
 * 开发中常用的则是Redis，memcached，ehcache
 * <p>
 * - 整合redis作为缓存
 * Redis是一个开源的，内存中的数据结构存储系统，可以用作数据库，缓存和消息中间件
 * 1. 使用docker 安装redis
 * 2. 工程引入redis的starter
 * 3. 配置redis
 * 4. 测试redis
 * 5. 测试缓存
 * - 原理：
 * - CacheManager--》Cache 缓存组件来给实际的缓存中存取数据
 * - 引入了redis的starter以后，容器中保存的就是RedisManager 了
 * - RedisManager 会帮我们创建RedisCache 缓存组件，而RedisCache 又是通过远程的redis服务器来缓存数据的
 * - 默认保存数据k-v都是Object的时候，使用jdk的序列化来保存数据，如果需要将数据转换为json格式再保存？
 * 1. 引入了redis的starter后，cacheManager 就变为了RedisCacheManager
 * 2. 默认创建的RedisCacheManger 操作redis的时候使用的是RedisTemplate<Object，Object>
 * 3. RedisTemplate<Object，Object> 默认使用的是jdk的序列化机制
 * 4. 自定义cacheManager
 * @Param
 * @return
 **/
@MapperScan("com.cris.springboot.mapper")
@SpringBootApplication
@EnableCaching
public class SpringbootCacheApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootCacheApplication.class, args);
    }
}
```

### 3. javaBean

```java
public class Department implements Serializable {

    private Integer id;
    private String departmentName;
	...
}
```

### 4. 自定义Redis 配置(自定义的序列化规则器类RedisTemplate 和 RedisCacheManager)

```java
/**
 * @ClassName MyRedisConfig
 * @Description 自定义Redis的序列化规则（以json格式保存我们的javaBean对象）
 * @Author zc-cris
 * @Version 1.0
 **/
@Configuration
public class MyRedisConfig {


    // 自定义redis的序列化规则
    @Bean
    public RedisTemplate<Object, Employee> employeeRedisTemplate(
            RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        RedisTemplate<Object, Employee> template = new RedisTemplate<Object, Employee>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setDefaultSerializer(new Jackson2JsonRedisSerializer<Employee>(Employee.class));
        return template;
    }


    // 自定义redis的cacheManager，将自定义的序列化规则器类传递进去
    @Bean
    @Primary        // 如果是有多个cacheManager的情况下，一定要使用@Primary 指定默认的cachaManager
    public RedisCacheManager EmpCacheManager(RedisTemplate<Object, Employee> employeeRedisTemplate) {
        RedisCacheManager cacheManager = new RedisCacheManager(employeeRedisTemplate);
        // 使用cache组件的cacheName作为前缀，方便区分数据
        cacheManager.setUsePrefix(true);
        return cacheManager;
    }

    // 自定义redis的序列化规则
    @Bean
    public RedisTemplate<Object, Department> departmentRedisTemplate(
            RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        RedisTemplate<Object, Department> template = new RedisTemplate<Object, Department>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setDefaultSerializer(new Jackson2JsonRedisSerializer<Department>(Department.class));
        return template;
    }

    // 自定义redis的cacheManager，将自定义的序列化规则器类传递进去
    @Bean
    public RedisCacheManager deptCacheManager(RedisTemplate<Object, Department> departmentRedisTemplate) {
        RedisCacheManager cacheManager = new RedisCacheManager(departmentRedisTemplate);
        // 使用cache组件的cacheName作为前缀，方便区分数据
        cacheManager.setUsePrefix(true);
        return cacheManager;
    }
}
```

### 5. controller

```java
/**
 * @ClassName DepartmentController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class DepartmentController {

    @Autowired
    DepartmentService departmentService;

    @GetMapping("/dept/{id}")
    public Department getDept(@PathVariable("id") Integer id) {
        return departmentService.getDept(id);
    }
}
```

### 6. mapper

```java
public interface DepartmentMapper {

    @Select("select * from department where id = #{id}")
    public Department getDeptById(Integer id);
}
```

### 7. service(重点)

```java
/**
 * @ClassName DepartmentService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class DepartmentService {

    @Autowired
    DepartmentMapper departmentMapper;

    @Qualifier("deptCacheManager")
    @Autowired
    CacheManager deptCacheManager;

    /**
     * @return com.cris.springboot.bean.Department
     * @Author zc-cris
     * @Description 缓存的数据可以存入redis；但是第二次从缓存中查询却无法反序列化回来，因为存入dept的json格式的数据
     * 但是默认使用RedisTemplate<Object, Employee> 序列化类来反序列化
     * 解决方案：为每个单独的需要序列化的javaBean对象创建CacheManager 和 cacheTemplate，service层调用dao层方法的时候缓存注解需要注明指定的属性
     * @Param [id]
     **/
//    @Cacheable(cacheNames = "dept", cacheManager = "deptCacheManager")
//    public Department getDept(Integer id){
//        return departmentMapper.getDeptById(id);
//    }
    public Department getDept(Integer id) {
        System.out.println("查询" + id + "号部门");
        Department dept = departmentMapper.getDeptById(id);
        // 使用cacheManager 来获取指定的缓存组件
        Cache cache = deptCacheManager.getCache("dept");
        // 编码方式将数据放入到缓存组件中
        cache.put("dept:1", dept);
        return dept;
    }
}
```

### 8. 测试

```java
    @Autowired
    StringRedisTemplate stringRedisTemplate;    // 操作k-v都是字符串

    @Autowired
    RedisTemplate redisTemplate;    // 操作k-v都是对象的

    @Autowired
    RedisTemplate<Object, Employee> employeeRedisTemplate; // 自定义序列化规则的redisTemplate

    /**
     * @return void
     * @Author zc-cris
     * @Description Redis 常见的五大数据类型
     * String，List，Set，Hash，ZSet（有序集合）
     * @Param []
     **/
    @Test
    public void testRedis() {
        //给远程redis服务器中保存数据
//        stringRedisTemplate.opsForValue().append("msg", "hello");

        // 从远程redis服务器读取数据
        String msg = stringRedisTemplate.opsForValue().get("msg");
        System.out.println(msg);

        stringRedisTemplate.opsForList().leftPush("list", "1");
        stringRedisTemplate.opsForList().leftPush("list", "2");

    }

    @Test
    public void testRedis2() {
        // 保存对象到redis服务器,默认使用jdk的序列化机制，也就是说将序列化后的数据保存到redis
//        redisTemplate.opsForValue().set("emp-01", employeeMapper.getEmpById(1));
        // 如果想将数据以josn形式保存：一般有两种方式：1. 自己将对象转换为json；2.自定义redis的序列化规则（推荐）

        employeeRedisTemplate.opsForValue().set("emp-01", employeeMapper.getEmpById(1));
    }
```



## 三. SpringBoot 和消息中间件 RabbitMQ

### 1. docker 整合 RabbitMQ

```xml
docker pull registry.docker-cn.com/library/rabbitmq:3-management
docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq c51d1c73d028
```

### 2. 进入到rabbitMQ的管理界面，添加三个不同类型的exchange（交换器）：direct,fanout,topic

### 3. 创建四个消息队列（cris,cris.news,cris.emps,zc.news），并且和交换器绑定,测试交换器和队列之间通信规则

### 4. SpringBoot 工程整合RabbitMQ

#### ① pom.xml

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

#### ② 自定义json类型的数据转换器

```java
/**
 * @ClassName MyRabbitConfig
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Configuration
public class MyRabbitConfig {

    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
```

#### ③ javaBean

```java
/**
 * @ClassName User
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
public class User {
    private String name;
    private Integer age;
    ...
}
```

#### ④ service

```java
/**
 * @ClassName UserService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class UserService {

    @RabbitListener(queues = {"cris.news"})
    public void receive(User user){
        System.out.println(user);
    }

    @RabbitListener(queues = {"cris"})
    public void receive2(Message message){
        System.out.println(message.getBody()+"---------------------消息体"); //消息体
        System.out.println(message.getMessageProperties()+"----------消息头"); //消息头
    }
}
```

#### ⑤ 主程序类

```java
/*
* @Author zc-cris
* @Description 自动配置：
*   1. RabbitAutoConfiguration
*   2. 自动配置了连接工厂ConnectionFactory
*   3. RabbitProperties：封装了RabbitMQ的配置信息
*   4. RabbitTemplate：给RabbitMQ发送和接收消息的模板
*   5. AmqpAdmin：RabbitMQ的系统管理功能组件
*   6. @EnableRabbit + @RabbitListener 开启监听消息队列的消息，如果消息发送到exchange然后被放入queue，就会被
*       对应的监听方法获取到消息
* @Param 
* @return 
**/
@EnableRabbit
@SpringBootApplication
public class SpringbootAmqpRabbitmqApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootAmqpRabbitmqApplication.class, args);
    }
}
```

#### ⑥ 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootAmqpRabbitmqApplicationTests {

    @Autowired
    RabbitTemplate rabbitTemplate;

    @Autowired
    AmqpAdmin amqpAdmin;

    // 通过编码的方式创建exchange，queue，binding
    @Test
    public void test2(){
//        amqpAdmin.declareExchange(new DirectExchange("amqpAdmin.exchange"));
//        amqpAdmin.declareQueue(new Queue("amqpAdmin.queue", true));
//        amqpAdmin.declareBinding(new Binding("amqpAdmin.queue", Binding.DestinationType.QUEUE, "amqpAdmin.exchange", "cris521", null));
    amqpAdmin.deleteQueue("amqpAdmin.queue");
    amqpAdmin.deleteExchange("amqpAdmin.exchange");

    }

    /**
    * @Author zc-cris
    * @Description 使用springboost 整合rabbitmq的方式来操作rabbitmq消息中间件
     * 1. 点对点(单播)
    * @Param []
    * @return void
    **/
    @Test
    public void contextLoads() {
//        可以自定义message的消息体和消息头
//        rabbitTemplate.send(exchange, routeKey,message);


//        object默认作为消息体，只需要传入要发送的对象，自动序列化发送给rabbitmq 服务器
//        rabbitTemplate.convertAndSend(exchange, routeKey, Object);
        Map<String, Object> map = new HashMap<>();
        map.put("msg", "这个第一个通过springboot 发送过来的消息");
        map.put("data", Arrays.asList("123", 321, true));
//        rabbitTemplate.convertAndSend("exchange.direct", "cris.news", map);
        // 测试javaBean对象能否以json格式发送到服务器以及从服务器接收（ok）
        rabbitTemplate.convertAndSend("exchange.direct", "cris", new User("cris", 21));

    }
    // 取出rabbitmq 服务器消息队列里面的信息并且转换为java对象(消息队列里的消息：先进先出)
    @Test
    public void testReceive(){
        Object o = rabbitTemplate.receiveAndConvert("cris");
        System.out.println(o.getClass());   //class java.util.HashMap
        System.out.println(o);  // {msg=这个第一个通过springboot 发送过来的消息, data=[123, 321, true]}
    }

    // 测试广播模式的交换器
    @Test
    public void test(){
        rabbitTemplate.convertAndSend("exchange.fanout", "", new User("重庆吴亦凡", 25));
    }
}
```

#### ⑦ 配置文件（应该放在第一步的。。。）

```yaml
spring:
  rabbitmq:
    host: 120.78.138.11
    username: guest
    password: guest
#    默认就是5672，可以不写
    port: 5672
#    默认不写就是访问/
#    virtual-host: /
```



## 四. SpringBoot 整合 ElasticSearch

### 1. Docker 整合 ES

```dockerfile
docker 下载elasticSearch并启动：
docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES01 0ba66712c1f9
```

### 2. 使用postman 向ES 服务器发送REST形式的请求

- 存储数据（json）put请求

  ![1](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/1.png)

- 获取数据（get请求）

  ![2](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/2.png)

- 判断资源是否存在（head请求）

  ![3](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/3.png)

  ![4](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/4.png)

- 删除数据（delete请求）

  ![5](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/5.png)

- 更新数据（put请求）

  ![6](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/6.png)

- _search 搜索员工数据；通过json格式的搜索信息进行强大的各种搜索（重点就是查询表达式）

  ![7](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/7-1526351639663.png)

### 3. SpringBoot 整合 ES 并使用jest 操作

#### ① pom.xml

```xml
<dependencies>
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
        <dependency>
            <groupId>io.searchbox</groupId>
            <artifactId>jest</artifactId>
            <version>5.3.3</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

#### ② 主程序类

```java
/*
* @Author zc-cris
* @Description SpringBoot 默认支持两种方式来操作ElasticSearch
* 1. Jest（默认不生效）
*   所以需要导入jest的工具包：（io.searchbox.client.JestClient)
* 2. SpringData ElasticSearch(有可能版本不对应，要么升级springboot版本，要么docker安装对应版本的ES)
*   - Client 节点信息：clusterNodes，clusterName
*   - ElasticSearchTemplate 操作es
*   - 还可以编写一个ElasticSearchRepository 的子接口来操作es
* 3. 使用spring data 的方式来操作ES
*   - ES5.6.9版本默认无法从外部访问9300端口，2.xx 版本就可以，暂时未解决。。。
*   - ElasticSearchRepository 接口的子类可以根据方法名来进行数据的检索
*   - 注入 ElasticSearchTemplate 可以根据不同的条件方法来检索数据
* @Param
* @return
**/
@SpringBootApplication
public class SpringbootElasticsearchApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootElasticsearchApplication.class, args);
    }
}
```

#### ③ javaBean

```java
/**
 * @ClassName Ariticle
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
public class Ariticle {

    @JestId     // 指定文档的索引id属性
    private Integer id;
    private String author;
    private String title;
    private String content;
    ...
}
```

#### ④ application.yml

```yaml
spring:
  elasticsearch:
    jest:
      uris: http://120.78.138.11:9200
```

#### ⑤ 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootElasticsearchApplicationTests {

    @Autowired
    JestClient jestClient;
    
    @Test
    public void contextLoads() {

        Ariticle ariticle = new Ariticle();
        ariticle.setId(1);
        ariticle.setAuthor("张大帅");
        ariticle.setTitle("屌丝程序员");
        ariticle.setContent("逆袭白富美，走上人生巅峰~");

        //构建索引
        Index i = new Index.Builder(ariticle).index("cris").type("articles").build();

        try {
            // 执行
            jestClient.execute(i);
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    // 测试搜索
    @Test
    public void test() {
        // 查询表达式
        String string = "{\n" +
                "    \"query\" : {\n" +
                "        \"match\" : {\n" +
                "            \"content\" : \"白富美\"\n" +
                "        }\n" +
                "    }\n" +
                "}";

        // 构建搜索功能
        Search build = new Search.Builder(string).addIndex("cris").addType("articles").build();
        try {
            // 执行搜索功能
            SearchResult execute = jestClient.execute(build);
            System.out.println(execute.getJsonString());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```



### 4. SpringBoot 使用ElasticSearchRepository 接口或者ElasticSearchTemplate  类操作ES

#### ① javaBean

```java
/**
 * @ClassName Book
 * @Description 如果使用sprig data 的方式来存储数据到ES，需要在javaBean上面使用@Document注解
 * @Author zc-cris
 * @Version 1.0
 **/
@Document(indexName = "cris", type = "books")
public class Book {
    private Integer id;
    private String name;
    private String author;
    ...
}
```

#### ② application.properties

```properties
spring.data.elasticsearch.cluster-name=elasticsearch
spring.data.elasticsearch.cluster-nodes=120.78.138.11:9300
```

#### ③ 自定义Repository

```java
public interface BookRepository extends ElasticsearchRepository<Book, Integer> {
}
```

#### ④ 测试

```java
    @Autowired
    BookRepository bookRepository;

    @Test
    public void test03(){
        Book book = new Book();
        book.setId(1);
        book.setName("神魔");
        book.setAuthor("血红");
//        System.out.println(bookRepository);
        bookRepository.index(book);
    }
```



## 五. SpringBoot 任务管理

### 1. pom.xml

```xml
 <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
   </dependencies>
```

### 2. 主程序类

```java
@EnableScheduling // 开启定时任务注解
@EnableAsync    // 开启异步注解
@SpringBootApplication
public class SpringbootTaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootTaskApplication.class, args);
    }
}
```

### 3. 异步任务处理

```java
/**
 * @ClassName AsyncService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class AsyncService {

    @Async
    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("处理数据中...");
    }
}

/**
 * @ClassName AsyncController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class AsyncController {

    @Autowired
    AsyncService asyncService;

    @GetMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "success";
    }
}
```

### 4. 定时任务处理

```java
/**
 * @ClassName ScheduleService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class ScheduleService {

    /**
    * @Author zc-cris
    * @Description second,minute,hour,day of month,month,day of week
     *              * * * * * MON-FRI （表示周一到周五的每一秒都执行这个定时任务）
     *              【0 0/5 14,18 * * ?】:每天的14点和18点，整点开始每隔5分钟执行一次
     *              【0 15 10 ? * 1-6】: 每个月的周一到周六的10点15分执行一次
     *              【0 0 2 ? * 6L】:每个月的最后一个周六的2点执行一次
     *              【0 0 2 LW * ?】:每个月的最后一个工作日的2点执行一次
     *              【0 0 2-4 ? * 1#1】: 每个月的第一个周一的2点至4点每个整点执行一次
    * @Param []
    * @return void
    **/
    //@Scheduled(cron = "0 * * * * MON-FRI")  // 周一到周五每分钟执行一次
    //@Scheduled(cron = "0,1,2,3,4 * * * * MON-FRI") // 周一到周五每分钟的每0,1,2,3,4 秒执行一次
    //@Scheduled(cron = "0-4 * * * * MON-FRI")    // 同上
    //@Scheduled(cron = "0/4 * * * * MON-FRI")    // 周一到周五每4秒执行一次
    public void scheduledTask(){
        System.out.println("定时任务执行中...");
    }
}
```

### 4. 邮件任务处理

#### ① application.yml

```yaml
spring:
  mail:
    username: 990435014@qq.com
    password: atyhijliedaqbcgg
    host: smtp.qq.com
    properties: {mail.smtp.ssl.enable: true}
```

#### ② 邮件发送测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootTaskApplicationTests {

    @Autowired
    JavaMailSenderImpl javaMailSender;

    // 测试发送一封简单的邮件
	@Test
	public void contextLoads() {
        SimpleMailMessage message = new SimpleMailMessage();
        // 邮件设置
        message.setSubject("今晚开会");
        message.setText("晚上8:00 到4楼开会");

        message.setTo("17623887386@163.com");
        message.setFrom("990435014@qq.com");

	    javaMailSender.send(message);

	}

	// 发送一封复杂的邮件
	@Test
    public void test() throws Exception{
	    // 创建一封复杂的邮件
        MimeMessage message = javaMailSender.createMimeMessage();
        // 是否要上传文件
        MimeMessageHelper helper = new MimeMessageHelper(message, true);

        // 邮件设置
        helper.setSubject("通知—今晚开会");
        helper.setText("<b style='color:red'>今晚9:00到天台好好聊聊，兄弟</b>", true);
        helper.setTo("17623887386@163.com");
        helper.setFrom("990435014@qq.com");

        // 上传附件
        helper.addAttachment("1.jpg", new File("C:\\Users\\zc-cris\\Pictures\\FLAMING MOUNTAIN.JPG"));
        helper.addAttachment("2.jpg", new File("C:\\Users\\zc-cris\\Downloads\\明日花\\1.jpg"));

        // 发送邮件
        javaMailSender.send(message);
    }
}
```



## 六. SpringBoot 和SpringSecurity 整合

### 1. pom.xml

```xml
    <dependencies>
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity4</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### 2. application.properties

```properties
spring.thymeleaf.cache=false
```

### 3. 导入静态资源

![8](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/8.png)

### 

### 4. 主程序类

```java
/*
* @Author zc-cris
* @Description
*   1. 引入spring security 的启动器
*   2. 编写spring security 的配置类
* @Param
* @return
**/
@SpringBootApplication
public class SpringbootSecurityApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootSecurityApplication.class, args);
    }
}
```

### 5. 自定义Spring Security 配置类

```java
/**
 * @ClassName MySecurityConfig
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@EnableWebSecurity  // 开启security注解
public class MySecurityConfig extends WebSecurityConfigurerAdapter {

    // 自定义静态资源的权限规则
    @Override
    protected void configure(HttpSecurity http) throws Exception {
    //    super.configure(http);
        // 定制请求的授权规则
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("VIP1")
                .antMatchers("/level2/**").hasRole("VIP2")
                .antMatchers("/level3/**").hasRole("VIP3");

        // 开启自动配置的登录功能，即如果没有登录，没有权限就会来到默认的用户登录页面
        http.formLogin().usernameParameter("user").passwordParameter("pwd").loginPage("/userlogin");   // 发送指定请求到指定的登录页面
        // 1. 发送 /login请求来到默认的登录页面
        // 2. 登录失败发送 /login?error 表示登录失败
        // 3. 更多详情参考文档
        // 4. 默认发送post形式的/login请求会交给spring security来处理登录验证；但是一旦修改了loginPage方法，
        // 即定制了loginPage，那么loginPage的post请求就是处理登录验证逻辑的请求（但是可以通过loginProcessingUrl(String url)来修改请求路径）

        // 开启自动配置的注销功能
        http.logout().logoutSuccessUrl("/");    // 修改注销成功后返回首页
        // 1. 默认访问/logout 表示注销请求，同时清空session
        // 2. 默认注销成功后返回到 /login?logout 页面（就是默认的登录页面）

        // 开启默认的记住我功能；登录成功后，服务器将名为remember-me的cookie 发送给浏览器保存，以后再访问服务器带上这个cookie即可，只要再
        // 服务器找到对应的这条cookie记录就可以免登录；如果注销成功也会删除这个cookie
        http.rememberMe().rememberMeParameter("remember");  // 自定义记住我的参数名字
    }

    // 用户登录以及授权功能
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
//        super.configure(auth);
        /*实际开发中用户名和密码，角色信息都应该从内存中读取*/
        auth.inMemoryAuthentication().withUser("cris").password("123456").roles("VIP1", "VIP2")
                .and()
                .withUser("张三").password("123456").roles("VIP2", "VIP3");
    }
}
```

### 6. controller

```java
@Controller
public class KungfuController {
	private final String PREFIX = "pages/";
	/**
	 * 欢迎页
	 * @return
	 */
	@GetMapping("/")
	public String index() {
		return "welcome";
	}
	
	/**
	 * 登陆页
	 * @return
	 */
	@GetMapping("/userlogin")
	public String loginPage() {
		return PREFIX+"login";
	}
	
	
	/**
	 * level1页面映射
	 * @param path
	 * @return
	 */
	@GetMapping("/level1/{path}")
	public String level1(@PathVariable("path")String path) {
		return PREFIX+"level1/"+path;
	}
	
	/**
	 * level2页面映射
	 * @param path
	 * @return
	 */
	@GetMapping("/level2/{path}")
	public String level2(@PathVariable("path")String path) {
		return PREFIX+"level2/"+path;
	}
	
	/**
	 * level3页面映射
	 * @param path
	 * @return
	 */
	@GetMapping("/level3/{path}")
	public String level3(@PathVariable("path")String path) {
		return PREFIX+"level3/"+path;
	}
}
```

### 7. 自定义登录页面

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1 align="center">欢迎登陆武林秘籍管理系统</h1>
	<hr>
	<div align="center">
		<form th:action="@{/userlogin}" method="post">
			用户名:<input type="text" name="user"/><br>
			密码:<input type="password" name="pwd"><br/>
            <input type="checkbox" name="remember">记住我<br>
			<input type="submit" value="登陆">
		</form>
	</div>
</body>
</html>
```

### 8. 自定义欢迎页面（根据不同权限显示不同内容）

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<!--根据当前用户的登录情况，显示不同的内容-->
<h1 align="center">欢迎光临武林秘籍管理系统</h1>
<div sec:authorize="!isAuthenticated()">
<h2 align="center">游客您好，如果想查看武林秘籍 <a th:href="@{/userlogin}">请登录</a></h2>
</div>
<div sec:authorize="isAuthenticated()">
    <h2><span sec:authentication="name"></span>,欢迎您！您的角色有：<span sec:authentication="principal.authorities"></span></h2>
    <form th:action="@{/logout}" method="post">
        <input type="submit" th:value="注销">
    </form>
</div>

<hr>

<!--根据不同用户的roles，显示不同的页面数据-->
<div sec:authorize="hasRole('VIP1')">
    <h3>普通武功秘籍</h3>
    <ul>
        <li><a th:href="@{/level1/1}">罗汉拳</a></li>
        <li><a th:href="@{/level1/2}">武当长拳</a></li>
        <li><a th:href="@{/level1/3}">全真剑法</a></li>
    </ul>
</div>

<div sec:authorize="hasRole('VIP2')">
    <h3>高级武功秘籍</h3>
    <ul>
        <li><a th:href="@{/level2/1}">太极拳</a></li>
        <li><a th:href="@{/level2/2}">七伤拳</a></li>
        <li><a th:href="@{/level2/3}">梯云纵</a></li>
    </ul>
</div>

<div sec:authorize="hasRole('VIP3')">
    <h3>绝世武功秘籍</h3>
    <ul>
        <li><a th:href="@{/level3/1}">葵花宝典</a></li>
        <li><a th:href="@{/level3/2}">龟派气功</a></li>
        <li><a th:href="@{/level3/3}">独孤九剑</a></li>
    </ul>
</div>
</body>
</html>
```



## 七. SpringBoot 分布式整合Dubbo，Zookeeper(绝对重点)

### 1. Docker 下载zookeeper和启动

```xml
docker run --name zk1 -p 2181:2181 --restart always -d 56d414270ae3
```

### 2. 构建空工程并创建对应的模块（服务提供模块和消费者模块）

![11](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/11.png)

### 3. SpringBoot 引入Dubbo，Zookeeper依赖

```xml
   <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--引入dubbo相关依赖-->
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>0.1.0</version>
        </dependency>

        <!--导入zkclient 客户端-->
        <dependency>
            <groupId>com.github.sgroschupf</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.1</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### 4. 服务提供者模块

#### ① application.properties

```properties
dubbo.application.name=provider-ticket
dubbo.registry.address=zookeeper://120.78.138.11:2181
dubbo.scan.base-packages=com.cris.ticket.service
```

#### ② 主程序类

```java
/*
* @Author zc-cris
* @Description 将服务注册到服务中心
*               1. 引入dubbo和zookeeper的相关依赖
*               2. 配置dubbo的扫描包和注册中心地址
*               3. 使用@Service（dubbo的注解）发布服务
* @Param
* @return
**/
@SpringBootApplication
public class ProviderTicketApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderTicketApplication.class, args);
    }
}
```

#### ③ 服务组件的service

```java
/**
 * @ClassName TicketServiceImpl
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Component
@Service    // 使用dubbo 的@Service 注解将服务注册到zookeeper
public class TicketServiceImpl implements TicketService {
    @Override
    public String saleTicket() {
        return "复仇者联盟三";
    }
}
```



### 5. 消费者模块

#### ① 引入相同的Zookeeper 和Dubbo 依赖

#### ② service

```java
/**
 * @ClassName UserService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class UserService {

    @Reference  // 根据全类名来引用注册中心的服务提供者
    TicketService ticketService;

    public void buy(){
        System.out.println("买到票了："+ticketService.saleTicket());
    }
}
```

#### ③ 引入服务提供者的接口

![9](C:\File\Typora\SpringBoot 高级整合篇\SpringBoot 高级整合篇.assets/9.png)

#### ④ application.yml

```yaml
dubbo:
  application:
    name: consumer-user
  registry:
    address: zookeeper://120.78.138.11:2181
```

#### ⑤ 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ConsumerUserApplicationTests {

    @Autowired
    UserService userService;

    @Test
    public void contextLoads() {
        userService.buy();
    }
}
```



## 八. SpringBoot 整合 SpringCloud（绝对重点）

### 1. 创建一个空工程，引入eureka 服务中心模块，服务者模块，消费者模块

### 2. eureka 服务中心模块

#### ① pom.xml（选择Eureka Server 启动器）

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

#### ② application.yml

```yaml
server:
  port: 8761
eureka:
  instance:
    hostname: eureka-server # eureka实例的主机名
  client:
    register-with-eureka: false #不把自己注册到eureka上
    fetch-registry: false   # 不从eureka上获取服务的注册信息
    service.url:
      defaultZone: http://localhost:8761/eureka/  #我们自己的服务中心地址
```

#### ③ 主程序类

```java
/**
* @Author zc-cris
* @Description 注册中心
 * 1. 配置Eureka注册中心的信息
 * 2. @EnableEurekaServer
* @Param 
* @return 
**/
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```



### 3. 服务者模块

#### ① pom.xml

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

#### ② application.yml

```yaml
server:
  port: 8002
spring:
  application:
    name: provider-ticket
eureka:
  instance:
    prefer-ip-address: true #注册到服务中心使用ip进行注册
  client:
    service.url:
      defaultZone: http://localhost:8761/eureka/  #我们自己的服务中心地址
```

#### ③ service

```java
/**
 * @ClassName TicketService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class TicketService {

    public String getTicket(){
        System.out.println("这是8002端口提供的服务");
        return "《复仇者联盟3》";
    }
}
```

#### ④ controller

```java
/**
 * @ClassName TicketController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
public class TicketController {

    @Autowired
    TicketService ticketService;

    @GetMapping("/ticket")
    public String getTicket(){
        return ticketService.getTicket();

    }
}
```



### 4. 消费者模块

#### ① pom.xml 同服务者模块（都是选择Eureka Discovery 启动器）

#### ② application.yml

```yaml
spring:
  application:
    name: consumer-user
server:
  port: 8200
eureka:
  instance:
    prefer-ip-address: true #注册到服务中心使用ip进行注册
  client:
    service.url:
      defaultZone: http://localhost:8761/eureka/  #我们自己的服务中心地址
```

#### ③ 主程序类

```java
@EnableDiscoveryClient  //开启发现eureka注册中心的服务组件的功能
@SpringBootApplication
public class ConsumerUserApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerUserApplication.class, args);
    }

    @LoadBalanced   //启动负载均衡机制
    @Bean   // 这个类帮助我们的消费者向服务中心发送http请求
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

#### ④ controller

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
    RestTemplate restTemplate;

    @GetMapping("/buy")
    public String buyTicket(String name) {
        String ticket = restTemplate.getForObject("http://PROVIDER-TICKET/ticket", String.class);
        return name + "买了票" + ticket;
    }
}
```



### 5. 测试总结：

​	和 Dubbo 以及 Zookeeper 的分布式应用相比，SpringCloud 为我们提供了更加一致性的整体解决方案，通过SpringCloud 为我们提供的五大神兽，我们可以以一种更加从容和优雅的方式搭建我们的分布式应用

***SpringCloud分布式开发五大常用组件*** 

1. 服务发现——Netflix Eureka 
2. 客服端负载均衡——Netflix Ribbon 
3. 断路器——Netflix Hystrix 
4. 服务网关——Netflix Zuul 
5. 分布式配置——Spring Cloud Config 

​        我们不再需要将服务者模块发布到远程的Zookeeper 注册中心服务器，不需要通过Dubbo 完成消费者模块和服务者模块之间的信息交互（导入服务者模块的接口到消费者模块）；SpringCloud 借助于强大的SpringBoot，将我们的应用完美的以微服务的架构呈现，并且提供一站式解决方案；每个模块之间的交互通过rest 请求进行交互；在一个庞大的整体模块中，每一个模块又分割的仅仅有条，这就是SpringCloud 给出的微服务架构最好解决方案。

 

## 九. SringBoot 的热部署（Devtools）

### 1. 导入对应的启动器即可

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
</dependency>
```



## 十. SpringBoot 监控管理

### 1. pom.xml

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### 2. 配置文件(application.properties)定制端点信息

````properties
# 允许访问端点
management.security.enabled=false

info.app.name=hello
info.app.version=1.0.0

# 允许远程通过访问/shutdown 路径关闭应用，仅开发使用
endpoints.shutdown.enabled=true
# 开启或关闭某个端点访问
#endpoints.beans.enabled=false
# 定制端点访问路径
#endpoints.dump.path=/du

# 关闭所有端点访问
#endpoints.enabled=false
#endpoints.beans.enabled=true

# 定制访问端点的根路径（结合spring security 完成权限控制）
management.context-path=/manage

# （结合spring security 完成权限控制）
# 定制端点访问的端口号，-1表示关闭所有端点访问
management.port=8181

spring.redis.host=120.78.138.11
````

### 3. 自定义组件健康状态监控器类

```java
/**
 * @ClassName MyHealthIndicator
 * @Description 自定义组件健康监控器必须实现HealthIndicator 接口，并且命名必须是xxxHealthIndicator
 * @Author zc-cris
 * @Version 1.0
 **/
@Component
public class MyHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {

        // 自定义的检查方法
        // 代表健康
//        return Health.up().build();
        return Health.down().withDetail("msg", "服务挂掉了！").build();
    }
}
```

