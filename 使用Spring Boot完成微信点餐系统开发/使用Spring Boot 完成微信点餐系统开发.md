# 使用Spring Boot 完成微信点餐系统开发

## 1. 项目环境

![1527176508502](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527176508502.png)

- 虚拟机使用说明

  ```markdown
  # 虚拟机说明文档
  VirtualBox-5.1.22
  虚拟机系统 centos7.3
  账号 root
  密码 123456
  #### 包括软件
  * jdk 1.8.0_111
  * nginx 1.11.7
  * mysql 5.7.17
  * redis 3.2.8

  ##### jdk
  * 路径 /usr/local/jdk1.8.0_111

  ##### nginx
  * 路径 /usr/local/nginx
  * 启动 nginx
  * 重启 nginx -s reload

  ##### mysql
  * 配置 /etc/my.conf
  * 账号 root
  * 密码 123456
  * 端口 3306
  * 启动 systemctl start mysqld
  * 停止 systemctl stop mysqld

  ##### redis
  * 路径 /usr/local/redis
  * 配置 /etc/reis.conf
  * 端口 6379
  * 密码 123456
  * 启动 systemctl start redis
  * 停止 systemctl stop redis

  ##### tomcat
  * 路径 /usr/local/tomcat
  * 启动 systemctl start tomcat
  * 停止 systemctl stop tomcat
  ```

  ​

## 2. 项目设计分析

### 2.1 角色划分

![1527176723386](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527176723386.png)

### 2.2 功能分析

![1527176769049](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527176769049.png)

### 2.3 关系流程图

![1527176845545](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527176845545.png)

### 2.4 部署架构

![1527176904100](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527176904100.png)

- 注意：本系统支持分布式部署（可以部署在多个tomcat 上）

### 2.5 微服务两大流派

![1527177138867](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527177138867.png)



## 3. 数据库

### 3.1 数据库分析

![1527177319463](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527177319463.png)

### 3.2 数据库设计（注意索引和修改时间小技巧）

#### - 商品表

![1527177782997](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527177782997.png)

```sql
-- 商品
create table `product_info` (
    `product_id` varchar(32) not null,
    `product_name` varchar(64) not null comment '商品名称',
    `product_price` decimal(8,2) not null comment '单价',
    `product_stock` int not null comment '库存',
    `product_description` varchar(64) comment '描述',
    `product_icon` varchar(512) comment '小图',
    `product_status` tinyint(3) DEFAULT '0' COMMENT '商品状态,0正常1下架',
    `category_type` int not null comment '类目编号',
    `create_time` timestamp not null default current_timestamp comment '创建时间',
    `update_time` timestamp not null default current_timestamp on update current_timestamp comment '修改时间',
    primary key (`product_id`)
);
```

#### - 类目表

![1527178340134](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527178340134.png)

```sql
-- 类目
create table `product_category` (
    `category_id` int not null auto_increment,
    `category_name` varchar(64) not null comment '类目名字',
    `category_type` int not null comment '类目编号',
    `create_time` timestamp not null default current_timestamp comment '创建时间',
    `update_time` timestamp not null default current_timestamp on update current_timestamp comment '修改时间',
    primary key (`category_id`),
		UNIQUE KEY `uqe_category_type` (`category_type`)
);
ALTER TABLE product_category ADD UNIQUE KEY uqe_category_type (category_type);
```

#### - 订单表

![1527178370485](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527178370485.png)

```sql
-- 订单
create table `order_master` (
    `order_id` varchar(32) not null,
    `buyer_name` varchar(32) not null comment '买家名字',
    `buyer_phone` varchar(32) not null comment '买家电话',
    `buyer_address` varchar(128) not null comment '买家地址',
    `buyer_openid` varchar(64) not null comment '买家微信openid',
    `order_amount` decimal(8,2) not null comment '订单总金额',
    `order_status` tinyint(3) not null default '0' comment '订单状态, 默认为新下单',
    `pay_status` tinyint(3) not null default '0' comment '支付状态, 默认未支付',
    `create_time` timestamp not null default current_timestamp comment '创建时间',
    `update_time` timestamp not null default current_timestamp on update current_timestamp comment '修改时间',
    primary key (`order_id`),
    key `idx_buyer_openid` (`buyer_openid`)		
);
```

#### - 订单详情表

![1527178781641](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527178781641.png)

```sql
-- 订单详情
create table `order_detail` (
    `detail_id` varchar(32) not null,
    `order_id` varchar(32) not null,
    `product_id` varchar(32) not null,
    `product_name` varchar(64) not null comment '商品名称',
    `product_price` decimal(8,2) not null comment '当前价格,单位分',
    `product_quantity` int not null comment '数量',
    `product_icon` varchar(512) comment '小图',
    `create_time` timestamp not null default current_timestamp comment '创建时间',
    `update_time` timestamp not null default current_timestamp on update current_timestamp comment '修改时间',
    primary key (`detail_id`),
    key `idx_order_id` (`order_id`)
);
```

#### - 卖家详情表

 ```sql
-- 卖家(登录后台使用, 卖家登录之后可能直接采用微信扫码登录，不使用账号密码)
create table `seller_info` (
    `id` varchar(32) not null,
    `username` varchar(32) not null,
    `password` varchar(32) not null,
    `openid` varchar(64) not null comment '微信openid',
    `create_time` timestamp not null default current_timestamp comment '创建时间',
    `update_time` timestamp not null default current_timestamp on update current_timestamp comment '修改时间',
    primary key (`id`)
) comment '卖家信息表';
 ```

## 4. 环境搭建

### 4.1 linux

- 下载安装virtual box

- 安装linux 系统包（如果双击安装请保证全英文路径）

- 设置虚拟机网路

  ![1527181864852](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527181864852.png)

- 使用xshell 操作linux

  ![1527181970417](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527181970417.png)

  ![1527182008252](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527182008252.png)

  ![1527182080084](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527182080084.png)

  ![1527182361019](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527182361019.png)

  ![1527182460572](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527182460572.png)

### 4.2 建立数据库和表格

![1527182914258](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527182914258.png)

### 4.3 maven 和 jdk 版本

![1527183156202](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527183156202.png)



## 5. 工程构建（IDEA）

![1527261725294](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527261725294.png)

![1527262089812](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527262089812.png)

### 5.1 springboot 中的日志框架选择

![1527262192554](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527262192554.png)

![1527262300435](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527262300435.png)



### 5.2 springboot 日志处理细节

![1527262924590](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527262924590.png)

![1527263039722](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527263039722.png)

![1527263056917](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527263056917.png)

![1527263150635](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527263150635.png)



### 5.3 借助lombok 插件完成logger 重复代码生成(日志logger和模板方法)

![1527263785752](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527263785752.png)

- 注意，如果测试不了，那么IDEA 必须事先装好lombok 插件，网上搜索即可

  ![1527263889929](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527263889929.png)

  ![1527264135408](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527264135408.png)

  ​

### 5.4 日志配置

![1527264234148](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527264234148.png)

![1527264302365](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527264302365.png)

- 具体日志文件

  ![1527267976793](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527267976793.png)

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>

  <configuration>

      <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
          <layout class="ch.qos.logback.classic.PatternLayout">
              <pattern>
                  %d - %msg%n
              </pattern>
          </layout>
      </appender>

      <appender name="fileInfoLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
          <filter class="ch.qos.logback.classic.filter.LevelFilter">
              <level>ERROR</level>
              <onMatch>DENY</onMatch>
              <onMismatch>ACCEPT</onMismatch>
          </filter>
          <encoder>
              <pattern>
                  %msg%n
              </pattern>
          </encoder>
          <!--滚动策略-->
          <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
              <!--路径-->
              <fileNamePattern>/var/log/tomcat/sell/info.%d.log</fileNamePattern>
          </rollingPolicy>
      </appender>
  ```


```xml
  <appender name="fileErrorLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
      <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
          <level>ERROR</level>
      </filter>
      <encoder>
          <pattern>
              %msg%n
          </pattern>
      </encoder>
      <!--滚动策略-->
      <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
          <!--路径:绝对路径，需要到当前盘去找到这个日志路径-->
          <fileNamePattern>/var/log/tomcat/sell/error.%d.log</fileNamePattern>
      </rollingPolicy>
  </appender>

  <root level="info">
      <appender-ref ref="consoleLog" />
      <appender-ref ref="fileInfoLog" />
      <appender-ref ref="fileErrorLog" />
  </root>
  </configuration>
```
### 5.5 pom.xml 

  ```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cris</groupId>
    <artifactId>wx-sell</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>wx-sell</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.13.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>

        <dependency>
            <groupId>com.github.binarywang</groupId>
            <artifactId>weixin-java-mp</artifactId>
            <version>2.7.0</version>
        </dependency>

        <dependency>
            <groupId>cn.springboot</groupId>
            <artifactId>best-pay-sdk</artifactId>
            <version>1.1.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
  ```

### 5.6 yml

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.123.231/sell?characterEncoding=utf-8&useSSL=false
    driver-class-name: com.mysql.jdbc.Driver
  jpa:
    show-sql: true
```



## 6. 类目开发

### 6.1 dao 层开发和测试

#### 一、删除不必要的文件和目录

![1527297180825](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527297180825.png)



#### 二、更新我们的maven 本地仓库，这样写dependency 的时候就有自动提示了

[参考](https://my.oschina.net/u/225373/blog/425935)

#### 三、生成entity以及对应的repository 接口，并测试（以类目表为例）

##### entity

```java
/**
 * @ClassName ProductCategory
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Entity
@DynamicUpdate      // 自动更新数据修改后的时间到数据库，非常好用
@Data                // getter，setter以及toString 方法自动生成
@Accessors(chain=true)  // 可以链式调用自动生成的模板方法
@NoArgsConstructor  // 无参构造器
@AllArgsConstructor // 全参构造器
public class ProductCategory {

    // 类目id
    @Id
    @GeneratedValue
    private Integer categoryId;
    // 类目名字
    private String categoryName;
    // 类目编号
    private Integer categoryType;
    // 创建时间
    private Date createTime;
    // 数据更新时间
    private Date updateTime;

}
```

##### 对应的repository

```java
public interface ProductCategoryRepository extends JpaRepository<ProductCategory, Integer> {}
```

##### 单元测试

![1](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets/1.png)

![1527300080238](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527300080238.png)

- 查询数据测试

![1527300136800](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527300136800.png)

![1527300236648](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527300236648.png)



- 插入数据测试

  ![1527300391025](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527300391025.png)

![1527300446463](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527300446463.png)

![1527300971197](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527300971197.png)

![1527301038316](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527301038316.png)



- 修改数据测试

  ​	![1527301271249](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527301271249.png)

  ![1527301280836](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527301280836.png)

  ​

  -  根据category_type 集合查询对应的category 数据

    ![1527301911185](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527301911185.png)

    ![1527301897867](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527301897867.png)





### 6.2 service 层开发和测试

#### 一、接口设计

```java
/**
 * @ClassName ProductCategoryService
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
public interface ProductCategoryService {

    ProductCategory get(Integer id);

    List<ProductCategory> list();

    // 根据类目编号集合查询对应的类目数据集合
    List<ProductCategory> findByCategoryTypeIn(List<Integer> list);

    // 新增和更新都是这个方法
    ProductCategory save(ProductCategory productCategory);


}
```

#### 二、接口实现设计

```java
/**
 * @ClassName ProductCategoryServiceImpl
 * @Description 注意：这里我们的service 方法名不和dao 方法保持，
 *              而是和controller 方法保持一致，以及和rest 风格的uri保持一致
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class ProductCategoryServiceImpl implements ProductCategoryService {

    @Autowired
    private ProductCategoryRepository repository;

    @Override
    public ProductCategory get(Integer id) {
        return repository.findOne(id);
    }

    @Override
    public List<ProductCategory> list() {
        return repository.findAll();
    }

    @Override
    public List<ProductCategory> findByCategoryTypeIn(List<Integer> list) {
        return repository.findByCategoryTypeIn(list);
    }

    @Override
    public ProductCategory save(ProductCategory productCategory) {
        return repository.save(productCategory);
    }
}
```

#### 三、快捷生成service实现类的测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ProductCategoryServiceImplTest {

    @Autowired
    private ProductCategoryService service;

    @Test
    public void get() {
        ProductCategory productCategory = service.get(1);
        Assert.assertEquals(new Integer(1), productCategory.getCategoryId());
    }

    @Test
    public void list() {
        List<ProductCategory> list = service.list();
        Assert.assertNotEquals(0, list.size());
    }

    @Test
    public void findByCategoryTypeIn() {
        List<ProductCategory> categoryTypeIn = service.findByCategoryTypeIn(Arrays.asList(1, 2, 3, 4));
        Assert.assertNotEquals(0, categoryTypeIn.size());
    }

    @Test
    public void save() {
        ProductCategory category = service.save(new ProductCategory().setCategoryName("屌丝升级套餐").setCategoryType(4));
        Assert.assertNotNull(category);
    }
```

![1527304246774](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527304246774.png)

![1527304255716](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527304255716.png)

### 6.3 类目开发业务总结（重点）

- **根据类目id查询一个类目数据**
- **查询所有类目数据**
- **根据传入的类目编号集合查询对应的类目数据集合**
- **新增和修改一个类目数据**



## 7. 商品开发

### 7.1 entity

```java
@Entity
@Data
@DynamicUpdate
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain=true)
public class ProductInfo {

    @Id
    private String productId;

    /** 名字. */
    private String productName;

    /** 单价. */
    private BigDecimal productPrice;

    /** 库存. */
    private Integer productStock;

    /** 描述. */
    private String productDescription;

    /** 小图. */
    private String productIcon;

    /** 状态, 0正常1下架. */
    private Integer productStatus;

    /** 类目编号. */
    private Integer categoryType;

    private Date createTime;

    private Date updateTime;
}
```

### 7.2 repository

```java
public interface ProductInfoRepository extends JpaRepository<ProductInfo, String> {

    // 查询所有上架的商品
    List<ProductInfo> findByProductStatus(Integer productStatus);
}
```

### 7.3 repository 单元测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ProductInfoRepositoryTest {

    @Autowired
    private ProductInfoRepository repository;

    @Test
    public void saveTest() {
        ProductInfo productInfo = repository.save(new ProductInfo().setProductName("牛奶")
                .setCategoryType(3).setProductDescription("一天一瓶奶，强壮中国人")
                .setProductIcon("http://xxx.jpg").setProductId("123")
                .setProductPrice(new BigDecimal(3.4)).setProductStock(100)
                .setProductStatus(0)
        );
        Assert.assertNotNull(productInfo);
    }

    @Test
    public void findByProductStatus() {
        List<ProductInfo> productInfos = repository.findByProductStatus(0);
        Assert.assertNotEquals(0, productInfos.size());
        productInfos.forEach(System.out::println);

    }
}
```

![1527306413271](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527306413271.png)

![1527306446934](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527306446934.png)



### 7.4 service 开发

```java
public interface ProductInfoService {

    ProductInfo get(String id);

    // 用户查询所有已上架商品
    List<ProductInfo> listUp();

    // 店家查询所有商品信息(分页)
    Page<ProductInfo> list(Pageable pageable);

    ProductInfo save(ProductInfo info);

    // 加库存

    // 减库存
}

/**
 * @ClassName ProductInfoServiceImpl
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class ProductInfoServiceImpl implements ProductInfoService {

    @Autowired
    private ProductInfoRepository repository;

    @Override
    public ProductInfo get(String id) {
        // 特别要注意repository 不要写成getOne 方法，因为getOne 返回的是代理
        return repository.findOne(id);
    }

    @Override
    public List<ProductInfo> listUp() {
        return repository.findByProductStatus(ProductStatusEnum.UP.getCode());
    }

    @Override
    public Page<ProductInfo> list(Pageable pageable) {
        return repository.findAll(pageable);
    }

    @Override
    public ProductInfo save(ProductInfo info) {
        return repository.save(info);
    }
}
```

### 7.5 枚举类（商品状态）

```java
// 商品状态
@Getter
public enum ProductStatusEnum {
    UP(0, "在架"),
    DOWN(1, "下架");

    private Integer code;

    private String message;

    ProductStatusEnum(Integer code, String message) {
        this.code = code;
        this.message = message;
    }
}
```

### 7.6 service 单元测试

```java
@SpringBootTest
@RunWith(SpringRunner.class)
@Slf4j
public class ProductInfoServiceImplTest {

    @Autowired
    private ProductInfoService service;

    @Test
    public void get() {
        ProductInfo productInfo = service.get("123");
        log.info(productInfo.toString());
        Assert.assertEquals("123", productInfo.getProductId());
    }


    @Test
    public void listUp() {
        List<ProductInfo> productInfos = service.listUp();
        Assert.assertNotEquals(0, productInfos.size());
    }

    @Test
    public void list() {
        Page<ProductInfo> productInfoPage = service.list(new PageRequest(0, 2));
        Assert.assertNotEquals(0, productInfoPage.getTotalElements());

    }

    @Test
    public void save() {
        ProductInfo productInfo = service.save(new ProductInfo().setProductName("鸡蛋")
                .setCategoryType(3).setProductDescription("不是一般的鸡下的蛋")
                .setProductIcon("http://xxx.jpg").setProductId("111")
                .setProductPrice(new BigDecimal(1.45)).setProductStock(20)
                .setProductStatus(ProductStatusEnum.DOWN.getCode())
        );
        Assert.assertNotNull(productInfo);
    }
}
```

![1527309617407](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527309617407.png)



### 7.7 controller

> 重点：controller层是我们后台和前端开始交互的地方，任何映射路径，数据格式等，都必须和前端商量清楚，共同开发出前后台交互的文档，在大家都觉得ok 的情况下，后台才允许进行controller层的开发（遵循前后端分离开发的严格规范）

#### 一、根据开发文档进行controller 层的测试

- yml定义项目名称

  ```yaml
  server:
    context-path: /sell    #  项目路径名
  ```

- VO

  ```java
  /**
   * @ClassName ResultVO
   * @Description 返回给前端的数据VO
   * @Author zc-cris
   * @Version 1.0
   **/
  @Data           // 自动生成getter，setter，toString 等方法
  @AllArgsConstructor     // 有参
  @NoArgsConstructor          // 无参
  @Accessors(chain=true)          // 链式调用
  public class ResultVO<T> {

      /*错误码*/
      private Integer code;

      /*提示信息*/
      private String msg;

      /*具体的返回内容*/
      private T data;
  }
  ```

- BuyerController

  ```java
  /**
   * @ClassName BuyerController
   * @Description TODO
   * @Author zc-cris
   * @Version 1.0
   **/
  @RestController
  @RequestMapping("/buyer/product")
  public class BuyerController {

      @GetMapping("/list")
      public ResultVO list(){
          return new ResultVO();
      }
  }
  ```

- 测试合格才可以进行下面的开发

  ![1527325639167](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527325639167.png)

  ![1527325679658](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527325679658.png)

  ​

- 进一步测试

  - 继续组建返回给前端的数据格式

    ![1527327812853](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527327812853.png)

    ```java
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @Accessors(chain = true)
    public class ResultData {

        // 类目名称
        @JsonProperty("name")           // 指定返回给前端的json数据的键
        private String categoryName;

        // 类目类型
        @JsonProperty("type")
        private Integer categoryType;

        // 具体的商品信息(集合)
        @JsonProperty("foods")
        private List<ResultProductInfo> foods = new ArrayList();
    }
    ```


    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    @Accessors(chain = true)
    public class ResultProductInfo {
    
        // 严格按照开发文档的说明传给前端规范的数据
        @JsonProperty("id")
        private String productId;
    
        @JsonProperty("name")
        private String productName;
    
        @JsonProperty("price")
        private BigDecimal productPrice;
    
        @JsonProperty("description")
        private String productDescription;
    
        @JsonProperty("icon")
        private String productIcon;
    }
    ```

- 继续结合controller 测试

  ```java
  @RestController
  @RequestMapping("/buyer/product")
  public class BuyerController {

      @GetMapping("/list")
      public ResultVO list(){

          ResultVO<List<ResultData>> resultVO = new ResultVO();

          List<ResultProductInfo> resultProductInfos = Arrays.asList(new ResultProductInfo().setProductId("213")
                  .setProductDescription("超级好吃").setProductIcon("xxx.jpg")
                  .setProductName("皮蛋").setProductPrice(new BigDecimal(12.23)));

          List<ResultData> resultData = Arrays.asList(new ResultData().setCategoryName("热卖榜")
                  .setCategoryType(1).setFoods(resultProductInfos));
  ```


          resultVO.setCode(0).setMsg("成功").setData(resultData);
    
          return resultVO;
      }
  }
  ```

- 测试效果

  ​

![1527327327199](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527327327199.png)

![1527327506146](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527327506146.png)



#### 二、整合数据库完成和前台的数据交互

-  util

  ```java
  /**
   * @ClassName ResultVOUtil
   * @Description TODO
   * @Author zc-cris
   * @Version 1.0
   **/
  public class ResultVOUtil {

      public static ResultVO success(Object object){
          return new ResultVO().setCode(0).setMsg("成功").setData(object);
      }

      public static ResultVO success(){
          return success(null);
      }

      public static ResultVO fail(Integer code, String msg){
          return new ResultVO().setCode(code).setMsg(msg).setData(null);
      }
  }
  ```

- 完善controller

  ```java
  /**
   * @ClassName BuyerController
   * @Description TODO
   * @Author zc-cris
   * @Version 1.0
   **/
  @RestController
  @RequestMapping("/buyer/product")
  public class BuyerController {

      @Autowired
      private ProductInfoService productInfoService;

      @Autowired
      private ProductCategoryService productCategoryService;

      @GetMapping("/list")
      public ResultVO list() {

          // 用户一进入首页就返回所有上架商品
          List<ProductInfo> productInfos = productInfoService.listUp();
          // 从所有商品中获取到类型集合（id类型）
          List<Integer> categoryList = productInfos.stream()
                  .map(e -> e.getCategoryType())
                  .distinct()             // 去重
                  .collect(Collectors.toList());

          // 根据id类型从category 表中查询出所有的类型(只查询一次)
          List<ProductCategory> categoryTypeList = productCategoryService.findByCategoryTypeIn(categoryList);
          // 数据组装(每一个类型的商品组合在一起，就是一个ResultData 对象)

          List<ResultData> resultDataList = new ArrayList<>();
          for (ProductCategory productCategory : categoryTypeList) {
              ResultData resultData = new ResultData();
              resultData.setCategoryName(productCategory.getCategoryName());
              resultData.setCategoryType(productCategory.getCategoryType());

              for (ProductInfo productInfo : productInfos) {
                // 特别注意：Integer类型的数值比较必须使用equals方法
                  if (productInfo.getCategoryType().equals(productCategory.getCategoryType())) {
                      ResultProductInfo resultProductInfo = new ResultProductInfo();
                      // 将一个对象的属性添加到另一个对象中（按照属性名对应添加）
                      BeanUtils.copyProperties(productInfo, resultProductInfo);
                      resultData.getFoods().add(resultProductInfo);
                  }
              }
              resultDataList.add(resultData);
          }

          // 成功返回的数据
          return ResultVOUtil.success(resultDataList);
              /* 以下为测试数据，必须通过以后才可以结合数据库数据进行整合测试
       // 返回给前台的整体数据对象
        ResultVO<List<ResultData>> resultVO = new ResultVO();

        // 返回给前台data属性数据里面的foods属性对应的集合
        List<ResultProductInfo> resultProductInfos = Arrays.asList(new ResultProductInfo().setProductId("213")
                .setProductDescription("超级好吃").setProductIcon("xxx.jpg")
                .setProductName("皮蛋").setProductPrice(new BigDecimal(12.23)));

        // 返回给前台的data 属性数据（集合）
        List<ResultData> resultData = Arrays.asList(new ResultData().setCategoryName("热卖榜")
                .setCategoryType(1).setFoods(resultProductInfos));

        resultVO.setCode(0).setMsg("成功").setData(resultData);

        */
    }
    }
  ```
  - 启动测试

    ![1](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets/1-1527332665343.png)

    ​

    ![1527332682578](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527332682578.png)



  #### 三、这里结合linux里的Nginx出了问题，始终无法连接上本机的8080端口

  ​

  ### 7.8 商品开发业务总结（重点）

  - **根据id查询商品数据**

  - **用户查询所有上架的商品信息**

  - **店家查询所有商品信息（分页）**

  - **店家新增或者修改一个商品信息**

  - **新增库存（见下面订单模块开发）**

  - **减库存（见下面订单模块开发）**

    ​


## 8. 订单模块的开发

### 8.1 Entity（订单主表和订单详情表）

  ```java

// 订单主表
@Entity
@Data
@DynamicUpdate
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class OrderMaster {

    /** 订单id. */
    @Id
    private String orderId;

    /** 买家名字. */
    private String buyerName;

    /** 买家手机号. */
    private String buyerPhone;

    /** 买家地址. */
    private String buyerAddress;

    /** 买家微信Openid. */
    private String buyerOpenid;

    /** 订单总金额. */
    private BigDecimal orderAmount;

    /** 订单状态, 默认为0新下单. */
    private Integer orderStatus = OrderStatusEnum.NEW.getCode();

    /** 支付状态, 默认为0未支付. */
    private Integer payStatus = PayStatusEnum.WAIT.getCode();

    /** 创建时间. */
    private Date createTime;

    /** 更新时间. */
    private Date updateTime;

}

// 订单详情表
@Entity
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class OrderDetail {

    @Id
    private String detailId;

    /** 订单id. */
    private String orderId;

    /** 商品id. */
    private String productId;

    /** 商品名称. */
    private String productName;

    /** 商品单价. */
    private BigDecimal productPrice;

    /** 商品数量. */
    private Integer productQuantity;

    /** 商品小图. */
    private String productIcon;
}
  ```

### 8.2 dao 层开发和单元测试

```java
public interface OrderDetailRepository extends JpaRepository<OrderDetail, String> {

    // 根据orderId 在订单详情表中查询出对应的商品订单详情
    List<OrderDetail> findByOrderId(String orderId);

}

public interface OrderMasterRepository extends JpaRepository<OrderMaster, String> {

    // 按照买家微信号，将其所有订单分页查询出来
    Page<OrderMaster> findByBuyerOpenid(String buyerOpenId, Pageable pageable);

}
```

- test

  - 订单主表

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class OrderMasterRepositoryTest {

        @Autowired
        private OrderMasterRepository repository;

        @Test
        public void findByBuyerOpenid() {
            PageRequest request = new PageRequest(0, 2);
            Page<OrderMaster> orderMasters = repository.findByBuyerOpenid("12233", request);
            orderMasters.getContent().forEach(System.out::println);
            // 如果要查询分页对象里到底有多少条非空白数据，就用getContent().size()即可
            Assert.assertEquals(1, orderMasters.getContent().size());
        }

        @Test
        public void SaveTest(){
            OrderMaster orderMaster = repository.save(new OrderMaster().setBuyerAddress("东京")
                    .setBuyerName("桥本有菜").setBuyerOpenid("12233")
                    .setBuyerPhone("123123").setOrderId("1234")
                    .setOrderAmount(new BigDecimal(123.21)));
            Assert.assertNotNull(orderMaster);

        }
    }
    ```

    ![1527405623988](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527405623988.png)

    ​

  - 订单详情表

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    @Slf4j
    public class OrderDetailRepositoryTest {

        @Autowired
        private OrderDetailRepository repository;

        @Test
        public void findByOrderId() {
            List<OrderDetail> byOrderId = repository.findByOrderId("123");
            byOrderId.forEach(System.out::println);
            Assert.assertNotEquals(0, byOrderId.size());
        }

        @Test
        public void saveTest(){
            OrderDetail detail = repository.save(new OrderDetail().setDetailId("123").setOrderId("123")
                    .setProductIcon("xx.jpg").setProductName("大西瓜")
                    .setProductQuantity(2).setProductPrice(new BigDecimal(5.5))
                    .setProductId("122"));
            log.info(detail.toString());
        }
    }
    ```

![1527405597117](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527405597117.png)



### 8.3 service 层的开发（绝对重点）

#### 一、构建OrderDTO

```java
/**
 * @ClassName OrderDTO
 * @Description 将每个订单项及其对应的订单详情类组合起来（用于后台各个层之间的数据传输，而VO则是controller层 和前端的数据传输）
 * @Author zc-cris
 * @Version 1.0
 **/
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class OrderDTO {

    /** 订单id. */
    @Id
    private String orderId;

    /** 买家名字. */
    private String buyerName;

    /** 买家手机号. */
    private String buyerPhone;

    /** 买家地址. */
    private String buyerAddress;

    /** 买家微信Openid. */
    private String buyerOpenid;

    /** 订单总金额. */
    private BigDecimal orderAmount;

    /** 订单状态, 默认为0新下单. */
    private Integer orderStatus;

    /** 支付状态, 默认为0未支付. */
    private Integer payStatus;

    /** 创建时间. */
    private Date createTime;

    /** 更新时间. */
    private Date updateTime;

    // 对应的订单详情集合(将有关联的两张表通过DTO的形式结合起来，而不是外键)
    private List<OrderDetail> orderDetails;
}
```

- 注意：为什么需要DTO？

  > 其实和VO一样，我们需要对数据进行业务上的定制化，以VO为例，前端需要指定格式的数据，我们后台的实体类是无法满足这种需求的，所以新建一个VO，来组装各种类型的数据，通过controller 层一并返回给前端处理（一般都是json格式）
  >
  > 而DTO 主要是负责我们后台之间数据的传输，数据都是以javaBean对象的格式，同样需要进行数据的组装，通常用于controller 和service之间的数据传输以及service 对dao的数据持久化操作；数据库有关系的两张表不是通过外键来维护关系（或者说不是数据库来维护关联关系），而是通过DTO来维护关系（逻辑维护）
  >
  > 可以这么理解：VO就是包含了前端需求的数据大集合；而DTO 是我们后台业务处理核心的数据大集合

#### 二、新建订单业务开发

##### - 订单主表service接口设计

```java
public interface OrderMasterService {

    /*创建订单*/
    OrderDTO create(OrderDTO orderDTO);

    /*查询单个订单*/
    OrderDTO get(String orderId);

    /*根据用户id查询订单列表，分页*/
    Page<OrderDTO> list(String buyerOpenid, Pageable pageable);

    /*取消订单*/
    OrderDTO cancell(OrderDTO orderDTO);

    /*完结订单*/
    OrderDTO finish(OrderDTO orderDTO);

    /*支付订单*/
    OrderDTO pay(OrderDTO orderDTO);

}
```

##### - 订单接口实现类(以创建订单为主)

1. 首先需要根据传入的OrderDTO 获取到用户这个订单主项的每个订单详情项（前台仅需要传来商品编号和数量）；
2. 然后根据商品编号从数据库查询到产品的价格信息并计算这个订单主项的总价格
3. 将每个订单详细项进行数据填充（从 2 查询出来的商品信息获取）并最终放入数据库
4. 将订单主项的数据进行填充放入数据库
5. 扣库存（商品信息表）
6. 亮点：java8 的stream API 和lambda 表达式，以及工具类新的时间API 使用，减少数据库的连接，尽量一次连接查询到所有数据，bigDecimal用于计算金额，beanUtils快速复制javaBean属性（类型和名字必须一一对应）

```java
/**
 * @ClassName OrderMasterServiceImpl
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
public class OrderMasterServiceImpl implements OrderMasterService {

    @Autowired
    private ProductInfoRepository productInfoRepository;

    @Autowired
    private OrderDetailRepository orderDetailRepository;

    @Autowired
    private OrderMasterRepository orderMasterRepository;

    @Autowired
    private ProductInfoService productInfoService;

    /*创建订单*/
    @Override
    @Transactional
    public OrderDTO create(OrderDTO orderDTO) {

        // 订单主表id
        String orderId = KeyUtil.genUniqueKey();
        // 定义订单总金额(初始为0)
        BigDecimal totalPrice = new BigDecimal(BigInteger.ZERO);

//          1. 查询商品价格（不能由前端传来价格信息），下单数量
        List<OrderDetail> orderDetails = orderDTO.getOrderDetails();
        for (OrderDetail orderDetail : orderDetails) {
            // 根据orderDTO中的每个订单详情的商品id查询到对应的商品信息
            ProductInfo productInfo = productInfoRepository.findOne(orderDetail.getProductId());
            // 如果商品不存在
            if (productInfo == null) {
                throw new SellException(ResultExceptionEnum.PRODUCT_NOT_EXIST);
            }

//          2. 计算总价（每个订单详情都应该包含这款商品的id，根据id查到价格，根据价格*购买数量获取到每个订单详情的价格；
//              再把每个订单详情的价格累加起来就是订单总金额）
            totalPrice = productInfo.getProductPrice()
                    .multiply(new BigDecimal(orderDetail.getProductQuantity()))
                    .add(totalPrice);

//          3. 写入订单数据库（orderDetail）--------->注意：开发文档中只规定了前台传来的订单详情项数据只有商品id和商品数量
            orderDetail.setDetailId(KeyUtil.genUniqueKey());
            orderDetail.setOrderId(orderId);
            BeanUtils.copyProperties(productInfo, orderDetail);
            orderDetailRepository.save(orderDetail);

        }
        // 4. 订单主表写入数据库（根据开发文档即前端传来的已有属性来写缺失的属性即可）
        OrderMaster orderMaster = new OrderMaster();
        BeanUtils.copyProperties(orderDTO, orderMaster);
        orderMaster.setOrderId(orderId);
        orderMaster.setOrderAmount(totalPrice);         // 订单总金额
        orderMaster.setOrderStatus(OrderStatusEnum.NEW.getCode());
        orderMaster.setPayStatus(PayStatusEnum.WAIT.getCode());
        orderMasterRepository.save(orderMaster);

//        5. 扣库存（容易疏忽）注意：这里使用lambda表达式（新方式，更加简洁，不要放在上面的for循环里污染代码）
        List<CartDTO> cartDTOList = orderDTO.getOrderDetails().stream()
                .map(e -> new CartDTO(e.getProductId(), e.getProductQuantity()))
                .collect(Collectors.toList());
        productInfoService.decreaseStock(cartDTOList);

        return orderDTO;
    }
	...
}
```

##### - 生成主键工具类

```java
/**
 * @ClassName KeyUtil
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
public class KeyUtil {

    /**
    * @Author zc-cris
    * @Description 生成随机的唯一主键（String 类型）：时间毫秒数+随机数
    * @Param []
    * @return java.lang.String
    **/
    public static synchronized String genUniqueKey(){

        int i = new Random().nextInt(1000000);

        return Instant.now().toEpochMilli() + String.valueOf(i);
    }
}
```

##### - 定义异常类

```java
/**
 * @ClassName SellException
 * @Description 定义各种异常信息
 * @Author zc-cris
 * @Version 1.0
 **/
public class SellException extends RuntimeException {

    private Integer code;

    // 将具体的异常信息枚举类传递进来
    public SellException(ResultExceptionEnum resultExceptionEnum) {
        super(resultExceptionEnum.getMsg());
        this.code = resultExceptionEnum.getCode();
    }
}
```

##### - 定义下订单时的异常枚举类型

```java
/*用枚举来表示各种下订单时的异常信息*/
@Getter
public enum ResultExceptionEnum {

    PRODUCT_NOT_EXIST(10, "商品不存在"), PRODUCT_STOCK_ERROR(11, "库存错误");

    private Integer code;

    private String msg;

    private ResultExceptionEnum(Integer code, String msg){
        this.code = code;
        this.msg = msg;
    }
}
```

##### - 定义订单状态枚举类

```java
/**
 * @ClassName OrderStatusEnum
 * @Description 订单状态
 * @Author zc-cris
 * @Version 1.0
 **/
@Getter
public enum  OrderStatusEnum {
    NEW(0, "新订单"),
    FINISHED(1, "已完结"),
    CANCELL(2, "已取消");

    private Integer code;
    private String status;

    private OrderStatusEnum(Integer code, String status){
        this.code = code;
        this.status = status;
    }

}
```

##### - 定义支付状态枚举类

```java
@Getter
public enum PayStatusEnum {
    WAIT(0, "未支付"),
    SUCCESS(1, "支付成功")
    ;

    private Integer code;
    private String msg;

    private PayStatusEnum (Integer code, String msg){
        this.code = code;
        this.msg = msg;
    }
}
```

##### - 为了去库存，创建购物车类

```java
/**
 * @ClassName CartDTO
 * @Description 购物车(仅仅只是前台传来的商品id和商品数量即可)
 * @Author zc-cris
 * @Version 1.0
 **/
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class CartDTO {

    /*商品id*/
    private String productId;

    /*商品数量*/
    private Integer productQuantity;

}
```

##### - 关于购物车类的说明

- 我们这里创建的购物车主要就两个属性：商品id（从数据库查询商品信息）；用户购买的商品数量（用于减库存）
- 而这两个属性都是前台传递给我们后台的，也就是说，后台业务逻辑只在乎用户每个订单详情项买了什么东西，买了多少个即可，其他数据后台业务逻辑都是可以处理的
- 关于前端传来的订单详情信息（商品id+商品数量）建议直接使用orderDetail 封装，而不是购物车CartDTO，因为方便我们进行订单详情类的创建和入数据库；而购物车CartDTO方便我们进行库存的计算（其实前台传来的订单详情信息就是用户购物车里的信息，而用户购物车的信息其实在用户第一次访问首页的时候就已经发送给前端了）

##### - 扣库存(由商品信息模块处理)

```java
@Service
public class ProductInfoServiceImpl implements ProductInfoService {

    @Autowired
    private ProductInfoRepository repository;
		...
      
    @Override
    @Transactional
    public void decreaseStock(List<CartDTO> cartDTOList) {
        for (CartDTO cartDTO : cartDTOList) {
            ProductInfo productInfo = repository.findOne(cartDTO.getProductId());
            if(productInfo == null){
                throw new SellException(ResultExceptionEnum.PRODUCT_NOT_EXIST);
            }
            int result = productInfo.getProductStock() - cartDTO.getProductQuantity();
            if(result < 0){
                throw new SellException(ResultExceptionEnum.PRODUCT_STOCK_ERROR);
            }
            productInfo.setProductStock(result);
            repository.save(productInfo);       // 千万别忘了这一步
        }
    }
}
```

##### - 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class OrderMasterServiceImplTest {

    @Autowired
    private OrderMasterService orderMasterService;

    @Test
    public void create() {
        OrderDTO orderDTO = new OrderDTO();
        orderDTO.setBuyerAddress("北京");
        orderDTO.setBuyerName("cris");
        orderDTO.setBuyerOpenid("123");
        orderDTO.setBuyerPhone("110");

        List<OrderDetail> orderDetailList = new ArrayList<>();
        // 以鸡蛋为例
        orderDetailList.add(new OrderDetail().setProductId("111").setProductQuantity(2));

        orderDTO.setOrderDetails(orderDetailList);
        OrderDTO orderDTO1 = orderMasterService.create(orderDTO);
        log.info("订单主表 ： {}"+ orderDTO1);

    }
  ...
}
```

- 订单主表

![1527407997853](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527407997853.png)

- 订单详情表

![1527408027819](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527408083921.png)

- 商品信息表

  ![1527408192704](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527408192704.png)

##### - 测试购买两件商品

```java
        // 再加个大西瓜
        orderDetailList.add(new OrderDetail().setProductId("122").setProductQuantity(2));
```

![1527408424825](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527408424825.png)

![1527408498913](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527408498913.png)

![1527408568484](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527408568484.png)

#### 三、根据id查询订单项及其订单详细项业务开发

##### - 订单service 接口方法完善

```java
    /*根据订单id查询订单及其对应的订单详情*/
    @Override
    public OrderDTO get(String orderId) {

        OrderMaster orderMaster = orderMasterRepository.findOne(orderId);
        if(orderMaster == null){
            throw new SellException(ResultExceptionEnum.ORDER_NOT_EXIST);
        }

        List<OrderDetail> orderDetails = orderDetailRepository.findByOrderId(orderId);
        if(CollectionUtils.isEmpty(orderDetails)){
            throw new SellException(ResultExceptionEnum.ORDER_DETAIL_NOT_EXIST);
        }

        OrderDTO orderDTO = new OrderDTO();
        // 将订单和订单详情组装到OrderDTO
        BeanUtils.copyProperties(orderMaster, orderDTO);
        orderDTO.setOrderDetails(orderDetails);

        return orderDTO;
    }
```

##### - 枚举类

```java
/*用枚举来表示各种下订单时的异常信息*/
@Getter
public enum ResultExceptionEnum {

    PRODUCT_NOT_EXIST(10, "商品不存在"),
    PRODUCT_STOCK_ERROR(11, "库存错误"),
    ORDER_NOT_EXIST(12, "订单不存在"),
    ORDER_DETAIL_NOT_EXIST(13, "订单详情不存在"),
    ;
  
    private Integer code;

    private String msg;

    private ResultExceptionEnum(Integer code, String msg){
        this.code = code;
        this.msg = msg;
    }
}
```

##### - 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class OrderMasterServiceImplTest {

    @Autowired
    private OrderMasterService orderMasterService;

    private final String openId = "123";

    private final String orderId = "1527408317970918560";	   
		@Test
    public void get() {
        OrderDTO orderDTO = orderMasterService.get(orderId);
        log.info("订单信息为 result={}", orderDTO);
        Assert.assertEquals(orderId, orderDTO.getOrderId());
    }
```

#### 四、根据用户的openId查询对应的订单项数据业务开发

##### - 接口方法实现

```java
    /*根据用户id查询订单列表，分页*/
    @Override
    public Page<OrderDTO> list(String buyerOpenid, Pageable pageable) {

        Page<OrderMaster> orderMasters = orderMasterRepository.findByBuyerOpenid(buyerOpenid, pageable);

        // 将orderMasters 里面的OrderMaster 转换为OrderDTO，需要特意写一个转换器 Ordermaster2OrderDTOConverter
        // 分页对象里面的数据格式转换
        Page<OrderDTO> page = new PageImpl<OrderDTO>
                (Ordermaster2OrderDTOConverter.convertList(orderMasters.getContent())
                        , pageable
                        , orderMasters.getTotalElements());

        return page;
    }
```

##### - 重点是自定义的转换器

```java
/**
 * @ClassName Ordermaster2OrderDTOConverter
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
public class Ordermaster2OrderDTOConverter {

    public static OrderDTO convert(OrderMaster orderMaster){
        OrderDTO target = new OrderDTO();
        BeanUtils.copyProperties(orderMaster, target);
        return target;
    }

    /*将OrderMaster 类型的list ---》OrderDTO 类型的list*/
  	// java8的stream和lambda
    public static List<OrderDTO> convertList(List<OrderMaster> orderMasterList){
        return orderMasterList.stream()
                .map(e -> convert(e))
                .collect(Collectors.toList());
    }

}
```

##### - 测试

```java
    @Test
    public void list() {
        PageRequest request = new PageRequest(0,2);
        Page<OrderDTO> page = orderMasterService.list(openId, request);
        page.getContent().forEach(System.out::println);
        Assert.assertNotEquals(0, page.getContent().size());
    }
```

#### 五、取消订单业务开发

##### - 接口实现

```java
    /*用户取消订单*/
    @Override
    @Transactional
    public OrderDTO cancell(OrderDTO orderDTO) {
        // 判断订单状态(如果不是新建的订单，抛出无法取消的异常)
        if (!orderDTO.getOrderStatus().equals(OrderStatusEnum.NEW.getCode())) {
            log.error("该订单无法取消，orderDTO={}, orderStatus={}", orderDTO, orderDTO.getOrderStatus());
            throw new SellException(ResultExceptionEnum.ORDER_CANNOT_CANCELL);
        }

        // 修改订单状态有两种处理方法：
        // 1. 从数据库查询到订单然后设置订单状态，最后保存到数据库---缺点：查询两次数据库；优点：简单，方便
        /*OrderMaster orderMaster = orderMasterRepository.findOne(orderDTO.getOrderId());
        orderMaster.setOrderStatus(OrderStatusEnum.CANCELL.getCode());*/

        // 2. 将OrderDTO 转换为OrderMaster，然后调用一次数据库即可完成--
        // 优点：查询一次数据库，快；缺点：使用BeanUtils很容易出错，一定要注意顺序
        // 注意：用户取消订单的时候一定会先查询看看自己的订单，这个时候订单id就已经查询出来了，没有必要使用第一种方式还去查询一次
        // 建议使用第二种
        OrderMaster orderMaster = new OrderMaster();
        orderDTO.setOrderStatus(OrderStatusEnum.CANCELL.getCode());
        BeanUtils.copyProperties(orderDTO, orderMaster);
        OrderMaster updateOrderMaster = orderMasterRepository.save(orderMaster);
        if(updateOrderMaster == null){
            log.error("取消订单失败，订单：{}", orderMaster);
            throw new SellException(ResultExceptionEnum.ORDER_CANCELL_FAIL);
        }

        // 返回库存（重点）
        if(CollectionUtils.isEmpty(orderDTO.getOrderDetails())){
            log.error("订单中没有订单详情项，orderDTO：{}", orderDTO);
            throw new SellException(ResultExceptionEnum.ORDER_DETAIL_NOT_FIND);
        }

        List<CartDTO> cartDTOList = orderDTO.getOrderDetails().stream()
                .map(e -> new CartDTO(e.getProductId(), e.getProductQuantity()))
                .collect(Collectors.toList());

        productInfoService.increaseStock(cartDTOList);

        // 如果已经支付，退款
        if(orderDTO.getPayStatus().equals(PayStatusEnum.SUCCESS.getCode())){
            //TODO      支付退款暂时先放着
        }

        return orderDTO;
    }
```

##### - 枚举类设计

```java
/*用枚举来表示各种下订单时的异常信息*/
@Getter
public enum ResultExceptionEnum {

    PRODUCT_NOT_EXIST(10, "商品不存在"),
    PRODUCT_STOCK_ERROR(11, "库存错误"),
    ORDER_NOT_EXIST(12, "订单不存在"),
    ORDER_DETAIL_NOT_EXIST(13, "订单详情不存在"),
    ORDER_CANNOT_CANCELL(14, "订单无法取消"),
    ORDER_CANCELL_FAIL(15, "订单取消失败"),
    ORDER_DETAIL_NOT_FIND(16, "订单详情项不存在"),
    ;

    private Integer code;

    private String msg;

    private ResultExceptionEnum(Integer code, String msg){
        this.code = code;
        this.msg = msg;
    }

}
```

##### - 加库存设计

```java
    @Override
    @Transactional
    public void increaseStock(List<CartDTO> cartDTOList) {
        for (CartDTO cartDTO : cartDTOList) {
            ProductInfo productInfo = repository.findOne(cartDTO.getProductId());
            if(productInfo == null){
                throw new SellException(ResultExceptionEnum.PRODUCT_NOT_EXIST);
            }
            int result = productInfo.getProductStock() + cartDTO.getProductQuantity();
            productInfo.setProductStock(result);
            repository.save(productInfo);        // 千万别忘了这一步
        }
    }
```

##### - 测试

- 测试之前

![1527415958213](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527415958213.png)

![1527416002958](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527416002958.png)

![1527416056231](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527416056231.png)

- 测试之后

  ![1527416704731](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527416704731.png)

  ![1527416733076](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527416733076.png)

  ##### 

#### 六、 完结订单业务开发

##### - 接口方法实现

```java
    /*完结订单*/
    @Override
    @Transactional
    public OrderDTO finish(OrderDTO orderDTO) {
        // 判断订单状态：只有新下的订单才可以进行完结操作
        if (!orderDTO.getOrderStatus().equals(OrderStatusEnum.NEW.getCode())){
            log.error("不是新建的订单，无法进行完结操作，orderId：{}, orderDTO:{}", orderDTO.getOrderId(), orderDTO);
            throw new SellException(ResultExceptionEnum.ORDER_CAN_NOT_FINISH);
        }

        // 修改订单状态
        OrderMaster orderMaster = new OrderMaster();
        orderDTO.setOrderStatus(OrderStatusEnum.FINISHED.getCode());
        BeanUtils.copyProperties(orderDTO, orderMaster);
        OrderMaster update = orderMasterRepository.save(orderMaster);
        if(update == null){
            throw new SellException(ResultExceptionEnum.ORDER_UPDATE_FAIL);
        }

        return orderDTO;
    }
```

##### - 测试

```java
    @Test
    public void finish() {
        OrderDTO orderDTO = orderMasterService.get("1527408317970918560");
        OrderDTO update = orderMasterService.finish(orderDTO);
        Assert.assertEquals(OrderStatusEnum.FINISHED.getCode(), update.getOrderStatus());

    }
```

##### - 测试效果图

![1527433481069](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527433481069.png)



#### 七、订单支付业务开发

##### - 接口完善

```java
    /*订单支付业务*/
    @Override
    @Transactional
    public OrderDTO pay(OrderDTO orderDTO) {
        // 判断订单状态
        if (!orderDTO.getOrderStatus().equals(OrderStatusEnum.NEW.getCode())) {
            log.error("订单不是新建订单，无法完成支付，orderDTO：{}", orderDTO);
            throw new SellException(ResultExceptionEnum.ORDER_CAN_NOT_PAY);
        }

        // 判断订单支付状态
        if (!orderDTO.getPayStatus().equals(PayStatusEnum.WAIT.getCode())) {
            log.error("该订单不处于等待支付状态，请核查，orderDTO：{}", orderDTO);
            throw new SellException(ResultExceptionEnum.ORDER_CAN_NOT_PAY);
        }

        // 完成订单支付
        orderDTO.setPayStatus(PayStatusEnum.SUCCESS.getCode());
        OrderMaster orderMaster = new OrderMaster();
        BeanUtils.copyProperties(orderDTO, orderMaster);
        OrderMaster updateResult = orderMasterRepository.save(orderMaster);
        if (updateResult == null) {
            log.error("订单支付失败，orderDTO：{}", orderDTO);
            throw new SellException(ResultExceptionEnum.ORDER_PAY_FAIL);
        }

        return orderDTO;
    }
```

##### - 测试

```java
    @Test
    public void pay() {
        OrderDTO orderDTO = orderMasterService.get("1527408317970918560");
        OrderDTO result = orderMasterService.pay(orderDTO);
        Assert.assertEquals(PayStatusEnum.SUCCESS.getCode(), orderDTO.getPayStatus());
    }
```

##### - 效果图

![1527434436206](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527434436206.png)



### 8.4 controller 层的开发（重点）

#### 一、新建订单开发

##### - controller 层方法实现

```java
/**
 * @ClassName BuyerOrderController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
@RequestMapping("/buyer/order")
@Slf4j
public class BuyerOrderController {

    @Autowired
    private OrderMasterService orderMasterService;

    // 创建订单
    @PostMapping("/create")
    public ResultVO<Map<String, String>> create(@Valid OrderForm orderForm, BindingResult bindingResult){
        // orderForm 中的数据验证失败了
        if(bindingResult.hasErrors()){
            log.error("orderForm 表单数据验证失败：orderForm = {}", orderForm);
            throw new SellException(ResultExceptionEnum.ORDER_FORM_VALIDATION_FAIL,
                    bindingResult.getFieldError().getDefaultMessage());
        }

        // service创建订单需要一个OrderDTO类型的数据，但是前台传来的是OrderForm 类型的数据，所以又要进行数据的转换
        OrderDTO orderDTO = OrderForm2OrderDTO.convert(orderForm);
        if(CollectionUtils.isEmpty(orderDTO.getOrderDetails())){
            log.error("购物车不能为空，orderForm.getItems = {}", orderForm.getItems());
            throw new SellException(ResultExceptionEnum.CART_EMPTY);
        }

        // 创建订单
        OrderDTO result = orderMasterService.create(orderDTO);

        // 返回给前台的数据
        // 简单的{...}就用map，复杂就用对象，效果一样
        Map<String, String> map = new HashMap<>();
        map.put("orderId", result.getOrderId());
        return ResultVOUtil.success(map);
    }


    // 订单列表

    // 订单详情

    // 取消订单
   
}
```

##### - 封装前端数据的orderFrom

```java
/**
 * @ClassName OrderForm
 * @Description 接收前台传来的order 数据封装成对象
 * @Author zc-cris
 * @Version 1.0
 **/
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class OrderForm {

    /*尽量前台传来的数据json格式，并且键的名字和我们后台的DTO的属性名字保持一致，
    而DTO的属性名字又尽量和Entity保持一致，而Eentity的属性名就是数据表的字段名*/

    @NotNull(message = "用户名必填")
    private String name;

    @NotNull(message = "手机号必填")
    private String phone;

    @NotNull(message = "地址必填")
    private String address;

    @NotNull(message = "微信号必填")
    private String openid;

    /*前台传来的订单数据中的购物车项，其实只有商品id和商品数量*/
    @NotNull(message = "购物车不能为空")
    private String items;
}
```

##### - 数据转换类：OrderForm2OrderDTO(重点)

```java
/**
 * @ClassName OrderForm2OrderDTO
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Slf4j
public class OrderForm2OrderDTO {

    public static OrderDTO convert(OrderForm orderForm){
        OrderDTO orderDTO = new OrderDTO();

        orderDTO.setBuyerName(orderForm.getName());
        orderDTO.setBuyerPhone(orderForm.getPhone());
        orderDTO.setBuyerAddress(orderForm.getAddress());
        orderDTO.setBuyerOpenid(orderForm.getOpenid());

        // 这里使用google 的gson来进行字符串到list集合的数据转换
        Gson gson = new Gson();
        List<OrderDetail> details = new ArrayList<>();
        try {
            // 使用gson转换数据，原json格式的数据一定要格式正确，并且键必须和要转换的数据
            details = gson.fromJson(orderForm.getItems(),
                    new TypeToken<List<OrderDetail>>() {
                    }.getType());
        }catch (Exception e){
            log.error("数据转换失败，goson无法解析这个数据：{}", orderForm.getItems());
            throw new SellException(ResultExceptionEnum.ORDER_FORM_ITEM_CONVERT_JSON_FAIL);
        }

        orderDTO.setOrderDetails(details);
        return orderDTO;
    }
}
```

##### - 前台数据的封装和后台返回给前端的数据都是开发文档定义好的，需要严格按照开发文档编写

##### - 流程图

![1527489742533](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527489742533.png)

##### - 测试

![1527490272388](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527490272388.png)

![1527490288330](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527490288330.png)

![1527490403510](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527490403510.png)

![1527604057098](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527604057098.png)



#### 二、订单列表查询

##### - controller代码

```java
    // 订单列表
    @GetMapping("/list")
    public ResultVO<List<OrderDTO>> list(@RequestParam(value = "openid") String openId,
                                         @RequestParam(value = "page", defaultValue = "0") Integer page,
                                         @RequestParam(value = "size", defaultValue = "5") Integer size){
        //  注意这里使用StringUtils判断字符串是否为空或者""
        if (StringUtils.isEmpty(openId)){
            log.error("查询订单列表时，openId为空");
            throw new SellException(ResultExceptionEnum.OPENID_IS_NULL);
        }
        PageRequest request = new PageRequest(page, size);
        Page<OrderDTO> orderDTOS = orderMasterService.list(openId, request);

        return ResultVOUtil.success(orderDTOS.getContent());
    }
```

##### - 测试

![1527606325587](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527606325587.png)

##### - 开发小技巧之数据格式问题（时间）

因为开发文档要求传给前端的时间数据需要是秒为单位，我们这里默认是毫秒为单位，那么应该如何快速的解决呢？

新建一个工具类，专门用来在将java对象转换为json数据的时候的数据转换

```java
/**
 * @ClassName Date2LongSerializer
 * @Description 开发小技巧，如果前端要求返回的数据和我们javaBean的数据格式不一致，可以通过以下方法修改
 *              // 示例：默认Date类型通过Spring MVC转换为json是毫秒单位，前端要求是秒为单位
 * @Author zc-cris
 * @Version 1.0
 **/
public class Date2LongSerializer extends JsonSerializer<Date> {

    @Override
    public void serialize(Date date, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException, JsonProcessingException {
        jsonGenerator.writeNumber(date.getTime()/1000);
    }
}
```

然后在我们的java对象属性上加上注解

```java
    /** 创建时间. */
    @JsonSerialize(using = Date2LongSerializer.class)
    private Date createTime;

    /** 更新时间. */
    @JsonSerialize(using = Date2LongSerializer.class)
    private Date updateTime;
```

测试

![1527606585875](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527606585875.png)

##### - 开发小技巧之返回null值处理

注意：上面返回的数据，“orderDetails”为null，实际开发中，一般返回值如果为null的数据是不用返回的，解决方案：

![1527606961734](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527606961734.png)

- 测试

  ![1527607084232](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527607084232.png)

- **更好的解决方案（全局配置）**

  ![1527607229454](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527607229454.png)

  测试同上

  注意：如果开发文档规定有的数据必须返回，如果没有值取默认值，那么这个时候又怎么处理呢？

  答：很简单，在java对象的属性上赋予初始值，如String 设置 "" 即可

#### 三、查询订单详情

##### - controller代码

```java
    // 订单详情
    @GetMapping("/get")
    public ResultVO get(@RequestParam(value = "openid") String openId,
                        @RequestParam(value = "orderId") String orderId){

        // TODO 不安全的做法，待改进
        OrderDTO orderDTO = orderMasterService.get(orderId);
        return ResultVOUtil.success(orderDTO);

    }
```

##### - 测试

![1527608067579](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527608067579.png)



#### 四、取消订单

##### - controller代码

```java
    // 取消订单
    @PostMapping("/cancel")
    public ResultVO cancel(@RequestParam(value = "openid") String openId,
                           @RequestParam(value = "orderId") String orderId){
        //TODO 不安全的做法，待改进
        OrderDTO orderDTO = orderMasterService.get(orderId);
        orderMasterService.cancel(orderDTO);
        return ResultVOUtil.success();
    }
```



##### - 测试

![1527608871205](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527608871205.png)



![1527608937654](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527608937654.png)

![1527608981603](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527608981603.png)

![1527609043408](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527609043408.png)

#### 五、 获取订详情和取消订单完善

##### - 因为获取订单详情和取消订单前台都传来了用户openid，其目的就是为了安全验证，必须当前要操作的订单的用户openid就是前台传来的用户openid（确定是本人操作）才可以继续往下执行

##### - 改善方法（将order service的处理逻辑查询订单和取消订单放入到buyer service中处理）

```java
public interface BuyerService {

    public OrderDTO getOrderDtoOne(String openId, String orderId);

    public void cancelOrder(String openId, String orderId);

}

/**
 * @ClassName BuyerServiceImpl
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
@Slf4j
public class BuyerServiceImpl implements BuyerService {

    @Autowired
    private OrderMasterService orderMasterService;

    // 将取消订单的业务逻辑放在这里来；因为要判断前台传来的订单号是否属于传来的用户openid，加强安全性
    // 这里注入订单service的查询业务，将其包裹
    @Override
    public OrderDTO getOrderDtoOne(String openId, String orderId) {
        return checkOrder(openId, orderId);
    }

    /*检查订单是否和前台传来的用户openid相匹配*/
    private OrderDTO checkOrder(String openId, String orderId) {
        OrderDTO orderDTO = orderMasterService.get(orderId);
        if (orderDTO == null) {
            return null;
        }
        if (!orderDTO.getBuyerOpenid().equalsIgnoreCase(openId)) {
            log.error("【查询订单及其详情】出错：当前订单不是该用户所有，order={}，openid={}", orderDTO, openId);
        }
        return orderDTO;
    }

    /*取消订单也要进行用户验证*/
    @Override
    public void cancelOrder(String openId, String orderId) {
        OrderDTO orderDTO = checkOrder(openId, orderId);
        if(orderDTO == null){
            log.error("【取消订单】出错：无法根据orderId={}，找到订单", orderId);
            throw new SellException(ResultExceptionEnum.ORDER_NOT_EXIST);
        }
        // 调用order 业务层的取消订单方法
        orderMasterService.cancel(orderDTO);
    }
}
```

##### - contoller改善

```java
    // 订单详情
    @GetMapping("/get")
    public ResultVO get(@RequestParam(value = "openid") String openId,
                        @RequestParam(value = "orderId") String orderId){
         /*
        OrderDTO orderDTO = orderMasterService.get(orderId);
        return ResultVOUtil.success(orderDTO);*/

         // 改进方法
        OrderDTO orderDtoOne = buyerService.getOrderDtoOne(openId, orderId);
        return ResultVOUtil.success(orderDtoOne);

    }

    // 取消订单
    @PostMapping("/cancel")
    public ResultVO cancel(@RequestParam(value = "openid") String openId,
                           @RequestParam(value = "orderId") String orderId){
        /*OrderDTO orderDTO = orderMasterService.get(orderId);
        orderMasterService.cancel(orderDTO);*/

        // 改进方法
        buyerService.cancelOrder(openId, orderId);
        return ResultVOUtil.success();
    }
```

## 9. 微信授权（通过第三方SDK完成）

#### 9.1 网页授权域名

![1527612891854](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527612891854.png)

![1527612909930](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527612909930.png)

- **必须按照微信文档一步一步开发**

  ![1527613050238](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613050238.png)

  ![1527613154688](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613154688.png)

  ![1527613189476](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613189476.png)

  ![1527613249119](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613249119.png)

  ![1527613346913](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613346913.png)

  ![1527613402573](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613402573.png)

  ![1527613428749](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613428749.png)



#### 9.2 获取微信服务器的code

![1527613568535](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613568535.png)

![1527613696502](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613696502.png)

![1527613767721](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527613767721.png)

#### 9.3 获取用户openid

![1527614035779](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527614035779.png)

![1527614246765](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527614246765.png)

![1527614319583](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527614319583.png)



#### 9.4 快速获取用户openid（借助免费的域名软件NATAPP和微信公众测试号）

![1527692503345](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692503345.png)

![1527692586471](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692586471.png)

![1527692645744](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692645744.png)

![1527692712880](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692712880.png)

![1527692793739](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692793739.png)

![1527692821846](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692821846.png)

![1527692835705](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692835705.png)

![1527692878372](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527692878372.png)

![1527693013129](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527693013129.png)

```xml
https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx37e872e99dc23959&redirect_uri=http://xgfmnn.natappfree.cc/sell/weixin/auth&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect
```

```java
/**
 * @ClassName WeiXinController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@RestController
@RequestMapping("/weixin")
@Slf4j
public class WeiXinController {

    @GetMapping("/auth")
    public void auth(@RequestParam("code") String code){
        log.info("auth方法被调用");
        log.info("code={}", code);
        RestTemplate restTemplate = new RestTemplate();
        String url = "https://api.weixin.qq.com/sns/oauth2/access_token?appid=wx37e872e99dc23959&secret=bea6e765eaca3eb0452fee7fe3fe770c&code="+ code +"&grant_type=authorization_code";
        String forObject = restTemplate.getForObject(url, String.class);
        log.info(forObject);
    }

}
```

![1527693110491](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527693110491.png)

```xml
https://api.weixin.qq.com/sns/oauth2/access_token?appid=wx37e872e99dc23959&secret=bea6e765eaca3eb0452fee7fe3fe770c&code=001gc0ET1GloM61psZDT1xibET1gc0Eh&grant_type=authorization_code
```

测试效果（需要在手机上模拟用户同意测试）

![1527693643958](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527693643958.png)



![1527693298545](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527693298545.png)



 

#### 9.5 **第三方SDK开发（及其重要，代替以上步骤）**

![1527614544250](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527614544250.png)

##### - 将我们微信公众号的账号id和secret信息保存到配置文件中，通过一个配置类来读取

![1527699822616](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527699822616.png)

```java
/**
 * @ClassName AccountConfig
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Data
@Component
@ConfigurationProperties(prefix = "wechat")
public class WechatAccountConfig {

    private String mpAppId;
    private String mpAppSecret;
}
```

##### - 将配置信息放入到第三方的指定对象中

```java
/**
 * @ClassName WechatMpConfig
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Component
public class WechatMpConfig {

    @Autowired
    private WechatAccountConfig wechatAccountConfig;

    @Bean
    public WxMpService wxMpService(){
        WxMpService wxMpService = new WxMpServiceImpl();
        wxMpService.setWxMpConfigStorage(wxMpConfigStorage());
        return wxMpService;
    }

    @Bean
    public WxMpConfigStorage wxMpConfigStorage(){
        WxMpInMemoryConfigStorage wxMpConfigStorage = new WxMpInMemoryConfigStorage();
        wxMpConfigStorage.setAppId(wechatAccountConfig.getMpAppId());
        wxMpConfigStorage.setSecret(wechatAccountConfig.getMpAppSecret());
        return wxMpConfigStorage;
    }
}
```

##### - 具体的授权获取code然后获取token及用户信息最后跳转至指定url的业务逻辑（绕脑）

```java
/**
 * @ClassName WechatController
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Controller
@RequestMapping("/wechat")
@Slf4j
public class WechatController {

    @Autowired
    private WxMpService wxMpService;

    @GetMapping("authorize")
    public String authorize(@RequestParam("returnUrl") String returnUrl){

        //1. 配置
        //2. 调用方法
        String url = "http://xgfmnn.natappfree.cc/sell/wechat/userInfo";
        String redirectUrl = wxMpService
                .oauth2buildAuthorizationUrl(url, WxConsts.OAUTH2_SCOPE_USER_INFO, URLEncoder.encode(returnUrl));
        return "redirect:" + redirectUrl;

    }

    @GetMapping("/userInfo")
    public String userInfo(@RequestParam("code") String code,
                           @RequestParam("state") String returnUrl){
        log.info("进入了这个方法 ");
        WxMpOAuth2AccessToken wxMpOAuth2AccessToken = new WxMpOAuth2AccessToken();
        try {
            wxMpOAuth2AccessToken = wxMpService.oauth2getAccessToken(code);
        } catch (WxErrorException e) {
            log.error("【微信网页授权失败：】", e);
            throw new SellException(ResultExceptionEnum.WECHAT_AUTH_FAIL, e.getMessage());
        }
        String openId = wxMpOAuth2AccessToken.getOpenId();
        return "redirect:" + returnUrl + "?openid=" + openId;
    }
}
```

##### - 测试（使用手机端）



![1527700102535](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527700102535.png)

![1527700154188](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527700154188.png)

![1527700333398](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527700333398.png)

**前台发送授权请求到后台（携带想要去的url）--》后台处理请求（调用第三方接口完成微信授权并且重定向到我们的指定业务方法获取code以及再次访问微信服务器获取token和用户openid，最后跳转到前台发送的url地址并携带openid）**

##### - 流程图

![1527748290100](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527748290100.png)



## 10. 微信支付及退款（绝对重点,尴尬的是无法使用微信公众测试号测试微信支付，也没有微信商家号...）

### 10.1 github上的微信支付SDK

![1527778434680](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527778434680.png)

![1527778466367](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527778466367.png)

![1527779158062](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527779158062.png)

### 10.2 支付接口及实现类

```java
public interface PayService {

    void create(OrderDTO orderDTO);
}

/**
 * @ClassName PayServiceImpl
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
@Service
@Slf4j
public class PayServiceImpl implements PayService {

    private static final String ORDER_NAME = "微信支付订单";

    @Autowired
    private BestPayServiceImpl bestPayService;

    @Override
    public void create(OrderDTO orderDTO) {
        PayRequest payRequest = new PayRequest();
        payRequest.setOpenid(orderDTO.getBuyerOpenid());
        payRequest.setOrderAmount(orderDTO.getOrderAmount().doubleValue());
        payRequest.setOrderId(orderDTO.getOrderId());
        payRequest.setOrderName(ORDER_NAME);
        payRequest.setPayTypeEnum(BestPayTypeEnum.WXPAY_H5);

        // 注意格式优化
        log.info("【微信支付】：payRequst={}", JsonUtil.toJson(payRequest));
        PayResponse payResponse = bestPayService.pay(payRequest);
        log.info("【微信支付response】={}", JsonUtil.toJson(payResponse));
    }
}
```

### 10.3 商家信息配置yml

```yaml
wechat:
  mpAppId: wx37e872e99dc23959
  mpAppSecret: bea6e765eaca3eb0452fee7fe3fe770c
  mchId: 1409146202
  mchKey: c976503d34ca432c601361f969fd8d85
  keyPath: /var/weixin_cert/h5.p12        # 文件认证文件
  notifyUrl: http://sy77cf.natappfree.cc/sell/pay/notify    # 还是natapp的免费地址，但是它会经常换，我们必须设置这个属性作为异步通知的请求地址
```

### 10.4 Json格式转换工具

```java
/**
 * @ClassName JsonUtil
 * @Description TODO
 * @Author zc-cris
 * @Version 1.0
 **/
public class JsonUtil {

    public static String toJson(Object object){
        GsonBuilder gsonBuilder = new GsonBuilder();
        gsonBuilder.setPrettyPrinting();
        Gson gson = gsonBuilder.create();
        return gson.toJson(object);
    }

}
```



### 10.5 引入第三方支付SDK需要设置的类

```java
@Data
@Component
@ConfigurationProperties(prefix = "wechat")
public class WechatAccountConfig {

    // 公众号id
    private String mpAppId;
    // 公众号secret
    private String mpAppSecret;
    // 商户号
    private String mchId;
    // 商户密钥
    private String mchKey;
    // 商户证书路径
    private String keyPath;
    // 微信支付异步通知的路径
    private String notifyUrl;
}

@Component
public class WechatPayConfigService {

    @Autowired
    private WechatAccountConfig wechatAccountConfig;

    @Bean
    public BestPayServiceImpl bestPayService(){
        BestPayServiceImpl bestPayService = new BestPayServiceImpl();
        bestPayService.setWxPayH5Config(wxPayH5Config());
        return bestPayService;
    }

    @Bean
    public WxPayH5Config wxPayH5Config(){
        WxPayH5Config wxPayH5Config = new WxPayH5Config();
        wxPayH5Config.setAppId(wechatAccountConfig.getMpAppId());
        wxPayH5Config.setAppSecret(wechatAccountConfig.getMpAppSecret());
        wxPayH5Config.setKeyPath(wechatAccountConfig.getKeyPath());
        wxPayH5Config.setMchId(wechatAccountConfig.getMchId());
        wxPayH5Config.setMchKey(wechatAccountConfig.getMchKey());
        wxPayH5Config.setNotifyUrl(wechatAccountConfig.getNotifyUrl());
        return wxPayH5Config;
    }
}
```



### 10.6 单元测试

```java
public class PayServiceImplTest {

    @Autowired
    private PayService payService;
    @Autowired
    private OrderMasterService orderMasterService;

    @Test
    public void create() {
        OrderDTO orderDTO = orderMasterService.get("12345566");
        payService.create(orderDTO);
    }
}
```



### 10.7 测试图

#### - 测试生成预支付订单及prepay_id(很重要)

![1527782389222](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527782389222.png)



### 10.8 引入FreeMarker 组件

#### - pom.xml

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
```

#### - 测试

![1527784184579](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527784184579.png)

![1527784197139](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527784197139.png)

![1527784209387](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527784209387.png)

![1527784220359](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527784220359.png)

#### - 修改pay的service 创建订单代码

```java
    @Override
    public PayResponse create(OrderDTO orderDTO) {
        PayRequest payRequest = new PayRequest();
        payRequest.setOpenid(orderDTO.getBuyerOpenid());
        payRequest.setOrderAmount(orderDTO.getOrderAmount().doubleValue());
        payRequest.setOrderId(orderDTO.getOrderId());
        payRequest.setOrderName(ORDER_NAME);
        payRequest.setPayTypeEnum(BestPayTypeEnum.WXPAY_H5);

        // 注意格式优化
        log.info("【微信支付】：payRequst={}", JsonUtil.toJson(payRequest));
        PayResponse payResponse = bestPayService.pay(payRequest);
        log.info("【微信支付response】={}", JsonUtil.toJson(payResponse));

        return payResponse;
    }
```

#### - 修改pay的controller测试freemarker的参数传递

```java
@Controller
@RequestMapping("/pay")
@Slf4j
public class PayController {

    @Autowired
    private OrderMasterService orderMasterService;

    @Autowired
    private PayService payService;

    @GetMapping("/create")
    public ModelAndView create(@RequestParam("orderId") String orderId,
                               @RequestParam("returnUrl") String returnUrl,
                               Map<String, Object> map){

        // 1. 查询订单（注意，这里订单金额必须从数据库查询而不是前台传来，安全性考虑）
//        OrderDTO orderDTO = orderMasterService.get(orderId);
//        if(orderDTO == null){
//            log.error("订单不存在，订单号id：{}", orderId);
//            throw new SellException(ResultExceptionEnum.ORDER_NOT_EXIST);
//        }

        // 2. 发起支付
//        PayResponse payResponse = payService.create(orderDTO);

        map.put("orderId", "123456");


        return new ModelAndView("/pay/create", map);

    }
}
```

#### - ftl

```html
<h1>哈罗！cris,你的订单是：${orderId}</h1>
```

#### - 测试图

![1527784734128](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527784734128.png)



### 10.9 测试freemarker页面获取微信服务器预付订单详情信息并跳转至微信付款页面

![1527785032594](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527785032594.png)

### 10.10 支付成功返回到支付后的订单详情页面

![1527785682539](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527785682539.png)

![1527785787565](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527785787565.png)

#### - 如果要测试请用手机测试（设置代理，因为前端代码都在本地虚拟机里）

![1527786058351](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527786058351.png)

![1527786123349](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527786123349.png)



![1527868208805](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527868208805.png)



### 10.11 根据微信异步通知修改订单，注意不要根据微信服务器传回来的支付消息来判断用户支付成功，因为并不绝对可靠（微信官方文档自己说的）

##### - controller层代码

```java
@Controller
@RequestMapping("/pay")
@Slf4j
public class PayController {

    @Autowired
    private OrderMasterService orderMasterService;

    @Autowired
    private PayService payService;

    // 前台发送支付请求，参数是订单id和最后支付完成后的返回路径（注意：用户点击下单后，先要向后台发送创建订单的请求；
    // 只有后台订单创建成功并返回成功信息后，用户才可以进行付款的操作）
    @GetMapping("/create")
    public ModelAndView create(@RequestParam("orderId") String orderId,
                               @RequestParam("returnUrl") String returnUrl,
                               Map<String, Object> map){

        // 1. 查询订单（注意，这里订单金额必须从数据库查询而不是前台传来，安全性考虑）
        OrderDTO orderDTO = orderMasterService.get(orderId);
        if(orderDTO == null){
            log.error("订单不存在，订单号id：{}", orderId);
            throw new SellException(ResultExceptionEnum.ORDER_NOT_EXIST);
        }

        // 2. 向微信服务器发起支付请求，返回预支付的订单信息
        PayResponse payResponse = payService.create(orderDTO);

//        map.put("orderId", "123456");     测试传递参数到freemarker页面
        map.put("payResponse", payResponse);
        map.put("returnUrl", returnUrl);

        return new ModelAndView("/pay/create", map);

    }

    // 处理微信服务器向我们发送用户支付后的异步通知的路径
    @PostMapping("/notify")     // 微信服务器在用户支付金钱后会向我们的异步通知路径不停发送异步通知（xml格式）
    public ModelAndView notify(@RequestBody String nofifyData){

        // 根据异步通知修改我们的订单的支付状态
        payService.notify(nofifyData);

        // 订单状态修改后需要告诉微信服务器，以停止微信服务器不断的给我们服务器发送异步通知
        return new ModelAndView("pay/success");
    }
}
```

##### - payServiceImpl

```java
    // 处理用户微信支付后，微信服务器发给我们的异步通知
    @Override
    public PayResponse notify(String notifyData) {
        // 1. 签名验证  sdk搞定了
        // 2. 支付状态  sdk搞定了
        // 3. 支付金额必须一致
        // 4. 支付人和下单人是否必须一致（这里不做校验，举例：好友代付）

        PayResponse payResponse = bestPayService.asyncNotify(notifyData);
        log.info("【微信支付】，异步通知，payResponse：{}", JsonUtil.toJson(payResponse));

        // 查询订单
        OrderDTO orderDTO = orderMasterService.get(payResponse.getOrderId());
        // 判断订单是否存在
        if (orderDTO == null) {
            log.error("【微信支付】，异步通知，订单不存在：orderId--》{}", payResponse.getOrderId());
            throw new SellException(ResultExceptionEnum.ORDER_NOT_EXIST);
        }
        // 判断金额是否一致 （金额比较大坑！如果两个金额需要相等，我们直接相减小于某个指定的精度就可以认为是相等）
        if (!MathUtil.moneyEquals(orderDTO.getOrderAmount().doubleValue(), payResponse.getOrderAmount())) {
            log.error("【微信支付】，异步通知，订单实际金额和微信服务器发来的金额不一致，orderId={}，" +
                            "微信异步同时传来金额={}，我们订单的实际金额={}", payResponse.getOrderId(),
                    payResponse.getOrderAmount(), orderDTO.getOrderAmount());
            throw new SellException(ResultExceptionEnum.WECHAT_PAY_NOTIFY_MONEY_VERIFY_ERROR);
        }

        // 修改订单的支付状态
        orderMasterService.pay(orderDTO);
        return payResponse;
    }
```

##### - 金额比较工具类（坑）

```java
/**
 * @ClassName MathUtil
 * @Description 用于数字计算的工具类
 * @Author zc-cris
 * @Version 1.0
 **/
public class MathUtil {

    private static final Double MONEY_RANGE = 0.01;

    /**
    * @Author zc-cris
    * @Description 比较两个金额的大小是否相等
    * @Param [d1, d2]
    * @return java.lang.Boolean
    **/
    public static Boolean moneyEquals(Double d1, Double d2){
        Double result = Math.abs(d1 - d2);
        if(result < MONEY_RANGE){
            return true;
        }else {
            return false;
        }
    }
}
```

##### - 我们处理完订单支付信息后，发送给微信服务器的响应信息，就是freemarker页面success.ftl

```xml
<xml>
    <return_code><![CDATA[SUCCESS]]></return_code>
    <return_msg><![CDATA[OK]]></return_msg>
</xml>
```

##### - 调用第三方sdk完成微信支付的流程图

![1527873471679](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527873471679.png)



### 10.12 微信退款（详细参考微信文档）

#### - 下载微信专门的证书

![1527901578958](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527901578958.png)**

![1527901551195](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527901551195.png)

![1527901625635](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527901625635.png)

![1527901761413](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527901761413.png)

#### - payServiceImpl 的退款方法

```java
    /**
    * @Author zc-cris
    * @Description 微信退款
    * @Param [orderDTO]
    * @return com.lly835.bestpay.model.RefundResponse
    **/
    @Override
    public RefundResponse refund(OrderDTO orderDTO) {
        RefundRequest refundRequest = new RefundRequest();
        refundRequest.setOrderAmount(orderDTO.getOrderAmount().doubleValue());
        refundRequest.setOrderId(orderDTO.getOrderId());
        refundRequest.setPayTypeEnum(BestPayTypeEnum.WXPAY_H5);
        log.info("【微信退款】,refundRequest = {}", JsonUtil.toJson(refundRequest));

        // 执行微信退款
        RefundResponse refundResponse = bestPayService.refund(refundRequest);
        log.info("【微信退款：】，refundResponse={}", JsonUtil.toJson(refundResponse));

        return refundResponse;
    }
```

#### - 测试微信退款

```java
    @Test
    public void refund(){
        OrderDTO orderDTO = orderMasterService.get("12345566");
        payService.refund(orderDTO);
    }
```

#### - 完善取消订单的业务逻辑（退款）

```java
    /*用户取消订单*/
    @Override
    @Transactional
    public OrderDTO cancel(OrderDTO orderDTO) {
        // 判断订单状态(如果不是新建的订单，抛出无法取消的异常)
        if (!orderDTO.getOrderStatus().equals(OrderStatusEnum.NEW.getCode())) {
            log.error("该订单无法取消，orderDTO={}, orderStatus={}", orderDTO, orderDTO.getOrderStatus());
            throw new SellException(ResultExceptionEnum.ORDER_CANNOT_CANCELL);
        }


        // 修改订单状态有两种处理方法：
        // 1. 从数据库查询到订单然后设置订单状态，最后保存到数据库---缺点：查询两次数据库；优点：简单，方便
        /*OrderMaster orderMaster = orderMasterRepository.findOne(orderDTO.getOrderId());
        orderMaster.setOrderStatus(OrderStatusEnum.CANCELL.getCode());*/

        // 2. 将OrderDTO 转换为OrderMaster，然后调用一次数据库即可完成--
        // 优点：查询一次数据库，快；缺点：使用BeanUtils很容易出错，一定要注意顺序
        // 注意：用户取消订单的时候一定会先查询看看自己的订单，这个时候订单id就已经查询出来了，没有必要使用第一种方式还去查询一次
        // 建议使用第二种
        OrderMaster orderMaster = new OrderMaster();
        orderDTO.setOrderStatus(OrderStatusEnum.CANCELL.getCode());
        BeanUtils.copyProperties(orderDTO, orderMaster);
        OrderMaster updateOrderMaster = orderMasterRepository.save(orderMaster);
        if (updateOrderMaster == null) {
            log.error("取消订单失败，订单：{}", orderMaster);
            throw new SellException(ResultExceptionEnum.ORDER_CANCELL_FAIL);
        }

        // 返回库存（重点）
        if (CollectionUtils.isEmpty(orderDTO.getOrderDetails())) {
            log.error("订单中没有订单详情项，orderDTO：{}", orderDTO);
            throw new SellException(ResultExceptionEnum.ORDER_DETAIL_NOT_FIND);
        }

        List<CartDTO> cartDTOList = orderDTO.getOrderDetails().stream()
                .map(e -> new CartDTO(e.getProductId(), e.getProductQuantity()))
                .collect(Collectors.toList());

        productInfoService.increaseStock(cartDTOList);

        // 如果已经支付，退款
        if (orderDTO.getPayStatus().equals(PayStatusEnum.SUCCESS.getCode())) {
            //TODO      支付退款暂时先放着
            payService.refund(orderDTO);
        }

        return orderDTO;
    }
```

#### -  拓展（对账系统）

根据微信开发文档，我们的用户下单完成后，如果系统有对账模块的话，就会通过再去查询微信服务器该订单是否已经支付成功来修改我们本地数据库的用户订单支付状态（我们这里没有这么做，而是根据微信服务器返回的支付消息来修改订单支付状态）

而且我们每天的退款订单也应该由对账系统通过定时任务调度，每天实时的发送退款订单查询请求，跟踪退款订单的退款转态并做好每日的报表记录统计出来

### 10.13 用户访问微信公众号首页完成授权然后下单的前后端整个流程图（绝对重点）

![1527910502997](C:\File\Typora\使用Spring Boot完成微信点餐系统开发\使用Spring Boot 完成微信点餐系统开发.assets\1527910502997.png)



## 11. 卖家端管理系统

