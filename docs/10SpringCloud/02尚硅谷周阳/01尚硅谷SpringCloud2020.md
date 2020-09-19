# 一、SpringCloud简介

> springcloud官方文档（Hoxton SR5）：https://cloud.spring.io/spring-cloud-static/Hoxton.SR5/reference/htmlsingle/
> springcloud中文文档：https://www.springcloud.cc/
> springcloud中国社区文档：http://docs.springcloud.cn/
> https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md

![在这里插入图片描述](img/20200603155130971.png)
![在这里插入图片描述](img/20200603155055794.png)
![在这里插入图片描述](img/20200603154928893.png)
京东的促销节架构：
![在这里插入图片描述](img/20200603155436840.png)
阿里的架构：
![在这里插入图片描述](img/20200603155536850.png)
京东物流的架构：
![在这里插入图片描述](img/20200603155628936.png)

------

![在这里插入图片描述](img/20200603155804995.png)

![在这里插入图片描述](img/2020060315584195.png)
![在这里插入图片描述](img/20200603160047209.png)

# 二、springcloud与springboot版本的对应关系

官方对应关系：http://start.spring.io/actuator/info
![在这里插入图片描述](img/20200603162032427.png)

# 三、关于Cloud各种组件的停更/升级/替换

由于升级和停更引发的技术变更：（圈起来的是阳哥推荐的技术）
![在这里插入图片描述](img/20200603163254682.png)

# 四、微服务架构编码构建

**编程风格：约定 > 配置 > 编码**

## 创建微服务cloud整体聚合父工程Project步骤

1. New Project
   ![在这里插入图片描述](img/2020060318580469.png)
2. 聚合总父工程名字
   ![在这里插入图片描述](img/20200603185945795.png)
3. Maven选版本
   ![在这里插入图片描述](img/20200603190023890.png)
4. 开启自动导入
   ![在这里插入图片描述](img/20200603190110388.png)
5. 字符编码
   ![在这里插入图片描述](img/20200603201536924.png)
   ![在这里插入图片描述](img/20200603202101292.png)
6. 注解生效激活
   ![在这里插入图片描述](img/20200603202018606.png)
7. java编译版本选8
   ![在这里插入图片描述](img/20200603202200759.png)
8. File Type过滤
   ![在这里插入图片描述](img/20200603202552680.png)

### 父工程的pom

删除src目录，并在pom中添加

```xml
  <packaging>pom</packaging>
1
```

![在这里插入图片描述](img/20200603203329681.png)
然后把`<packaging>pom</packaging>`下的都用下面这些替换了。

```xml
  <!--统一管理jar包版本-->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <lombok.version>1.18.10</lombok.version>
    <log4j.version>1.2.17</log4j.version>
    <mysql.version>8.0.18</mysql.version>
    <druid.version>1.1.20</druid.version>
    <mybatis.spring.boot.version>1.3.2</mybatis.spring.boot.version>
  </properties>

  <!--子模块继承之后，提供作用：锁定版本+子module不用写groupId和version-->
  <dependencyManagement>
    <dependencies>
      <!--spring boot 2.2.2-->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.2.2.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud Hoxton.SR1-->
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>Hoxton.SR1</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud alibaba-->
      <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>2.1.0.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--mysql-->
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
        <scope>runtime</scope>
      </dependency>
      <!-- druid-->
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>${druid.version}</version>
      </dependency>

      <!--mybatis-->
      <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>${mybatis.spring.boot.version}</version>
      </dependency>
      <!--junit-->
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>${junit.version}</version>
      </dependency>
      <!--log4j-->
      <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>${log4j.version}</version>
      </dependency>
    </dependencies>

  </dependencyManagement>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>

</project>
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990
```

maven中dependencyManagement标签：
![在这里插入图片描述](img/20200603213321267.png)
子项目中，如果不指定，默认和父项目dependencyManagement标签中的版本一致，并且父项目dependencyManagement标签只是规定了版本号，具体引入依赖还是子项目引入。
![在这里插入图片描述](img/20200603213650893.png)
maven中跳过单元测试
![在这里插入图片描述](img/20200603214024557.png)
父工程创建完成后执行`mvn:install`将父工程发布到仓库方便子工程继承。

## Rest微服务工程构建

最开始的订单模块
![在这里插入图片描述](img/20200603214430625.png)
步骤：

1. 建module
2. 改pom
3. 写yml
4. 主启动
5. 业务类
6. 测试

### 1. 微服务提供者支付module模块cloud-provider-payment8001

##### 1. 新建模块

![在这里插入图片描述](img/20200603215013600.png)
![在这里插入图片描述](img/20200603215031951.png)
子项目名：cloud-provider-payment8001
![在这里插入图片描述](img/20200603215836141.png)
调整一下父项目pom文件
![在这里插入图片描述](img/20200603220138429.png)

##### 2. 在pom添加依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.20</version>
            <!--子工程写了版本号，就使用子工程的版本号，如果没写版本,找父工程中规定的版本号-->
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647
```

##### 3. 写yml

在resources目录下新建application.yml配置文件

```yml
#微服务建议一定要写服务端口号和微服务名称
server:
  #端口号
  port: 8001

spring:
  application:
    #微服务名称
    name: cloud-payment-service
  #数据库配置
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    #mysql5.x的没有cj
    driver-class-name: com.mysql.cj.jdbc.Driver
    #记得先创建数据库
    url: jdbc:mysql://localhost:3306/db2020?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

#mybatis配置
mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.angenin.springcloud.entities  #所有Entity别名类所在包
1234567891011121314151617181920212223
```

##### 4. 主启动

在java包下创建主启动类com.angenin.springcloud.PaymentMain8001

```java
@SpringBootApplication
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
123456
```

##### 5. 业务类

1. 建表SQL

   ```sql
   CREATE TABLE `payment`(
   	`id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT 'ID',
   	`serial` VARCHAR(200) DEFAULT '',
   	PRIMARY KEY(`id`)
   )ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
   
   INSERT INTO payment(`serial`)VALUES("张三");
   1234567
   ```

2. entities
   在springcloud包下新建实体类entities.Payment

   ```java
   //这三个注解是lombok的，除了导入依赖，idea还需要安装插件（具体操作问度娘）
   @Data   //set/get方法
   @AllArgsConstructor //有参构造器
   @NoArgsConstructor  //无参构造器
   public class Payment implements Serializable {
       private long id;
       private String serial;
   }
   12345678
   ```

   在entities包下新建CommonResult（json封装体，传给前端的）

   ```java
   //返回给前端的通用json数据串
   @Data   //set/get方法
   @AllArgsConstructor //有参构造器
   @NoArgsConstructor  //无参构造器
   public class CommonResult<T> {
       private Integer code;
       private String message;
       private T data; //泛型，对应类型的json数据
   
       //自定义两个参数的构造方法
       public CommonResult(Integer code, String message){
           this(code, message, null);
       }
   }
   1234567891011121314
   ```

3. dao
   在springcloud包下新建Dao.PaymentDao接口

   ```java
   @Mapper
   public interface PaymentDao {
   
       //增
       int create(Payment payment);
   
       //改     加上@Param注解，mapper中就可以采用#{}的方式把@Param注解括号内的参数进行引用
       Payment getPaymentById(@Param("id") Long id);
       
       //这里用增和改进行演示，有兴趣的可以自己加其他的方法
   }
   1234567891011
   ```

4. mapper
   在resources目录下新建mapper目录，然后新建PaymentMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <mapper namespace="com.angenin.springcloud.Dao.PaymentDao">
   
       <resultMap id="BaseResultMap" type="com.angenin.springcloud.entities.Payment">
           <id column="id" property="id" jdbcType="BIGINT"/>
           <id column="serial" property="serial" jdbcType="VARCHAR"/>
       </resultMap>
       
   
       <!--  增  -->
       <!--  Payment标红了不用管，因为我们已经在yml文件中指定了Payment的位置了  -->
       <insert id="create" parameterType="Payment" useGeneratedKeys="true" keyProperty="id">
           insert into payment(serial)values(#{serial});
       </insert>
   
       <!--  改  -->
       <!--返回用resultMap，防止命名不规范-->
       <select id="getPaymentById" parameterType="Long" resultMap="BaseResultMap">
           select * from payment where id=#{id};
       </select>
   </mapper>
   1234567891011121314151617181920212223
   ```

5. service
   在springcloud包下新建service.PaymentService接口

   ```java
   public interface PaymentService {
   
       int create(Payment payment);
   
       Payment getPaymentById(@Param("id") Long id);
   
   }
   1234567
   ```

   在service包下新建impl.PaymentServiceIpml实现类

   ```java
   @Service
   public class PaymentServiceIpml implements PaymentService {
   
       @Resource   //@Autowired也可以
       private PaymentDao paymentDao;
   
       public int create(Payment payment){
           return paymentDao.create(payment);
       }
   
       public Payment getPaymentById(Long id){
           return paymentDao.getPaymentById(id);
       }
   
   }
   123456789101112131415
   ```

6. controller
   在springcloud包下新建controller.PaymentController

   ```java
   @RestController
   @Slf4j  //日志
   public class PaymentController {
   
       @Resource
       private PaymentService paymentService;
   
       //前后端分离，所以不能直接返回对象，数据要先经过CommonResult封装再返回
       @PostMapping("/payment/create")
       public CommonResult create(@RequestBody Payment payment){
           int result = paymentService.create(payment);
           log.info("******插入的数据为：" + payment);
           log.info("******插入结果：" + result);
   
           if(result > 0){
               //插入成功
               return new CommonResult(200, "插入数据库成功", result);
           }else{
               return new CommonResult(444, "插入数据库失败");
           }
       }
   
   
       @GetMapping("/payment/get/{id}")
       public CommonResult getPaymentById(@PathVariable("id") Long id){
           Payment payment = paymentService.getPaymentById(id);
           log.info("******查询结果：" + payment);
   
           if(payment != null){
               //查询成功
               return new CommonResult(200, "查询成功", payment);
           }else{
               return new CommonResult(444, "没有对应记录，查询ID：" + id);
           }
       }
   
   }
   12345678910111213141516171819202122232425262728293031323334353637
   ```

##### 6. 测试

启动项目
![在这里插入图片描述](img/20200604133701991.png)
浏览器输入`http://localhost:8001/payment/get/1`，查询成功。
![在这里插入图片描述](img/20200604133909669.png)
因为浏览器一般不支持直接发送post请求，所以，需要使用工具进行测试。（我这里用的是Postman）
重新测试查询，没问题。
![在这里插入图片描述](img/20200604134210828.png)
输入`http://localhost:8001/payment/create`发送post请求，往数据库中插入一条数据，需要把数据写到body中。
![在这里插入图片描述](img/20200604161149191.png)
![在这里插入图片描述](img/20200604134424124.png)

### 2. 热部署Devtools（只能在开发阶段使用）

1. 在cloud-provider-payment8001项目中添加热部署依赖（已经在导入了，所以看看就行了，记一下步骤）

   ```xml
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
   1234567
   ```

2. 添加一个插件到父类总工程的pom.xml里（这一步之前也已经做了）

   ```xml
     <build>
       <plugins>
         <plugin>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-maven-plugin</artifactId>
           <configuration>
             <fork>true</fork>
             <addResources>true</addResources>
           </configuration>
         </plugin>
       </plugins>
     </build>
   123456789101112
   ```

3. 开启自动编译的选项
   ![在这里插入图片描述](img/20200604135952686.png)
   ![在这里插入图片描述](img/20200604140121655.png)

4. 热注册开启
   组合键`Shift+Ctrl+Alt+/`（Mac系统的把Ctrl换成command键），选中Registry…
   ![在这里插入图片描述](img/20200604140351413.png)
   ![在这里插入图片描述](img/20200604140808410.png)
   ![在这里插入图片描述](img/20200604140913214.png)

5. 重启IDEA
   开完之后感觉有点卡，虽然能自动部署，但是代码提示变慢了，等以后换了电脑再玩自动热部署，现在就不开了。
   把第四步打上的勾去掉（原本打上的就不用去掉了），然后重启idea就可以了。
   不使用自动热部署，也可以使用热部署，按这个绿色的锤子，重新启动，只编译改了的文件，所以比重启快一些。
   ![在这里插入图片描述](img/20200604143546260.png)

### 3. 微服务消费者订单module模块cloud-consumer-order80

##### 1. 新建模块

![在这里插入图片描述](img/20200604142610310.png)
cloud-consumer-order80
![在这里插入图片描述](img/20200604142632868.png)

##### 2. 改pom

往pom中添加：

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
12345678910111213141516171819202122232425262728
```

##### 3. 写yml

在resources目录下新建application.yml文件

```yml
#访问一个网站时，默认是80端口，给用户80端口，用户就可以不用加端口直接访问页面
server:
  port: 80
123
```

##### 4. 主启动

com.angenin.springcloud.OrderMain80

```java
@SpringBootApplication
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class, args);
    }
}
123456
```

##### 5. 写业务

1. 复制cloud-provider-payment8001项目里的entities（里面2个实体类）到本项目（cloud-consumer-order80）的springcloud包下。（在后面的工程重构，阳哥会把实体类抽取出来）

2. 在springcloud包下新建config.ApplicationContextConfig

   ```java
   @Configuration
   public class ApplicationContextConfig {
   
       //往容器中添加一个RestTemplate
       //RestTemplate提供了多种便捷访问远程http访问的方法
       @Bean
       public RestTemplate restTemplate(){
           return new RestTemplate();
       }
   
   }
   1234567891011
   ```

3. 在springcloud包下新建controller.OrderController

   ```java
   @RestController
   @Slf4j
   public class OrderController {
   
       public static final String PAYMENT_URL = "http://localhost:8001";
   
       @Resource
       private RestTemplate restTemplate;
   
       //因为浏览器只支持get请求，为了方便这里就用get
       @GetMapping("/consumer/payment/create")
       public CommonResult<Payment> create(Payment payment){
           log.info("********插入的数据：" + payment);
           //postForObject分别有三个参数：请求地址，请求参数，返回的对象类型
           return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
       }
   
       @GetMapping("/consumer/payment/get/{id}")
       public CommonResult<Payment> getPayment(@PathVariable("id") Long id){
           log.info("********查询的id：" + id);
           //getForObject两个参数：请求地址，返回的对象类型
           return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
       }
   }
   123456789101112131415161718192021222324
   ```

##### 6. 测试

启动两个项目进行测试，两个都启动后，右下角会弹出个services提示，点击show。
然后会把运行的项目合并在一起显示：
![在这里插入图片描述](img/20200604154125630.png)
在浏览器中输入`http://localhost/consumer/payment/get/1`成功查询到数据。
![在这里插入图片描述](img/20200604154414132.png)
在浏览器中输入`http://localhost/consumer/payment/create?serial=王五`插入一条数据。
![在这里插入图片描述](img/20200604154610918.png)
![在这里插入图片描述](img/20200604160027651.png)

### 4. 工程重构

观察问题：系统中有重复部分，重构。

##### 1. 新建新工程：cloud-api-commons

![在这里插入图片描述](img/2020060416212192.png)
![在这里插入图片描述](img/20200604162141706.png)

##### 2. 改pom

添加依赖：

```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <!--   一个Java工具包     -->
       <dependency>
           <groupId>cn.hutool</groupId>
           <artifactId>hutool-all</artifactId>
           <version>5.1.0</version>
       </dependency>
   </dependencies>
12345678910111213141516171819
```

##### 3. 拷贝entities到本项目中（路径需要一样，先在本项目中建包，然后在拷贝）

##### 4. 对本项目进行打包

![在这里插入图片描述](img/20200604163117202.png)

##### 5. 删除另外两个项目的entities包。

##### 6. 然后给两个项目引入打包后项目的坐标。

```xml
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
123456
```

1. 重新测试
   `http://localhost/consumer/payment/get/1`
   ![在这里插入图片描述](img/20200604164429764.png)
   `http://localhost/consumer/payment/create?serial=赵六`
   ![在这里插入图片描述](img/20200604164441337.png)
   ![在这里插入图片描述](img/20200604164456248.png)

# 五、Eureka服务注册与发现

## Eureka基础知识

#### 服务治理

![在这里插入图片描述](img/20200604170255644.png)

#### 服务注册

![在这里插入图片描述](img/20200604170538357.png)
![在这里插入图片描述](img/20200604170621320.png)

#### Eureka两组件

![在这里插入图片描述](img/20200604172054158.png)

## 单机Eureka构建步骤

### IDEA生成eurekaServer端服务注册中心

##### 1. 建module

cloud-eureka-server7001

##### 2. 改pom

server端依赖对比：
![在这里插入图片描述](img/20200604173445310.png)
在pom中添加

```xml
  <dependencies>
      <!-- eureka-server -->
      <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
      </dependency>
      <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
      <dependency>
          <groupId>com.angenin.springcloud</groupId>
          <artifactId>cloud-api-commons</artifactId>
          <version>${project.version}</version>
      </dependency>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <!--监控-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-actuator</artifactId>
      </dependency>
      <!-- 一般通用配置 -->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-devtools</artifactId>
          <scope>runtime</scope>
          <optional>true</optional>
      </dependency>
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
          <optional>true</optional>
      </dependency>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-test</artifactId>
          <scope>test</scope>
      </dependency>
  </dependencies>
123456789101112131415161718192021222324252627282930313233343536373839
```

##### 3. 写yml

在resources目录下新建application.yml文件

```yml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost  #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己（想注册也可以，不过没必要）
    register-with-eureka: false
    #false表示自己端就是注册中心，职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与eurekaServer交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
1234567891011121314
```

##### 4. 主启动

在java包下新建com.angenin.springcloud.EurekaMain7001

```java
@EnableEurekaServer //表示此项目是eureka的服务注册中心
@SpringBootApplication
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class, args);
    }
}
1234567
```

##### 6. 测试

启动项目，在浏览器输入`http://localhost:7001/`
![在这里插入图片描述](img/20200604184833496.png)

### 将EurekaClient端8001注册进EurekaServer成为服务提供者provider

client端依赖对比
![在这里插入图片描述](img/20200604185304156.png)

1. 引入依赖

   ```xml
    <!-- eureka-client -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
   12345
   ```

2. 在yml文件中添加

   ```yml
   eureka:
     client:
       #true表示向注册中心注册自己，默认为true
       register-with-eureka: true
       #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
       fetch-registry: true
       service-url:
         defaultZone: http://localhost:7001/eureka
   12345678
   ```

3. 在主配置类上加上`@EnableEurekaClient`注解，表示这个项目是eureka的客户端。

4. 启动项目，然后刷新页面，成功注册进注册中心。
   ![在这里插入图片描述](img/20200604190511890.png)
   在yml文件中application.name就是注册进注册中心时的应用名。
   ![在这里插入图片描述](img/20200604190426232.png)

### 将EurekaClient端80注册进EurekaServer成为服务消费者consumer

1. 引入依赖

   ```xml
    <!-- eureka-client -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
   12345
   ```

2. 在yml文件中添加

   ```yml
   spring:
     application:
       name: cloud-order-service
   
   eureka:
     client:
       register-with-eureka: true
       fetch-registry: true
       service-url:
         defaultZone: http://localhost:7001/eureka
   12345678910
   ```

3. 在主配置类上加上`@EnableEurekaClient`注解。

4. 启动项目，刷新页面
   ![在这里插入图片描述](img/20200604193645656.png)

## 集群Eureka构建步骤

### 原理说明

![在这里插入图片描述](img/202006042116066.png)
搭建Eureka注册中心集群，实现负载均衡+故障容错。

Eureka集群：**相互注册，相互守望**。
![在这里插入图片描述](img/20200604212901842.png)

### 构建eurekaServer集群环境

1. 参照cloud-eureka-server7001新建cloud-eureka-server7002和cloud-eureka-server7003。

2. Windows系统的兄弟就跟着老师修改hosts文件，因为我的是mac系统的，接下来写的步骤是mac的。

   打开终端，输入

   ```
   sudo vim /etc/hosts
   ```

   来修改hosts文件。（权限不够需要加上sudo并输入密码）

   在最后一行加入：

   ```shell
   127.0.0.1       eureka7001.com
   127.0.0.1       eureka7002.com
   127.0.0.1		eureka7003.com
   123
   ```

   然后

   ```
   :wq!
   ```

   保存退出。

3. 修改7001的yml文件

   ```yml
   eureka:
     instance:
       hostname: eureka7001.com  #eureka服务端的实例名称
     client:
       register-with-eureka: false
       fetch-registry: false
       service-url:
   #      单机
   #      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
         #集群版  相互注册，相互守望
         defaultZone: http://eureka7002.com:7002/eureka/, http://eureka7003.com:7003/eureka/ 
         
   #    defaultZone是固定写法，如果想自定义，需要按以下写法才行：
   #    region: eureka-server
   #    availability-zones:
   #      eureka-server: server1,server2
   #    service-url:
   #      server1: http://eureka7002.com:7002/eureka/
   #      server2: http://eureka7003.com:7003/eureka/
   12345678910111213141516171819
   ```

4. 修改7002的yml文件

   ```yml
   eureka:
     instance:
       hostname: eureka7002.com  #eureka服务端的实例名称
     client:
       register-with-eureka: false
       fetch-registry: false
       service-url:
         #集群版  相互注册，相互守望
         defaultZone: http://eureka7001.com:7001/eureka/, http://eureka7003.com:7003/eureka/ #相互注册，相互守望
   123456789
   ```

5. 修改7003的yml文件

   ```yml
   eureka:
     instance:
       hostname: eureka7003.com  #eureka服务端的实例名称
     client:
       register-with-eureka: false
       fetch-registry: false
       service-url:
         #集群版  相互注册，相互守望
         defaultZone: http://eureka7001.com:7001/eureka/, http://eureka7002.com:7002/eureka/ #相互注册，相互守望
   123456789
   ```

6. 然后启动这三个项目
   ![在这里插入图片描述](img/202006042310304.png)
   ![在这里插入图片描述](img/20200604231019775.png)
   ![在这里插入图片描述](img/20200604231007137.png)

### 将支付服务8001和订单服务80微服务发布到集群配置中

把两个项目的yml文件中的defaultZone改为：

```yml
      #集群版
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
12
```

启动5个项目进行测试：（先启动集群，再启动8001，最后启动80）
集群后台截图：
![在这里插入图片描述](img/20200605103426626.png)
![在这里插入图片描述](img/20200605103439915.png)
![在这里插入图片描述](img/20200605103455312.png)

### 构建支付服务提供者集群环境

1. 按照8001新建8002（只多建了一个提供者，建多了怕电脑受不了）。（除了要yml文件中需要改端口号和主配置类，其他直接复制8001的，yml文件中的应用名不需要改，因为是集群，所以应用名需要一致）

2. 分别在所有的提供者的PaymentController中加入：（这个@Value是spring的注解）
   ![在这里插入图片描述](img/20200605182211246.png)

3. 修改消费者的OrderController，把写死的url改为服务名称：

   ```java
      	public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";
   1
   ```

4. 然后在消费者的ApplicationContextConfig里的restTemplate方法上加上`@LoadBalanced`，开启负载均衡功能。

5. 启动eurekaServer集群，启动提供者集群，启动消费者。
   *如果启动提供者后出现，这个错误：`Public Key Retrieval is not allowed`*
   *请在yml文件中的datasource.datasource.url后加上`&allowPublicKeyRetrieval=true`即可解决。*
   ![在这里插入图片描述](img/20200605202006850.png)
   另外两个eurekaserver也一样，就不截图。
   在浏览器中输入`http://localhost/consumer/payment/get/1`，多次刷新可以看到，提供服务的应用在不同的切换，实现负载均衡的效果。
   ![在这里插入图片描述](img/20200605202105874.png)
   ![在这里插入图片描述](img/20200605202054534.png)

## actuator微服务信息完善

修改三个微服务的yml文件：

```yml
#######8001#######
# client:	
#	 ...	instance要和client对齐
  instance:
    instance-id: payment8001
    prefer-ip-address: true   #访问路径可以显示ip地址

#######8002#######
  instance:
    instance-id: payment8002   #修改显示的主机名
    prefer-ip-address: true   #访问路径可以显示ip地址
    
#######80#######
  instance:
    instance-id: consumer80   #修改显示的主机名
    prefer-ip-address: true   #访问路径可以显示ip地址
12345678910111213141516
```

修改前：
![在这里插入图片描述](img/2020060520375732.png)
修改后：
![在这里插入图片描述](img/20200605203832256.png)

## 服务发现Discovery

对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息。

#### 修改提供者集群的controller

1. 在主配置类上加上`@EnableDiscoveryClient`注解，启用发现客户端。
2. 在两个提供者的PaymentController中加入：

```java
    @Resource
    private DiscoveryClient discoveryClient;	//springframework的DiscoveryClient（不要导错包了）
    

    @GetMapping("/payment/discovery")
    public Object discovery(){
        //获取服务列表的信息
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            log.info("*******element：" + element);
        }

        //获取CLOUD-PAYMENT-SERVICE服务的所有具体实例
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            //getServiceId服务器id getHost主机名称 getPort端口号  getUri地址
            log.info(instance.getServiceId() + "\t" + instance.getHost() + "\t" + instance.getPort() + "\t" + instance.getUri());
        }

        return this.discoveryClient;
    }

}
123456789101112131415161718192021222324
```

#### 测试

对8001进行测试
，在浏览器输入：`http://localhost:8001/payment/discovery`
![在这里插入图片描述](img/20200605213141193.png)
![在这里插入图片描述](img/2020060521312719.png)

## Eureka自我保护

#### 概述

![在这里插入图片描述](img/2020060521354613.png)

##### 导致原因

![在这里插入图片描述](img/20200605213632574.png)
![在这里插入图片描述](img/20200605214225221.png)
![在这里插入图片描述](img/20200605214249635.png)
![在这里插入图片描述](img/20200605214415868.png)
*只有在一定时间内丢失大量服务的心跳才开启自我保护模式。*

#### 禁止自我保护

先把cloud-eureka-server7001和cloud-provider-payment8001都切回单机版测试禁止自我保护。

cloud-eureka-server7001的yml文件：

```yml
#  client
#  	...		server与client对齐
  server:
    #关闭自我保护，默认为true
    enable-self-preservation: false
    #心跳的间隔时间，单位毫秒
    eviction-interval-timer-in-ms: 2000
1234567
```

cloud-provider-payment8001的yml文件：

```yml
    #Eureka客户端向服务端发送心跳的时间间隔，单位秒（默认30秒）
    lease-renewal-interval-in-seconds: 1
    #Eureka服务端在收到最后一次心跳后等待的时间上限，单位秒（默认90秒），超时剔除服务
    lease-expiration-duration-in-seconds: 2
1234
```

启动注册中心和提供者：
![在这里插入图片描述](img/20200605220547579.png)
然后关闭提供者（模拟网络延时），提供者直接被剔除。
![在这里插入图片描述](img/20200605220726416.png)

# 六、Zookeeper服务注册与发现

## 注册中心Zookeeper

![在这里插入图片描述](img/20200605221319661.png)

关闭linux的防火墙：

```shell
systemctl stop firewalld
systemctl status firewalld
12
```

![在这里插入图片描述](img/2020060522174042.png)
使用docker启动Zookeeper：（docker的操作可以看这篇：[Docker基础入门学习笔记](https://blog.csdn.net/qq_36903261/article/details/105870268)）

```shell
#拉取Zookeeper镜像
docker pull zookeeper

#启动Zookeeper
docker run --name zk01 -p 2181:2181 --restart always -d zookeeper
12345
```

#### 服务节点是临时节点还是持久节点？

zookeeper也是有心跳机制，在一定时间能如果一直没心跳返回，Zookeeper就会把服务节点剔除掉。所以在Zookeeper上的服务节点是临时节点。

## 服务提供者

为了区分，中间空个8003。

1. 新建工程cloud-provider-payment8004

2. pom

   ```xml
   <!--因为接下来不会用到数据库，所以不导入数据库相关的依赖（防止没配置而报错）-->
   
   	<!--替换掉eureka依赖，其他直接复制8001-->
           <!--SpringBoot整合Zookeeper客户端-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
   <!--        <exclusions>-->
               <!--先排除自带的zookeeper3.5.3-->
   <!--            <exclusion>-->
   <!--                <groupId>org.apache.zookeeper</groupId>-->
   <!--                <artifactId>zookeeper</artifactId>-->
   <!--            </exclusion>-->
   <!--        </exclusions>-->
           </dependency>
   
           <!--添加zookeeper3.4.9版本（引入对应版本的依赖）-->
   <!--        <dependency>-->
   <!--            <groupId>org.apache.zookeeper</groupId>-->
   <!--            <artifactId>zookeeper</artifactId>-->
   <!--            <version>3.4.9</version>-->
   <!--        </dependency>-->
   12345678910111213141516171819202122
   ```

3. yml

   ```yml
   #端口号
   server:
     port: 8004
   
   
   spring:
     application:
       #服务别名——注册到zookeeper注册中心的名称
       name: cloud-provider-payment
     cloud:
       zookeeper:
         connect-string: 10.211.55.17:2181 #linux的ip加暴露的端口号
   123456789101112
   ```

4. 主启动类

   ```java
   @EnableDiscoveryClient	//该注解用于向使用consul或者Zookeeper作为注册中心时注册服务
   @SpringBootApplication
   public class PaymentMain8004 {
       public static void main(String[] args) {
           SpringApplication.run(PaymentMain8004.class, args);
       }
   }
   1234567
   ```

5. controller
   新建controller.PaymentController

   ```java
   @Slf4j
   @RestController
   public class PaymentController {
   
       @Value("${server.port}")        //获取端口号
       private String serverPort;
   
       @RequestMapping("/payment/zk")
       public String paymentzk(){
           return "springcloud with zookeeper：" + serverPort + "\t" + UUID.randomUUID().toString();
       }
   
   }
   12345678910111213
   ```

6. Zookeeper容器
   默认已经帮我们启动了服务端和客户端，如果想进去容器的话，使用下面的命令。

   ```shell
   #查看正在运行的容器（查看Zookeeper容器的id）
   docker ps
   
   #进入zookeeper容器
   docker exec -it 容器ID /bin/bash
   
   #退出容器（或者按快捷键ctrl+P+Q退出）
   exit
   #启动容器
   docker start 容器ID
   #关闭容器
   docker stop 容器ID
   123456789101112
   ```

7. 启动8004项目，浏览器输入`http://localhost:8004/payment/zk`
   ![在这里插入图片描述](img/20200606152453907.png)

8. 进入Zookeeper容器（成功注册进注册中心）
   ![在这里插入图片描述](img/20200606152531888.png)
   ![在这里插入图片描述](img/20200606152857631.png)
   https://tool.lu/json/
   ![在这里插入图片描述](img/2020060615303883.png)

## 服务消费者

1. 新建消费者模块cloud-consumerzk-order80。

2. pom和yml直接复制8004。（yml中端口号改为80，应用名改为cloud-consumer-order，其他都相同）

3. 主启动类。（与8004相同）

4. 在springcloud包下新建config.ApplicationContextConfig

   ```java
   @Configuration
   public class ApplicationContextConfig {
   
       @Bean
       @LoadBalanced   //负载均衡
       public RestTemplate restTemplate(){
           return new RestTemplate();
       }
   
   }
   12345678910
   ```

5. 新建controller.OrderZKController

   ```java
   @RestController
   @Slf4j
   public class OrderZKController {
   
       public static final String INVOKE_URL = "http://cloud-provider-payment";
   
       @Resource
       private RestTemplate restTemplate;
   
       @RequestMapping("/consumer/payment/zk")
       public String paymentInfo(){
           String result = restTemplate.getForObject(INVOKE_URL + "/payment/zk", String.class);
           return result;
       }
   
   }
   12345678910111213141516
   ```

6. 启动项目
   ![在这里插入图片描述](img/20200606160012462.png)
   `http://localhost/consumer/payment/zk`
   ![在这里插入图片描述](img/20200606160309104.png)

# 七、Consul服务注册与发现

Consul官网：https://www.consul.io/
Consul中文文档：https://www.springcloud.cc/spring-cloud-consul.html

### 简介

Consul是一种服务网格解决方案，提供具有服务发现，配置和分段功能的全功能控制平面。这些功能中的每一个都可以根据需要单独使用，也可以一起使用以构建完整的服务网格。Consul需要一个数据平面，并支持代理和本机集成模型。Consul附带了一个简单的内置代理，因此一切都可以直接使用，还支持Envoy等第三方代理集成。
![在这里插入图片描述](img/20200606161357409.png)

### 主要特点

- 服务发现：Consul的客户端可以注册服务，例如 api或mysql，其他客户端可以使用Consul来发现给定服务的提供者。使用DNS或HTTP，应用程序可以轻松找到它们依赖的服务。
- 健康检测：领事客户端可以提供任意数量的运行状况检查，这些检查可以与给定服务（“ Web服务器是否返回200 OK”）或本地节点（“内存利用率低于90％”）相关。操作员可以使用此信息来监视群集的运行状况，服务发现组件可以使用此信息将流量从不正常的主机发送出去。
- KV存储：应用程序可以将Consul的分层键/值存储用于多种目的，包括动态配置，功能标记，协调，领导者选举等。简单的HTTP API使其易于使用。
- 安全的服务通信：领事可以为服务生成并分发TLS证书，以建立相互TLS连接。 意图 可用于定义允许哪些服务进行通信。可以使用可以实时更改的意图轻松管理服务分段，而不必使用复杂的网络拓扑和静态防火墙规则。
- 多数据中心：Consul开箱即用地支持多个数据中心。这意味着Consul的用户不必担心会构建其他抽象层以扩展到多个区域。

Consul旨在对DevOps社区和应用程序开发人员友好，使其非常适合现代，灵活的基础架构。

### 在docker上安装启动consul

```shell
#拉取consul镜像
docker pull consul

#启动consul
docker run -d  -p 8500:8500/tcp --name myConsul  consul agent -server -ui -bootstrap-expect=1 -client=0.0.0.0
12345
```

然后在浏览器输入`http://http://10.211.55.17/:8500`（linux的IP地址加上冒号8500）
![在这里插入图片描述](img/20200606164154982.png)

## 服务提供者

1. 新建服务提供者cloud-provider-consul-payment8006。
2. pom复制8004。（用下面的依赖替换Zookeeper的依赖）

```xml
 <!--SpringCloud consul-server-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-consul-discovery</artifactId>
  </dependency>
12345
```

1. yml

```yml
server:
  port: 8006


spring:
  application:
    name: consul-provider-payment
  cloud:
    consul:
      host: 10.211.55.17  #用linux的ip地址（consul在本机就填localhost）
      port: 8500
      discovery:
        service-name: ${spring.application.name}
12345678910111213
```

1. 主启动类（与8004相同）

2. controller

   ```java
   @RestController
   @Slf4j
   public class PaymentController {
   
       @Value("${server.port}")        //获取端口号
       private String serverPort;
   
       @RequestMapping("/payment/consul")
       public String paymentConsul(){
           return "springcloud with zookeeper：" + serverPort + "\t" + UUID.randomUUID().toString();
       }
   
   }
   12345678910111213
   ```

3. 启动项目
   ![在这里插入图片描述](img/20200606170052641.png)
   `http://localhost:8006/payment/consul`
   ![在这里插入图片描述](img/20200606170123592.png)

## 服务消费者

1. 新建模块cloud-consumer-consul-order80

2. pom（与8006相同）

3. yml（端口号为80，应用名为consul-consumer-order，其他和8006相同）

4. 主启动类（与8006相同）

5. config（和zk的消费者相同）

6. controller.OrderConsulController

   ```java
   @RestController
   @Slf4j
   public class OrderConsulController {
   
       public static final String INVOKE_URL = "http://consul-provider-payment";
   
       @Resource
       private RestTemplate restTemplate;
   
       @RequestMapping("/consumer/payment/consul")
       public String paymentInfo(){
           String result = restTemplate.getForObject(INVOKE_URL + "/payment/consul", String.class);
           return result;
       }
   
   }
   12345678910111213141516
   ```

7. 启动项目
   ![在这里插入图片描述](img/20200606171610107.png)
   `http://localhost/consumer/payment/consul`
   ![在这里插入图片描述](img/20200606171821289.png)

## 三者的异同点

![在这里插入图片描述](img/20200606172331933.png)

```
CAP：（只能二选一）
A：可用性
C：一致性
P：分区容错性（微服务架构必须保证有P）
1234
```

![在这里插入图片描述](img/20200606172402942.png)
![在这里插入图片描述](img/20200606172725807.png)
![在这里插入图片描述](img/20200606173028839.png)

# 八、Ribbon负载均衡服务调用

恢复eureka集群环境，以便接下来的练习。

## 简介

![在这里插入图片描述](img/20200606174053738.png)

### 负载均衡

![在这里插入图片描述](img/20200606200122261.png)
![在这里插入图片描述](img/2020060620064198.png)
![在这里插入图片描述](img/20200606200403757.png)
![在这里插入图片描述](img/2020060620042210.png)

## Ribbon（负载均衡+RestTemplate调用）

Ribbon是客户端（消费者）负载均衡的工具。
![在这里插入图片描述](img/20200606201514137.png)
![在这里插入图片描述](img/20200606201621571.png)
![在这里插入图片描述](img/20200606201721234.png)

### pom

```xml
  <!--Ribbon的依赖-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
 </dependency>
12345
```

新版的eureka依赖以及集成了Ribbon依赖，所以可以不引用。
![在这里插入图片描述](img/20200606202719493.png)

### RestTemplate

@LoadBalanced注解给RestTemplate开启负载均衡的能力。

官方文档：https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html
![在这里插入图片描述](img/20200606202842361.png)

#### getForObject/getForEntity方法

![在这里插入图片描述](img/20200606202923266.png)
getForObject已经用过了，所以只测试getForEntity方法。

##### 测试getForEntity方法

在消费者cloud-consumer-order80的OrderController方法中添加：

```java
    @GetMapping("/consumer/payment/getEntity/{id}")
    public CommonResult<Payment> getPayment2(@PathVariable("id") Long id){
        log.info("********查询的id：" + id);
        //getForObject两个参数：请求地址，返回的对象类型
//        return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
        ResponseEntity<CommonResult> entity = restTemplate.getForEntity(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);

        //getStatusCode获取状态码，is2xxSuccessful如果是状态码是2xx
        if(entity.getStatusCode().is2xxSuccessful()){
            //返回body
            return entity.getBody();
        }else{
            return new CommonResult<>(444, "操作失败");
        }
    }
123456789101112131415
```

然后启动eureka集群，提供者集群，消费者。（记得把eureka7001和提供者8001的yml文件改为集群环境（这个就这东西搞了我半小时，我就说怎么负载均衡不了了，只使用了8002））
在浏览器输入`http://localhost/consumer/payment/getEntity/1`
![在这里插入图片描述](img/20200606211930935.png)

#### postForObject/postForEntity方法

```java
    @GetMapping("/consumer/payment/createEntity")
    public CommonResult<Payment> create2(Payment payment){
        log.info("********插入的数据：" + payment);
        //postForObject分别有三个参数：请求地址，请求参数，返回的对象类型
//        return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
        ResponseEntity<CommonResult> entity = restTemplate.postForEntity(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
        if(entity.getStatusCode().is2xxSuccessful()){
            return entity.getBody();
        }else{
            return new CommonResult<>(444, "操作失败");
        }
    }
123456789101112
```

输入`http://localhost//consumer/payment/createEntity?serial=田七`，插入一条数据。
![在这里插入图片描述](img/2020060621291977.png)
![在这里插入图片描述](img/20200606214040809.png)

## Ribbon核心组件IRUle

IRUle接口的实现类：
![在这里插入图片描述](img/20200606213616967.png)
![在这里插入图片描述](img/20200606213539263.png)
*默认为RoundRobinRule轮询。*

### 替换

![在这里插入图片描述](img/20200607094235930.png)
**Ribbon的自定义配置类不可以放在@ComponentScan所扫描的当前包下以及子包下**，否则这个自定义配置类就会被所有的Ribbon客户端共享，达不到为指定的Ribbon定制配置，**而@SpringBootApplication注解里就有@ComponentScan注解**，所以不可以放在主启动类所在的包下。（因为Ribbon是客户端（消费者）这边的，所以Ribbon的自定义配置类是在客户端（消费者）添加，不需要在提供者或注册中心添加）

1. 所以Ribbon的自定义配置类不能放在springcloud包下，要在angenin包下再新建一个myrule包。
   ![在这里插入图片描述](img/20200607095113878.png)

2. 在此包下新建MySelfRule自定义配置类

   ```java
   @Configuration
   public class MySelfRule {
   
       @Bean
       public IRule myRule(){
           return new RandomRule();    //负载均衡机制改为随机
       }
   
   }
   123456789
   ```

3. 在主启动类上添加`@RibbonClient(name = "CLOUD-PAYMENT-SERVICE", configuration = MySelfRule.class)`
   name为指定的服务名（服务名必须与注册中心显示的服务名大小写一致）
   configuration为指定服务使用自定义配置（自定义负载均衡机制）

4. 启动eurekaserver集群，提供者集群，消费者。

5. 浏览器输入`http://localhost/consumer/payment/get/1`，多次刷新实现负载均衡为随机。

## Ribbon负载均衡——轮询算法

### RoundRobinRule原理

![在这里插入图片描述](img/20200607105533195.png)
取余

### RoundRobinRule源码

RoundRobinRule的核心为choose方法：

```java
public class RoundRobinRule extends AbstractLoadBalancerRule {
	//AtomicInteger原子整形类
    private AtomicInteger nextServerCyclicCounter;
	...
    public RoundRobinRule() {
    	//此时nextServerCyclicCounter是一个原子整形类，并且value为0
        nextServerCyclicCounter = new AtomicInteger(0);
    }
	...
	//ILoadBalancer选择的负载均衡机制，这里lb为轮询
    public Server choose(ILoadBalancer lb, Object key) {
    	//如果传入的lb没有负载均衡，为空
        if (lb == null) {
            log.warn("no load balancer");
            return null;
        }

        Server server = null;
        int count = 0;
        //还没选到执行的server，并且选择的次数没超过10次，进行选择server
        while (server == null && count++ < 10) {
        	//lb.getReachableServers获取所有状态是up的服务实例
            List<Server> reachableServers = lb.getReachableServers();
            //lb.getAllServers获取所有服务实例
            List<Server> allServers = lb.getAllServers();
            //状态为up的服务实例的数量
            int upCount = reachableServers.size();
            //所有服务实例的数量
            int serverCount = allServers.size();
			
			//如果up的服务实例数量为0或者服务实例为0，打印日志log.warn并返回server=null
            if ((upCount == 0) || (serverCount == 0)) {
                log.warn("No up servers available from load balancer: " + lb);
                return null;
            }
			
			//获取到接下来server的下标
            int nextServerIndex = incrementAndGetModulo(serverCount);
            //获取下一个server
            server = allServers.get(nextServerIndex);

			//如果
            if (server == null) {
                //线程让步，线程会让出CPU执行权，让自己或者其它的线程运行。（让步后，CPU的执行权也有可能又是当前线程）
                Thread.yield();
                //进入下次循环
                continue;
            }
			
			//获取的server还活着并且还能工作，则返回该server
            if (server.isAlive() && (server.isReadyToServe())) {
                return (server);
            }

            //否则server改为空
            server = null;
        }

		//选择次数超过10次，打印日志log.warn并返回server=null
        if (count >= 10) {
            log.warn("No available alive servers after 10 tries from load balancer: "
                    + lb);
        }
        return server;
    }


    private int incrementAndGetModulo(int modulo) {
    	//CAS加自旋锁
    	//CAS（Conmpare And Swap）：是用于实现多线程同步的原子指令。CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。更新一个变量的时候，只有当变量的预期值A和内存地址V当中的实际值相同时，才会将内存地址V对应的值修改为B。
    	//自旋锁：是指当一个线程在获取锁的时候，如果锁已经被其它线程获取，那么该线程将循环等待，然后不断的判断锁是否能够被成功获取，直到获取到锁才会退出循环。 
        for (;;) {
        	//获取value，即0
            int current = nextServerCyclicCounter.get();
            //取余，为1
            int next = (current + 1) % modulo;
            //进行CAS判断，如果此时在value的内存地址中，如果value和current相同，则为true，返回next的值，否则就一直循环，直到结果为true
            if (nextServerCyclicCounter.compareAndSet(current, next))
                return next;
        }
    }
    ...
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071727374757677787980818283
```

AtomicInteger的compareAndSet方法：

```java
public class AtomicInteger extends Number implements java.io.Serializable {
	...
    public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
    ...
}
1234567
```

### 手写一个轮询自定义配置类

##### 8001和8002微服务改造

在8001和8002的PaymentController中加上这个方法，用于测试我们的自定义轮询：

```java
    @GetMapping("/payment/lb")
    public String getPaymentLB(){
        return serverPort;
    }
1234
```

##### 80订单微服务改造

1. 去掉ApplicationContextConfig里restTemplate方法上的@LoadBalanced注解。

2. 在springcloud包下新建lb.ILoadBalancer接口（自定义负载均衡机制（面向接口））

   ```java
   public interface ILoadBalancer {
   
       //传入具体实例的集合，返回选中的实例
       ServiceInstance instances(List<ServiceInstance> serviceInstance);
   
   }
   123456
   ```

3. 在lb包下新建自定义ILoadBalancer接口的实现类

   ```java
   @Component  //加入容器
   public class MyLB implements ILoadBalancer {
   
       //新建一个原子整形类
       private AtomicInteger atomicInteger = new AtomicInteger(0);
   
       //
       public final int getAndIncrement(){
           int current;
           int next;
           do{
               current = this.atomicInteger.get();
               //如果current是最大值，重新计算，否则加1（防止越界）
               next = current >= Integer.MAX_VALUE ? 0 : current + 1;
   
           //进行CAS判断，如果不为true，进行自旋
           }while (!this.atomicInteger.compareAndSet(current, next));
           System.out.println("****第几次访问，次数next：" + next);
   
           return next;
       }
   
       @Override
       public ServiceInstance instances(List<ServiceInstance> serviceInstance) {
           //非空判断
           if(serviceInstance.size() <= 0){
               return null;
           }
           //进行取余
           int index = getAndIncrement() % serviceInstance.size();
           //返回选中的服务实例
           return serviceInstance.get(index);
       }
   }
   12345678910111213141516171819202122232425262728293031323334
   ```

4. 在OrderController添加：

   ```java
       @Resource
       private ILoadBalancer iLoadBalancer;
       @Resource
       private DiscoveryClient discoveryClient;
   	
       @GetMapping("/consumer/payment/lb")
       public String getPaymentLB(){
           //获取CLOUD-PAYMENT-SERVICE服务的所有具体实例
           List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
           if(instances == null || instances.size() <= 0){
               return null;
           }
   
           ServiceInstance serviceInstance = iLoadBalancer.instances(instances);
           URI uri = serviceInstance.getUri();
           System.out.println(uri);
   
           return restTemplate.getForObject(uri + "/payment/lb", String.class);
       }
   12345678910111213141516171819
   ```

5. 启动server集群，提供者集群，80消费者，然后在浏览器中输入`http://localhost/consumer/payment/lb`，多次刷新，实现自定义轮询。
   ![在这里插入图片描述](img/20200607163336842.png)

# 九、OpenFeign服务接口调用

## 简介

官网文档：https://cloud.spring.io/spring-cloud-static/Hoxton.SR1/reference/htmlsingle/#spring-cloud-openfeign
![在这里插入图片描述](img/20200607163822409.png)
Feign是一个声明式的web服务客户端，让编写web服务客户端变得非常容易，只需创建一个接口并在接口上添加注解即可。

![在这里插入图片描述](img/20200607164817985.png)
Feign与OpenFeign的区别
![在这里插入图片描述](img/20200607170020927.png)

## OpenFeign的使用（也是在消费者端）

![在这里插入图片描述](img/20200607203046585.png)

1. 新建模块cloud-consumer-feign-order80

2. pom

   ```xml
   <dependencies>
       <!-- openfeign -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
       <!-- eureka-client -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   12345678910111213141516171819202122232425262728293031323334353637383940414243
   ```

3. yml

   ```yml
   server:
     port: 80
   
   
   eureka:
     client:
       register-with-eureka: false
       service-url:
         defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
   123456789
   ```

4. 主启动类

   ```java
   @EnableFeignClients //激活feign
   @SpringBootApplication
   public class OrderFeignMain80 {
   
       public static void main(String[] args) {
           SpringApplication.run(OrderFeignMain80.class, args);
       }
   
   }
   123456789
   ```

5. 在springcloud包下新建service.PaymentFeignService接口
   *（业务逻辑接口+@FeignClient配置调用provider服务。）*

   新建PaymentFeignService接口并新增注解@FeignClient

   ```java
   //Feign封装了Ribbon和RestTemplate，实现负载均衡和发送请求
   @Component
   @FeignClient(value = "CLOUD-PAYMENT-SERVICE")    //作为feign的接口，找CLOUD-PAYMENT-SERVICE服务
   public interface PaymentFeignService {
   
       //直接复制8001的方法
       @GetMapping("/payment/get/{id}")
       public CommonResult getPaymentById(@PathVariable("id") Long id);
   
   }
   12345678910
   ```

6. 在springcloud包下新建controller.OrderFeignController

   ```java
   @Slf4j
   @RestController
   public class OrderFeignController {
   
       @Resource
       private PaymentFeignService paymentFeignService;
   
       @GetMapping("/consumer/payment/get/{id}")
       public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id){
           return paymentFeignService.getPaymentById(id);
       }
   
   }
   12345678910111213
   ```

7. 按照顺序启动项目（server集群，提供者集群，消费者），然后在浏览器输入`http://localhost/consumer/payment/get/1`，成功查询到数据，并且有负载均衡（轮询）。 ![在这里插入图片描述](img/20200607204313340.png)

总结：
![在这里插入图片描述](img/20200607205147170.png)

## OpenFeign超时控制

提供者在处理服务时用了3秒，提供者认为花3秒是正常，而消费者只愿意等1秒，1秒后，提供者会没返回数据，消费者就会造成超时调用报错。
所以需要双方约定好时间，不使用默认的。

### 模拟超时出错的情况

![在这里插入图片描述](img/20200608095152374.png)

1. 在8001的PaymentController里添加：（模拟服务处理时间长）

   ```java
       @GetMapping("/payment/feign/timeout")
       public String paymentFeignTimeout(){
           try {
               TimeUnit.SECONDS.sleep(3);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return serverPort;
       }
   123456789
   ```

2. 在80的PaymentFeignService中添加：

   ```java
   @GetMapping("/payment/feign/timeout")
   public String paymentFeignTimeout();
   12
   ```

3. 然后在80的OrderFeignController中添加：

   ```java
       @GetMapping("/consumer/payment/feign/timeout")
       public String paymentFeignTimeout(){
           //openFeign-ribbon，客户端一般默认等待1秒
           return paymentFeignService.paymentFeignTimeout();
       }
   12345
   ```

4. 启动server集群，提供者8001，消费者80
   `http://localhost/consumer/payment/feign/timeout`
   三秒后报错。
   ![在这里插入图片描述](img/20200608100640631.png)

![在这里插入图片描述](img/20200608100449820.png)

1. 在80的yml中添加：

   ```yml
   #没提示不管它，可以设置
   ribbon:
     #指的是建立连接后从服务器读取到可用资源所用的时间
     ReadTimeout: 5000
     #指的是建立连接使用的时间，适用于网络状况正常的情况下，两端连接所用的时间
     ConnectTimeout: 5000
   123456
   ```

2. 重新访问`http://localhost/consumer/payment/feign/timeout`，3秒后显示。
   ![在这里插入图片描述](img/20200608102002429.png)

## OpenFeign日志打印功能

![在这里插入图片描述](img/20200608102405693.png)

### 日志级别

![在这里插入图片描述](img/20200608102459608.png)

### 步骤

1. 配置日志bean
   在80的springcloud包下新建config.FeignConfig

```java
import feign.Logger;	//不要导错包

@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel(){
        //打印最详细的日志
        return Logger.Level.FULL;
    }

}
123456789101112
```

1. 在80的yml文件中添加：

   ```yml
   #开启日志的feign客户端
   logging:
     level:
       #feign日志以什么级别监控哪个接口
       com.angenin.springcloud.service.PaymentFeignService: debug	#写你们自己的包名
   12345
   ```

2. 启动项目，`http://localhost/consumer/payment/feign/timeout`
   ![在这里插入图片描述](img/20200608104212986.png)

# 十、Hystrix断路器

![在这里插入图片描述](img/20200608110710376.png)
![在这里插入图片描述](img/20200608111201407.png)

## 简介

![在这里插入图片描述](img/20200608111253677.png)
功能：**服务降级**，**服务熔断**，**接近实时的监控**，限流，隔离等。

## Hystrix重要概念

### 服务降级（fallback）

提供者和消费者都可以进行服务降级。（一般都是放在客户端（消费者））
![在这里插入图片描述](img/20200608113847222.png)
![在这里插入图片描述](img/20200608113900164.png)

### 服务熔断（break）

![在这里插入图片描述](img/20200608113944442.png)
![在这里插入图片描述](img/20200608114110865.png)

### 服务限流（flowlimit）

![在这里插入图片描述](img/20200608114039678.png)

## Hystrix案例

#### 构建

把7001改为单机版，方便后面进行案例测试。

1. 新建项目cloud-provider-hystrix-payment8001

2. pom

   ```xml
   <dependencies>
       <!-- hystrix-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--eureka client-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <!--   一个Java工具包     -->
       <dependency>
           <groupId>cn.hutool</groupId>
           <artifactId>hutool-all</artifactId>
           <version>5.1.0</version>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950
   ```

3. yml

   ```yml
   server:
     port: 8001
   
   
   spring:
     application:
       name: cloud-provider-hystrix-payment
   	
   
   eureka:
     client:
       register-with-eureka: true
       fetch-registry: true
       service-url:
         #单机版
         defaultZone: http://localhost:7001/eureka
         #集群版
   #      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
   123456789101112131415161718
   ```

4. 主启动类

   ```java
   @EnableEurekaClient
   @SpringBootApplication
   public class PaymentHystrixMain8001 {
   
       public static void main(String[] args) {
           SpringApplication.run(PaymentHystrixMain8001.class, args);
       }
   
   }
   123456789
   ```

5. service

   ```java
   @Service
   public class PaymentService {
   
       //正常访问方法
       public String paymentInfo_OK(Integer id){
           return "线程池：" + Thread.currentThread().getName() + "\tpaymentInfo_OK，id：" + id;
       }
   
   
       //超时访问方法
       public String paymentInfo_TimeOut(Integer id){
           int timeNumber = 3;
           try {
               TimeUnit.SECONDS.sleep(timeNumber);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return "线程池：" + Thread.currentThread().getName() +
                   "\tpaymentInfo_TimeOut，id：" + id + "，耗时：" + timeNumber + "秒";
       }
   
   }
   12345678910111213141516171819202122
   ```

6. controller

   ```java
   @Slf4j
   @RestController
   public class PaymentController {
   
       @Resource
       PaymentService paymentService;
   
       @Value("${server.port}")    //spring的@Value注解
       private String ServerPort;
   
       @GetMapping("/payment/hystrix/ok/{id}")
       public String paymentInfo_OK(@PathVariable("id") Integer id){
           String result = paymentService.paymentInfo_OK(id);
           log.info("******result：" + result);
           return result;
       }
   
       @GetMapping("/payment/hystrix/timeout/{id}")
       public String paymentInfo_TimeOut(Integer id){
           String result = paymentService.paymentInfo_OK(id);
           log.info("******result：" + result);
           return result;
       }
   
   }
   12345678910111213141516171819202122232425
   ```

7. 启动7001和8001
   `http://localhost:8001/payment/hystrix/ok/1`
   ![在这里插入图片描述](img/20200608144948608.png)
   `http://localhost:8001/payment/hystrix/timeout/1`
   ![在这里插入图片描述](img/20200608145241573.png)

![在这里插入图片描述](img/20200608144837222.png)

#### 高并发测试

![在这里插入图片描述](img/20200608145827536.png)

##### 安装JMeter

JMeter下载地址：http://jmeter.apache.org/download_jmeter.cgi

下载tgz和zip都可以：
![在这里插入图片描述](img/20200608145715640.png)
进入解压后的目录的bin目录，找到jmeter.properties文件，修改语言zh_CN。
![在这里插入图片描述](img/2020060815044582.png)
从终端进入bin目录，输入`./jmeter`运行jmeter，修改成中文版。
![在这里插入图片描述](img/20200608150856861.png)
![在这里插入图片描述](img/20200608150911815.png)

##### 进行高并发测试

测试`http://localhost:8001/payment/hystrix/timeout/1`
![在这里插入图片描述](img/20200608151226755.png)
![在这里插入图片描述](img/20200608152442774.png)
然后去访问`http://localhost:8001/payment/hystrix/ok/1`，访问速度变慢了。
![在这里插入图片描述](img/20200608152534193.png)

##### 新建80

1. 新建cloud-consumer-feign-hystrix-order80

2. pom

   ```xml
   <dependencies>
       <!-- openfeign -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
       <!--   hystrix     -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
       </dependency>
       <!--eureka client-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   123456789101112131415161718192021222324252627282930313233343536373839404142434445464748
   ```

3. yml

   ```yml
   server:
     port: 80
   
   
   eureka:
     client:
       register-with-eureka: false
       service-url:
         defaultZone: http://localhost:7001/eureka
   
   #需要加上，否则会报错
   ribbon:
     ReadTimeout: 4000
     ConnectTimeout: 4000
   1234567891011121314
   ```

4. 主启动类

   ```java
   @EnableEurekaClient
   @EnableFeignClients
   @SpringBootApplication
   public class OrderHystrixMain80 {
   
       public static void main(String[] args) {
           SpringApplication.run(OrderHystrixMain80.class, args);
       }
   
   }
   12345678910
   ```

5. service

   ```java
   @Component
   @FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
   public interface PaymentHystrixService {
   
       @GetMapping("/payment/hystrix/ok/{id}")
       public String paymentInfo_OK(@PathVariable("id") Integer id);
       
       @GetMapping("/payment/hystrix/timeout/{id}")
       public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
   }
   12345678910
   ```

6. controller

   ```java
   @Slf4j
   @RestController
   public class OrderHystrixController {
   
       @Resource
       private PaymentHystrixService paymentHystrixService;
   
   
       @GetMapping("/consumer/payment/hystrix/ok/{id}")
       public String paymentInfo_OK(@PathVariable("id") Integer id){
           String result = paymentHystrixService.paymentInfo_OK(id);
           return result;
       }
   
       @GetMapping("/consumer/payment/hystrix/timeout/{id}")
       public String paymentInfo_TimeOut(@PathVariable("id") Integer id){
           String result = paymentHystrixService.paymentInfo_TimeOut(id);
           return result;
       }
   
   }
   123456789101112131415161718192021
   ```

7. 启动80，进行测试
   `http://localhost/consumer/payment/hystrix/ok/1`
   ![在这里插入图片描述](img/20200608155353468.png)

   `http://localhost/consumer/payment/hystrix/timeout/1`
   ![在这里插入图片描述](img/20200608160024174.png)

8. 启动jmeter，然后再进行测试
   ![在这里插入图片描述](img/20200608160132790.png)

#### 故障现象、导致原因以及解决

现象：
![在这里插入图片描述](img/20200608160322883.png)
![在这里插入图片描述](img/20200608160503320.png)
解决：
![在这里插入图片描述](img/20200608160728995.png)
![在这里插入图片描述](img/20200608160850186.png)

### 服务降级

![在这里插入图片描述](img/20200608161511347.png)
![在这里插入图片描述](img/20200608162502904.png)

#### 提供者

1. 修改8001中PaymentService的paymentInfo_TimeOut方法，并添加paymentInfo_TimeOutHandler方法：

   ```java
       @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler", commandProperties = {
               //设置自身超时调用时间的峰值为3秒，峰值内可以正常运行，超过了需要有兜底的方法处理，服务降级fallback
               @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
       })
       public String paymentInfo_TimeOut(Integer id){
           int timeNumber = 5;
           //int i = 1 / 0;
           try {
               TimeUnit.SECONDS.sleep(timeNumber);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return "线程池：" + Thread.currentThread().getName() +
                   "\tpaymentInfo_TimeOut，id：" + id + "，耗时：" + timeNumber + "秒";
       }
       public String paymentInfo_TimeOutHandler(Integer id){
           return "8001提供者，线程池：" + Thread.currentThread().getName() + 
                   "\tpaymentInfo_TimeOutHandler系统繁忙，请稍后再试，id：" + id;
       }
   12345678910111213141516171819
   ```

2. 然后在8001的主启动类上添加`@EnableCircuitBreaker`注解，启用断路器。

3. 启动7001和8001，测试8001的fallback，`http://localhost:8001/payment/hystrix/timeout/1`成功进入fallback方法。（并且fallback方法是用Hystrix的线程池）
   ![在这里插入图片描述](img/2020060816415940.png)

4. 去掉sleep，改为 1 / 0，测试方法运行异常，`http://localhost:8001/payment/hystrix/timeout/1`，也可以进入fallback方法。
   ![在这里插入图片描述](img/20200608164714747.png)

![在这里插入图片描述](img/20200608164938767.png)

#### 消费者

![在这里插入图片描述](img/20200608171946683.png)

1. 在80的yml中添加：

   ```yml
   feign:
     hystrix:
       enabled: true
   123
   ```

2. 在主启动类添加`@EnableHystrix`注解。

3. 修改OrderHystrixController的paymentInfo_TimeOut方法，并添加paymentTimeOutFallbackMethod方法：

   ```java
       @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
               @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
       })
       @GetMapping("/consumer/payment/hystrix/timeout/{id}")
       public String paymentInfo_TimeOut(@PathVariable("id") Integer id){
           String result = paymentHystrixService.paymentInfo_TimeOut(id);
           return result;
       }
       public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id){
           return "消费者80，支付系统繁忙";
       }
   1234567891011
   ```

4. 启动7001，8001，80，`http://localhost/consumer/payment/hystrix/timeout/1`（如果是提供者那边出问题，并且消费者设置了fallback，会优先进入消费者的fallback）
   ![在这里插入图片描述](img/20200608173224496.png)

5. 在中添加`int i = 1 / 0;`，运行异常也会进入80的fallback方法。
   ![在这里插入图片描述](img/20200608173757412.png)

#### 目前的问题和解决办法

![在这里插入图片描述](img/20200608174333300.png)

##### 代码膨胀的解决办法

解决办法：设置全局fallback方法。
![在这里插入图片描述](img/20200608174410198.png)
![在这里插入图片描述](img/20200608174612625.png)
![在这里插入图片描述](img/20200608174640149.png)

1. 在80的OrderHystrixController中添加全局fallback方法：

```java
    //全局fallback方法，不能有传参
    public String payment_Global_FallbackMethod(){
        return "Global异常处理信息，请稍后再试！";
    }
1234
```

1. 并在OrderHystrixController类上加上`@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")`，设置全局fallback方法。

2. 把paymentInfo_TimeOut方法的@HystrixCommand

   ```java
   //    @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
   //            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
   //    })
       @HystrixCommand
   1234
   ```

3. 进行测试，`http://localhost/consumer/payment/hystrix/timeout/1`
   ![在这里插入图片描述](img/20200608185611971.png)

##### 混乱的解决办法

![在这里插入图片描述](img/20200608200933729.png)
![在这里插入图片描述](img/20200608201024518.png)
![在这里插入图片描述](img/20200608201043786.png)

###### 模拟宕机场景

1. 在80的service包下新建PaymentFallbackService类，实现PaymentHystrixService接口

   ```java
   //统一为接口里面的方法进行异常处理
   @Component
   public class PaymentFallbackService implements PaymentHystrixService {
       @Override
       public String paymentInfo_OK(Integer id) {
           return "----PaymentFallbackService\t fallback-paymentInfo_OK----";
       }
   
       @Override
       public String paymentInfo_TimeOut(Integer id) {
           return "----PaymentFallbackService\t fallback-paymentInfo_TimeOut----";
       }
   }
   12345678910111213
   ```

2. 要在yml中加上：（我们在之前就加上了）

   ```yml
   feign:
     hystrix:
       enabled: true
   123
   ```

3. 然后给PaymentHystrixService接口的@FeignClient注解加上`fallback = PaymentFallbackService.class`属性，用于出错进行fallback处理。
   ![在这里插入图片描述](img/20200608202526256.png)

4. 启动7001，8001，80，然后先访问``，成功访问
   ![在这里插入图片描述](img/20200608202753614.png)

5. 然后关掉8001，模拟提供者宕机，刷新页面
   ![在这里插入图片描述](img/20200608202822142.png)

![在这里插入图片描述](img/20200608202919567.png)

### 服务熔断

![在这里插入图片描述](img/2020060821090792.png)

#### 实操

![在这里插入图片描述](img/20200608211640725.png)

1. 在8001的PaymentService中添加

   ```java
   	@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
           @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),                      //开启断路器
           @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),         //请求总数阈值（默认20）
           @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),   //休眠时间窗口期（休眠多久进入半开模式（单位毫秒，默认5秒））
           @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"),       //请求次数的错误率达到多少跳闸（百分率%，默认50%）
   })
       public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
           if(id < 0){
               throw  new RuntimeException("****id 不能为负数");
           }
           String serialNumber = IdUtil.simpleUUID();
   
           return  Thread.currentThread().getName() + "\t" + "调用成功，流水号：" + serialNumber;
       }
       public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id){
           return "id 不能为负数,请稍后再试， id: " + id;
       }
   1234567891011121314151617
   ```

2. 在8001的PaymentController中添加

   ```java
   @GetMapping("/payment/circuit/{id}")
   public String paymentCircuitBreaker(@PathVariable("id") Integer id){
       String result = paymentService.paymentCircuitBreaker(id);
       log.info("******result：" + result);
       return result;
   }
   123456
   ```

3. 启动7001和8001
   `http://localhost:8001/payment/circuit/-1`（输入超过6次进入熔断）
   ![在这里插入图片描述](img/20200608223649310.png)
   熔断10秒内就算是正确的请求也返回错误信息。
   ![在这里插入图片描述](img/20200608224017409.png)
   10秒后进入半开模式，对请求进行处理，此时如果是正确的请求，那么就关闭熔断，否则再次进入熔断，10秒后再次开启半开模式，对请求进行处理，直到半开模式处理到正确请求。
   ![在这里插入图片描述](img/20200608224005451.png)

#### 总结

https://martinfowler.com/bliki/CircuitBreaker.html
![在这里插入图片描述](img/20200608224127288.png)

我的总结：如果请求次数的错误率超过指定值，开启熔断，经过一段时间后，变为半开模式，然后放进一个请求进行处理，如果请求处理成功，关闭熔断；如果还是报错，继续进入熔断，再经过一段时间后，变为半开模式，再进行对下一个请求进行处理，一直在熔断，半开模式来回切换，直到请求成功，关闭熔断。

![在这里插入图片描述](img/20200608224155273.png)
![在这里插入图片描述](img/20200608230402814.png)
官网步骤：
![在这里插入图片描述](img/20200608224336249.png)

断路器在什么情况下开始起作用：

https://github.com/Netflix/Hystrix/wiki/How-it-Works
![在这里插入图片描述](img/20200608221343984.png)
![在这里插入图片描述](img/20200608230130722.png)
断路器开启或关闭的条件：
![在这里插入图片描述](img/2020060823003693.png)
断路器打开之后：
![在这里插入图片描述](img/20200608230235175.png)

### 服务限流

会在后面高级篇alibaba的Sentinel讲解。



## Hystrix工作流程

https://github.com/Netflix/Hystrix/wiki/How-it-Works
![在这里插入图片描述](img/2020060909353016.png)
官方图例：
![在这里插入图片描述](img/20200609094213297.png)

## 服务监控HystrixDashboard

## 简介

![在这里插入图片描述](img/20200609094828760.png)

## 仪表盘9001

![在这里插入图片描述](img/20200609094937545.png)

1. 新建模块cloud-consumer-hystrix-dashboard9001

2. pom

   ```xml
   <dependencies>
       <!--   hystrix仪表盘图形化     -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
       </dependency>
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   12345678910111213141516171819202122232425262728293031323334353637
   ```

3. yml

   ```yml
   server:
     port: 9001
   12
   ```

4. 主启动类

   ```java
   @EnableHystrixDashboard //启用Hystrix仪表板
   @SpringBootApplication
   public class HystrixDashboard9001 {
   
       public static void main(String[] args) {
           SpringApplication.run(HystrixDashboard9001.class, args);
       }
   
   }
   123456789
   ```

5. 启动9001，在浏览器中输入`http://localhost:9001/hystrix`
   ![在这里插入图片描述](img/20200609100541257.png)

## 断路器演示（服务监控hystrixDashboard）

注意：所有微服务提供者都需要在pom中引入监控依赖。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
1234
```

修改8001
![在这里插入图片描述](img/2020060910090526.png)
在8001的主启动类中添加：

```java
   /**
    * 此配置是为了服务监控而配置，与服务容错本身无关，springcloud升级后的坑
    * ServletRegistrationBean因为SpringBoot的默认路径不是 “/hystrix.stream"
    * 只要在自己的项目里配置上下的servlet就可以了
    */
   @Bean
   public ServletRegistrationBean getServlet() {
       HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet() ;
       ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
       registrationBean.setLoadOnStartup(1);
       registrationBean.addUrlMappings("/hystrix.stream");
       registrationBean.setName("HystrixMetricsStreamServlet");
       return  registrationBean;
   }
1234567891011121314
```

![在这里插入图片描述](img/20200609101257700.png)

### 监控测试

启动9001，7001，8001

1. 9001监控8001
   ![在这里插入图片描述](img/20200609101801470.png)
   ![在这里插入图片描述](img/2020060910204091.png)
   ![在这里插入图片描述](img/20200609102048243.png)
2. 在浏览器输入`http://localhost:8001/payment/circuit/1`和`http://localhost:8001/payment/circuit/-1`
   ![在这里插入图片描述](img/20200609102306534.png)
   多次输入`http://localhost:8001/payment/circuit/-1`错误的访问。
   ![在这里插入图片描述](img/20200609102506818.png)
   稍微等一会，然后输入正确的访问`http://localhost:8001/payment/circuit/1`，就会熔断就会关闭。
   ![在这里插入图片描述](img/20200609102729898.png)

![在这里插入图片描述](img/20200609102611326.png)
7色：
![在这里插入图片描述](img/20200609102823528.png)
1圈：
![在这里插入图片描述](img/20200609102909116.png)
1线：
![在这里插入图片描述](img/20200609102946569.png)
整图说明：
![在这里插入图片描述](img/20200609103008938.png)
![在这里插入图片描述](img/20200609103023793.png)
整图说明2：
![在这里插入图片描述](img/20200609103129409.png)
![在这里插入图片描述](img/20200609103204583.png)

# 十一、zuul路由网关

zuul核心人员走了两个，zuul2的研发过久，spring公司等不及，自己研发的Gateway网关。

zuul路由网管，阳哥没讲，跳过，没笔记。
https://github.com/Netflix/zuul/wiki





# 十二、Gateway新一代网关

https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/

## 简介

![在这里插入图片描述](img/2020060921124624.png)
![在这里插入图片描述](img/20200609211337862.png)
![在这里插入图片描述](img/20200609211403820.png)
![在这里插入图片描述](img/20200609211609164.png)
![在这里插入图片描述](img/20200609211728665.png)
![在这里插入图片描述](img/20200609211706727.png)
![在这里插入图片描述](img/20200609211837236.png)

![在这里插入图片描述](img/20200609212209940.png)
![在这里插入图片描述](img/20200609212424173.png)
![在这里插入图片描述](img/20200609212644782.png)
![在这里插入图片描述](img/202006092128364.png)
![在这里插入图片描述](img/20200609212937813.png)
![在这里插入图片描述](img/20200609213053599.png)
![在这里插入图片描述](img/20200609213214211.png)

## 三大核心概念

### Route（路由）

![在这里插入图片描述](img/20200609221437913.png)

### Predicate（断言）

![在这里插入图片描述](img/20200609221559367.png)

### Filter（过滤）

![在这里插入图片描述](img/20200617094327739.png)

### 总体

![在这里插入图片描述](img/20200617094501319.png)
![在这里插入图片描述](img/20200617094535575.png)

## Gateway工作流程

官网总结
![在这里插入图片描述](img/20200617094752770.png)
![在这里插入图片描述](img/2020061709485433.png)
![在这里插入图片描述](img/20200617095001771.png)

## 入门配置

1. 新建模块cloud-gateway-gateway9527

2. pom

   ```xml
   <dependencies>
       <!--gateway-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-gateway</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <!--eureka client(通过微服务名实现动态路由)-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435
   ```

3. yml

   ```yml
   server:
     port: 9527
   
   spring:
     application:
       name: cloud-gateway
   
   eureka:
     instance:
       hostname: cloud-gateway-service
     client:
       fetch-registry: true
       register-with-eureka: true
       service-url:
         defaultZone: http://eureka7001.com:7001/eureka/
   123456789101112131415
   ```

4. 主启动类GatewayMain9527

   ```java
   @SpringBootApplication
   @EnableEurekaClient
   public class GatewayMain9527 {
       public static void main(String[] args) {
           SpringApplication.run(GatewayMain9527.class, args);
       }
   }
   1234567
   ```

5. 修改yml文件
   ![在这里插入图片描述](img/20200617102349293.png)
   ![在这里插入图片描述](img/2020061710242031.png)

   ```yml
   server:
     port: 9527
   
   spring:
     application:
       name: cloud-gateway
     cloud:
       gateway:
         routes:
           - id: payment_route # 路由的id,没有规定规则但要求唯一,建议配合服务名
             #匹配后提供服务的路由地址
             uri: http://localhost:8001
             predicates:
               - Path=/payment/get/** # 断言，路径相匹配的进行路由
   
           - id: payment_route2
             uri: http://localhost:8001
             predicates:
               - Path=/payment/lb/** #断言,路径相匹配的进行路由
   
   eureka:
     instance:
       hostname: cloud-gateway-service
     client:
       fetch-registry: true
       register-with-eureka: true
       service-url:
         defaultZone: http://eureka7001.com:7001/eureka/
   12345678910111213141516171819202122232425262728
   ```

6. 测试，启动7001，cloud-provider-payment8001，9527
   ![在这里插入图片描述](img/2020061710415232.png)
   ![在这里插入图片描述](img/20200617104243634.png)
   `http://localhost:8001/payment/get/1`
   ![在这里插入图片描述](img/2020061710432068.png)
   `http://localhost:9527/payment/get/1`
   ![在这里插入图片描述](img/20200617104421762.png)

### Gateway网关路由的两种配置方式

1. 在配置文件中配置
   在配置文件yml中配置

2. 在配置类中配置
   代码中注入RouteLocator的Bean ![在这里插入图片描述](img/20200617105050308.png)
   ![在这里插入图片描述](img/20200617105029632.png)
   新建config.GatewayConfig

   ```java
   @Configuration
   public class GatewayConfig {
   
       @Bean
       public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
           RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
   
           routes.route("path_route_angenin",  //id
                   r -> r.path("/guonei")  //访问 http://localhost:9527/guonei
                           .uri("http://news.baidu.com/guonei"));  //就会转发到 http://news.baidu.com/guonei
   
           routes.route("path_route_angenin2",  //id
                   r -> r.path("/guoji")  //访问 http://localhost:9527/guoji
                           .uri("http://news.baidu.com/guoji"));  //就会转发到 http://news.baidu.com/guonji
   
           return routes.build();
       }
   
   //    @Bean
   //    public RouteLocator customRouteLocator2(RouteLocatorBuilder routeLocatorBuilder){
   //        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
   //
   //        routes.route("path_route_angenin2",  //id
   //                r -> r.path("/guoji")  //访问 http://localhost:9527/guoji
   //                        .uri("http://news.baidu.com/guoji"));  //就会转发到 http://news.baidu.com/guonji
   //
   //        return routes.build();
   //    }
   
   }
   123456789101112131415161718192021222324252627282930
   ```

测试，启动7001，8001，9527
`http://localhost:9527/guonei`
![在这里插入图片描述](img/20200617111634913.png)
`http://localhost:9527/guoji`
![在这里插入图片描述](img/20200617112128289.png)

## 通过微服务名实现动态路由

![在这里插入图片描述](img/20200617113401547.png)
修改yml文件

```yml
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true  #开启从注册中心动态创建路由的功能，利用微服务名称进行路由(默认false)
      routes:
        - id: payment_route #路由的id,没有规定规则但要求唯一,建议配合服务名
#          uri: http://localhost:8001  #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service
          predicates:
            - Path=/payment/get/** #断言，路径相匹配的进行路由

        - id: payment_route2
#          uri: http://localhost:8001
          uri: lb://cloud-payment-service
          predicates:
            - Path=/payment/lb/** #断言,路径相匹配的进行路由
1234567891011121314151617181920
```

enabled默认false：
![在这里插入图片描述](img/20200617113049949.png)

测试，启动7001，8001，8002，9527
`http://localhost:9527/payment/lb`
![在这里插入图片描述](img/20200617113531930.png)
![在这里插入图片描述](img/20200617113524542.png)

## Predicate的使用

![在这里插入图片描述](img/20200617113741744.png)
官网：https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/#gateway-request-predicates-factories
![在这里插入图片描述](img/20200617114358789.png)
![在这里插入图片描述](img/20200617114414889.png)

常用的Route Predicate
![在这里插入图片描述](img/20200617114705678.png)

### After/Before/Between

新建测试类T2

```java
public class T2 {

    public static void main(String[] args) {
    	//获取当前时间串
        ZonedDateTime now = ZonedDateTime.now();
        System.out.println(now);
        //2020-06-17T11:53:40.325+08:00[Asia/Shanghai]
    }

}
12345678910
```

然后在yml中的`predicates:`加上

```yml
			#指定时间后才能访问（After）时间往后写一小时
            - After=2020-06-17T12:53:40.325+08:00[Asia/Shanghai]
12
```

测试，启动7001，8001，8002，9527
`http://localhost:9527/payment/lb`
![在这里插入图片描述](img/20200617120529161.png)

Before和Between作用一样：

```yml
			#指定时间前才能访问（Before）
            - Before=2020-06-17T11:53:40.325+08:00[Asia/Shanghai]
            #指定时间内才能访问（Between）
            - Between=2020-06-17T11:53:40.325+08:00[Asia/Shanghai],2020-06-17T12:53:40.325+08:00[Asia/Shanghai]
1234
```

### Cookie

![在这里插入图片描述](img/20200617152542645.png)
![在这里插入图片描述](img/20200617152501632.png)
在yml中的`predicates:`加上（记得把after的时间改成已经过去的时间，时间没到访问不了）

```yml
          - Cookie=username,angenin   #带Cookie，并且username的值为angenin
1
```

1. 不带cookie访问
   打开终端，输入`curl http://localhost:9527/payment/lb`（直接访问失败）
   ![在这里插入图片描述](img/20200617154450395.png)
2. 带cookie访问
   输入`curl http://localhost:9527/payment/lb --cookie "username=angenin"`
   ![在这里插入图片描述](img/20200617155016820.png)

### Header

![在这里插入图片描述](img/20200617155221124.png)
![在这里插入图片描述](img/20200617155209547.png)
注释掉其他两个，加上Header

```yml
#            - After=2020-06-17T12:53:40.325+08:00[Asia/Shanghai]
#            - Cookie=username,angenin   #带Cookie，username的值为angenin
            - Header=X-Request-Id, \d+   #请求头要有 X-Request-Id属性并且值为整数的正则表达式
123
```

重启9527，然后在终端输入`http://localhost:9527/payment/lb -H "X-Request-Id:123"`
![在这里插入图片描述](img/20200617155839384.png)

### Host

![在这里插入图片描述](img/20200617161726732.png)
加上：

```yml
            - Host=**.angenin.com	#Host: xxx.angenin.com 请求是Host必须有**.angenin.com
1
```

重启9527
`http://localhost:9527/payment/lb -H "Host: www.angenin.com"`
![在这里插入图片描述](img/20200617162116639.png)

### Method

![在这里插入图片描述](img/20200617163007922.png)

```yml
            - Method=GET	#只允许get请求访问
1
```

### Path

![在这里插入图片描述](img/20200617160128510.png)

```yml
			#访问的url地址有 /payment/lb/ 才能访问
			- Path=/payment/lb/**	
12
```

已经用过了，这里不进行演示。

### Query

![在这里插入图片描述](img/20200617163131624.png)

```yml
            - Query=username, \d+   #url请求地址必须带上username参数，并且值必须为整数
1
```

`http://localhost:9527/payment/lb?username=110`
![在这里插入图片描述](img/20200617163931254.png)
![在这里插入图片描述](img/20200617164126851.png)
![在这里插入图片描述](img/20200617164140278.png)

### 总结

![在这里插入图片描述](img/2020061716423856.png)

## Filter的使用

![在这里插入图片描述](img/20200617164642463.png)
![在这里插入图片描述](img/20200617164602514.png)
![在这里插入图片描述](img/20200617164846762.png)
GatewayFilter（31种）
Global Filter（10种）

这里以`AddRequestParameter`为代表。

### 自定义过滤器

![在这里插入图片描述](img/20200617165926400.png)
新建filter.MyLogGateWayFilter

```java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {


    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("**************come in MyLogGateWayFilter：" + new Date());
        //获取request中的uname参数
        String uname = exchange.getRequest().getQueryParams().getFirst("uname");

        if(uname == null){
            log.info("*******用户名为null，非法用户！！");
            //设置响应，不被接受
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);

            return exchange.getResponse().setComplete();
        }

        //返回chain.filter(exchange)，放行
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        //返回值是过滤器的优先级，越小优先级越高（最小-2147483648，最大2147483648）
        return 0;
    }
}
1234567891011121314151617181920212223242526272829
```

启动7001，8001，8002，9527
`http://localhost:9527/payment/lb?uname=111`
![在这里插入图片描述](img/20200617171704814.png)
`http://localhost:9527/payment/lb?xxx=111`
![在这里插入图片描述](img/20200617171847390.png)

# 十三、SpringCloud Config分布式配置中心

![在这里插入图片描述](img/20200617205139981.png)
![在这里插入图片描述](img/20200617205200384.png)
![在这里插入图片描述](img/20200617205312367.png)
![在这里插入图片描述](img/20200617205453318.png)
![在这里插入图片描述](img/20200617210644394.png)
![在这里插入图片描述](img/20200617210752450.png)

## Config服务端配置与测试

![在这里插入图片描述](img/2020061721492237.png)

1. 在GitHub上新建一个名为springcloud-config的新Repository（需要是public的仓库，private的访问不了）
   ![在这里插入图片描述](img/20200617211235973.png)
   ![在这里插入图片描述](img/2020061721132953.png)
   创建后，点击复制，然后黏贴到终端（可以先新建一个springcloud2020目录，等下用来clone到本地的存放目录）。（需要在本地安装好git）
   ![在这里插入图片描述](img/20200617212419203.png)
   ![在这里插入图片描述](img/20200617212511267.png)
   此时，这个springcloud2020就是我们本地的一个新的git仓库了（git init）。
   ![在这里插入图片描述](img/20200617214131459.png)
   ![在这里插入图片描述](img/20200617212524352.png)
   创建myorder文件夹
   ![在这里插入图片描述](img/20200617213626943.png)
   ![在这里插入图片描述](img/20200617213551828.png)
   创建视频中的yml文件
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200617213626943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2OTAzMjYx,size_16,color_FFFFFF,t_70)![在这里插入图片描述](img/20200617212928333.png)
   ![在这里插入图片描述](img/20200617213715919.png)

2. clone到本地，复制地址，然后终端进入springcloud2020文件夹里，输入`git clone 你们的仓库地址`
   ![在这里插入图片描述](img/20200617213945709.png)
   ![在这里插入图片描述](img/20200617214403662.png)
   ![在这里插入图片描述](img/20200617214519378.png)
   ![在这里插入图片描述](img/20200617214510992.png)

3. 新建模块cloud-config-center-3344

4. pom

   ```xml
   <dependencies>
       <!--config server-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-config-server</artifactId>
       </dependency>
       <!--eureka client(通过微服务名实现动态路由)-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   12345678910111213141516171819202122232425262728293031323334353637
   ```

5. yml
   点击可以切换为ssh模式的地址（就是视频中的@git… ，不过ssh需要先配置才能使用，所以用https模式的地址比较方便）
   ![在这里插入图片描述](img/20200617220959809.png)

   ```yml
   server:
     port: 3344
   
   
   spring:
     application:
       name: cloud-config-center #注册进Eureka服务器的微服务名
     cloud:
       config:
         server:
           git:
             uri: https://github.com/angenin/springcloud-config.git  #git的仓库地址
             search-paths:   #搜索目录
               - springcloud-config
         label: master   #读取的分支
   
   
   eureka:
     client:
       service-url: 
         defaultZone: http://localhost:7001/eureka   #服务注册到的eureka地址
   
   12345678910111213141516171819202122
   ```

6. 主启动类

   ```java
   @EnableConfigServer
   @SpringBootApplication
   public class ConfigCenterMain3344 {
       
       public static void main(String[] args) {
           SpringApplication.run(ConfigCenterMain3344.class, args);
       }
       
   }
   123456789
   ```

7. 修改hosts文件，增加映射
   终端输入`sudo vim /private/etc/hosts`，再输入本机密码，按i进入编辑模式，在最下行加入

   ```shell
   127.0.0.1       config-3344.com
   1
   ```

   ![在这里插入图片描述](img/20200617222739259.png)
   按esc键，然后输入`:wq!`强制保存退出。

8. 在GitHub中的配置文件加入（点击这个文件，然后点击笔形状的按钮进行编辑）

   ```yml
   #config-dev.yml
   config:
     info: "master branch,springcloud-config/config-dev.yml version=1"
     
   #######################################
   
   #config-prod.yml
   config:
     info: "master branch,springcloud-config/config-prod.yml version=1"
   
   #######################################
   
   #config-test.yml
   config:
     info: "master branch,springcloud-config/config-test.yml version=1"
   123456789101112131415
   ```

9. 启动7001，3344，然后在浏览器输入`http://config-3344.com:3344/master/config-dev.yml`（成功获取到github上的配置文件数据）
   ![在这里插入图片描述](img/20200617223928696.png)

### 配置的读取规则

![在这里插入图片描述](img/20200617230321552.png)
![在这里插入图片描述](img/20200617224233530.png)
第一种（我们上面用的就是这一种）
![在这里插入图片描述](img/20200617225126860.png)
`http://config-3344.com:3344/master/config-test.yml`
![在这里插入图片描述](img/20200617230101338.png)
第二种（分支不写，默认master分支）
![在这里插入图片描述](img/20200617225536589.png)
`http://config-3344.com:3344/config-test.yml`
![在这里插入图片描述](img/20200617230135830.png)
第三种（反着写）
![在这里插入图片描述](img/20200617225738329.png)
`http://config-3344.com:3344/config/test/master`
![在这里插入图片描述](img/20200617230219140.png)

## Config客户端配置与测试

![在这里插入图片描述](img/2020061809263857.png)

1. 新建模块cloud-config-client-3355

2. pom

   ```xml
   <dependencies>
       <!--config server-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-config</artifactId>
       </dependency>
       <!--eureka client(通过微服务名实现动态路由)-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   12345678910111213141516171819202122232425262728293031323334353637
   ```

3. bootstrap.yml（系统级别的配置文件）
   ![在这里插入图片描述](img/20200618093402439.png)

   ```yml
   server:
     port: 3355
   
   
   spring:
     application:
       name: config-client
     cloud:
       config: #config客户端配置
         label: master   #分支名称
         name: config    #配置文件名称       这三个综合：master分支上的config-dev.yml的配置文件
         profile: dev    #读取后缀名称       被读取到http://config-3344.com:3344/master/config/dev
         uri: http://localhost:3344  #配置中心地址
   
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka   #服务注册到的eureka地址
   12345678910111213141516171819
   ```

4. 主启动类

   ```java
   @EnableEurekaClient
   @SpringBootApplication
   public class ConfigClientMain3355 {
       
       public static void main(String[] args) {
           SpringApplication.run(ConfigClientMain3355.class, args);
       }
       
   }
   123456789
   ```

5. controller（读取GitHub的配置文件）

   ```java
   @RestController
   public class ConfigClientController {
   
       @Value("${config.info}")	//spring的@Value注解
       private String configInfo;
   
       @GetMapping("/configInfo")
       public String getConfigInfo(){
           return configInfo;
       }
   
   }
   123456789101112
   ```

6. 测试，启动7001，3344，3355
   ![在这里插入图片描述](img/20200618095559492.png)
   3344测试，`http://config-3344.com:3344/master/config-dev.yml`
   ![在这里插入图片描述](img/20200618095527314.png)
   3355测试，`http://localhost:3355/configInfo`
   ![在这里插入图片描述](img/20200618095534178.png)

### 动态刷新问题

![在这里插入图片描述](img/20200618100128735.png)

1. 修改GitHub上的config-dev.yml文件的版本号为2。
2. 刷新`http://config-3344.com:3344/master/config-dev.yml`，版本号发生改变。
   ![在这里插入图片描述](img/20200618100518819.png)
3. 刷新`http://localhost:3355/configInfo`，没有改变。
   ![在这里插入图片描述](img/20200618100603408.png)
4. 重启3355，刷新`http://localhost:3355/configInfo`，读取到最新的版本号。
   ![在这里插入图片描述](img/20200618100734915.png)

## Config客户端之动态刷新

![在这里插入图片描述](img/20200618100934587.png)

1. 往config客户端3355在pom中添加（上面已经加了）

   ```xml
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
   1234
   ```

2. 然后在bootstrap.yml中添加

   ```yml
   #暴露监控端点
   management:
     endpoints:
       web:
         exposure:
           include: "*"
   123456
   ```

3. 在ConfigClientController类上加上`@RefreshScope`注解。

4. 重启3355。

5. 修改GitHub上文件的版本号，然后访问3344和3355。（启动完成后再修改）
   `http://config-3344.com:3344/master/config-dev.yml`
   ![在这里插入图片描述](img/20200618102108715.png)
   `http://localhost:3355/configInfo`（没读取到，需要发送post请求刷新3355才能生效）
   ![在这里插入图片描述](img/20200618102402323.png)

6. 打开终端，输入`curl -X POST "http://localhost:3355/actuator/refresh"`
   ![在这里插入图片描述](img/2020061810264983.png)

7. 刷新`http://localhost:3355/configInfo`
   ![在这里插入图片描述](img/20200618102552561.png)

![在这里插入图片描述](img/20200618102854339.png)

# 十四、SpringCloud Bus消息总线

![在这里插入图片描述](img/2020061810315883.png)
Bus支持两种消息代理：RabbitMQ和Kafka。
![在这里插入图片描述](img/20200618110225555.png)
![在这里插入图片描述](img/20200618110427733.png)
RabbitMQ粗浅的理论和使用的[学习笔记](https://blog.csdn.net/qq_36903261/article/details/106517697)，有兴趣可以看一下。

## Docker安装RabbitMQ

[Docker基础入门学习笔记](https://blog.csdn.net/qq_36903261/article/details/105870268)，有兴趣的可以看一下。

在linux的docker里拉取RabbitMQ镜像`docker pull rabbitmq:3.8.3-management`（management是带web的管理界面）。
5672是客户端和RabbitMQ进行通信的端口。
15672是管理界面访问web页面的端口。

运行RabbitMQ

```shell
docker run -d -p 5672:5672 -p 15672:15672 --name myRabbitMQ 容器id
1
```

在浏览器中输入`http://10.211.55.17:15672/`访问RabbitMQ的管理页面，用户名和密码默认guest。（10.211.55.17是我linux的IP地址）
![在这里插入图片描述](img/20200618113312751.png)
![在这里插入图片描述](img/20200618113325980.png)

## SpringCloud Bus动态刷新全局广播

![在这里插入图片描述](img/20200618113555870.png)

按照3355新建3366。
![在这里插入图片描述](img/20200618114508312.png)
利用消息总线触发一个客户端/bus/refresh而刷新所有客户端的配置：
![在这里插入图片描述](img/20200618114659491.png)
![在这里插入图片描述](img/20200618114727461.png)
利用消息总线触发一个服务端 ConfigServer的/bus/refresh端点,而刷新所有客户端的配置：
![在这里插入图片描述](img/20200618114750436.png)
![在这里插入图片描述](img/20200618114800191.png)
图二的架构显然更加适合，图一不适合的原因如下：
![在这里插入图片描述](img/20200618115036173.png)

### 给服务端3344添加消息总线支持

pom添加：

```xml
  <!--添加消息总线RabbitMQ的支持-->
  <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-bus-amqp</artifactId>
 </dependency>
12345
```

yml添加：

```yml
  rabbitmq:
    host: 10.211.55.17  #本机写localhost，服务器的写服务器地址
    port: 5672   #客户端和RabbitMQ进行通信的端口
    username: guest #默认也是guest
    password: guest #默认也是guest


#RabbitMQ相关配置
management:
  endpoints:  #暴露bus刷新配置的端点
    web:
      exposure:
        include: 'bus-refresh'
12345678910111213
```

![在这里插入图片描述](img/20200618143443501.png)

### 给客户端3355和3366添加消息总线支持

pom添加：

```xml
  <!--添加消息总线RabbitMQ的支持-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-bus-amqp</artifactId>
  </dependency>
12345
```

yml添加：

```yml
  rabbitmq:
    host: 10.211.55.17  #本机写localhost，服务器的写服务器地址
    port: 5672   #客户端和RabbitMQ进行通信的端口
    username: guest #默认也是guest
    password: guest #默认也是guest
12345
```

spring的下一级，不要写错了。
![在这里插入图片描述](img/20200618120525379.png)

### 测试

1. 启动7001，3344，3355，3366。
2. 修改GitHub上文件的版本号。
3. 在终端输入：`curl -X POST "http://localhost:3344/actuator/bus-refresh"`
4. 在浏览器输入`http://localhost:3355/configInfo`，`http://localhost:3366/configInfo`
   ![在这里插入图片描述](img/20200618143639248.png)
   ![在这里插入图片描述](img/20200618143708765.png)

项目先不停止，下面要用。

## SpringCloud Bus动态刷新定点通知

![在这里插入图片描述](img/20200618144938886.png)
![在这里插入图片描述](img/20200618144429975.png)

1. 修改GitHub的文件版本号。
2. 在终端输入：`curl -X POST "http://localhost:3344/actuator/bus-refresh/config-client:3355"`
   *多加了服务名:端口号即可定点通知。*

`http://localhost:3355/configInfo`
![在这里插入图片描述](img/2020061814534146.png)
`http://localhost:3366/configInfo`
![在这里插入图片描述](img/20200618145348944.png)

总结：
![在这里插入图片描述](img/20200618145606746.png)

# 十五、SpringCloud Stream消息驱动

## 消费驱动概述

#### 是什么

![在这里插入图片描述](img/20200618151201404.png)
![在这里插入图片描述](img/20200618151622851.png)
![在这里插入图片描述](img/20200618151918362.png)
官网：https://spring.io/projects/spring-cloud-stream#overview
![在这里插入图片描述](img/20200618151932271.png)
![在这里插入图片描述](img/20200618151946159.png)
API：https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.1.RELEASE/reference/html/

中文指导手册：https://m.wang1314.com/doc/webapp/topic/20971999.html

#### 设计思想

![在这里插入图片描述](img/20200618183206679.png)
标准MQ：
![在这里插入图片描述](img/20200618183223934.png)
![在这里插入图片描述](img/20200618183356561.png)
![在这里插入图片描述](img/20200618183412345.png)

为什么用Cloud Stream？
![在这里插入图片描述](img/20200618183502490.png)
![在这里插入图片描述](img/20200618183618608.png)
![在这里插入图片描述](img/20200618183757808.png)
![在这里插入图片描述](img/20200618183715500.png)
![在这里插入图片描述](img/20200618183835919.png)
![在这里插入图片描述](img/20200618183858411.png)
![在这里插入图片描述](img/20200618184037800.png)
![在这里插入图片描述](img/20200618184152655.png)
![在这里插入图片描述](img/20200618184228725.png)

#### Spring Cloud Stream标准流程套路

![在这里插入图片描述](img/20200618184607923.png)
![在这里插入图片描述](img/2020061818462481.png)
![在这里插入图片描述](img/20200618184759166.png)
![在这里插入图片描述](img/20200618184807765.png)
![在这里插入图片描述](img/20200618184820843.png)

#### 编码API和常用注解

![在这里插入图片描述](img/20200618184843993.png)

## 案例说明

![在这里插入图片描述](img/20200618190735210.png)

## 消息驱动之生产者

1. 新建模块cloud-stream-rabbitmq-provider8801

2. pom

   ```xml
   <dependencies>
       <!--stream rabbit -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
       </dependency>
       <!--eureka client-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738
   ```

3. yml

   ```yml
   server:
     port: 8801
   
   spring:
     application:
       name: cloud-stream-provider
     cloud:
       stream:
         binders: #在此处配置要绑定的rabbitmq的服务信息
           defaultRabbit: #表示定义的名称，用于binding整合
             type: rabbit #消息组件类型
             environment: #设置rabbitmq的相关环境配置
               spring:
                 rabbitmq:
                   host: 10.211.55.17  #RabbitMQ在本机的用localhost，在服务器的用服务器的ip地址
                   port: 5672
                   username: guest
                   password: guest
         bindings: #服务的整合处理
           output: #这个名字是一个通道的名称
             destination: studyExchange #表示要使用的Exchange名称定义
             content-type: application/json #设置消息类型，本次为json，本文要设置为“text/plain”
             binder: defaultRabbit #设置要绑定的消息服务的具体设置（爆红不影响使用，位置没错）
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
     instance:
       lease-renewal-interval-in-seconds: 2 #设置心跳的时间间隔（默认是30S)
       lease-expiration-duration-in-seconds: 5 #如果超过5S间隔就注销节点 默认是90s
       instance-id: send-8801.com #在信息列表时显示主机名称
       prefer-ip-address: true #访问的路径变为IP地址
   123456789101112131415161718192021222324252627282930313233
   ```

4. 主启动类

   ```java
   @SpringBootApplication
   public class StreamMQMain8801 {
   
       public static void main(String[] args) {
           SpringApplication.run(StreamMQMain8801.class, args);
       }
   
   }
   12345678
   ```

5. 业务类
   ![在这里插入图片描述](img/20200618195237466.png)

   1. 新建service.IMessageProvider接口

      ```java
      public interface IMessageProvider {
          public String send();
      }
      123
      ```

   2. 在service下新建impl.IMessageProviderImpl实现类

      ```java
      import com.angenin.springcloud.service.IMessageProvider;
      import org.springframework.cloud.stream.annotation.EnableBinding;
      import org.springframework.cloud.stream.messaging.Source;
      import org.springframework.integration.support.MessageBuilder;
      import org.springframework.messaging.MessageChannel;
      import javax.annotation.Resource;
      import java.util.UUID;
      
      @EnableBinding(Source.class)    //定义消息的推送管道（Source是spring的）
      public class IMessageProviderImpl implements IMessageProvider {
      
          @Resource
          private MessageChannel output;  //消息发送管道
      
          @Override
          public String send() {
              String serial = UUID.randomUUID().toString();
              output.send(MessageBuilder.withPayload(serial).build());     //MessageBuilder是spring的integration.support.MessageBuilder
              System.out.println("*******serial: " + serial);
              return null;
          }
      }
      12345678910111213141516171819202122
      ```

   3. 新建controller.SendMessageController

      ```java
      @RestController
      public class SendMessageController {
      
          @Resource
          private IMessageProvider iMessageProvider;
      
          @GetMapping("/sendMessage")
          public String sendMessage(){
              return iMessageProvider.send();
          }
      
      }
      123456789101112
      ```

6. 测试
   启动7001，RabbitMQ，启动8801

   然后在RabbitMQ后台可以看到新生成的交换机（在yml定义的）
   ![在这里插入图片描述](img/20200618202702999.png)
   然后在浏览器输入：`http://localhost:8801/sendMessage`，多次刷新，后台打印的数据：
   ![在这里插入图片描述](img/20200618202838352.png)
   RabbitMQ后台：
   ![在这里插入图片描述](img/20200618202830670.png)

## 消息驱动之消费者

1. 新建模块cloud-stream-rabbitmq-consumer8802

2. pom

   ```xml
   <dependencies>
       <!--stream rabbit -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
       </dependency>
       <!--eureka client-->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738
   ```

3. yml

   ```yml
   server:
     port: 8802
   
   spring:
     application:
       name: cloud-stream-provider
     cloud:
       stream:
         binders: #在此处配置要绑定的rabbitmq的服务信息
           defaultRabbit: #表示定义的名称，用于binding整合
             type: rabbit #消息组件类型
             environment: #设置rabbitmq的相关环境配置
               spring:
                 rabbitmq:
                   host: 10.211.55.17  #RabbitMQ在本机的用localhost，在服务器的用服务器的ip地址
                   port: 5672
                   username: guest
                   password: guest
         bindings: #服务的整合处理
           input: #这个名字是一个通道的名称
             destination: studyExchange #表示要使用的Exchange名称定义
             content-type: application/json #设置消息类型，本次为json，本文要设置为“text/plain”
             binder: defaultRabbit #设置要绑定的消息服务的具体设置（爆红不影响使用，位置没错）
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
     instance:
       lease-renewal-interval-in-seconds: 2 #设置心跳的时间间隔（默认是30S)
       lease-expiration-duration-in-seconds: 5 #如果超过5S间隔就注销节点 默认是90s
       instance-id: receive-8802.com #在信息列表时显示主机名称
       prefer-ip-address: true #访问的路径变为IP地址
   123456789101112131415161718192021222324252627282930313233
   ```

4. 主启动类

   ```java
   @SpringBootApplication
   public class StreamMQMain8802 {
   
       public static void main(String[] args) {
           SpringApplication.run(StreamMQMain8802.class, args);
       }
   
   }
   12345678
   ```

5. 新建controller.ReceiveMessageListenerController

   ```java
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.annotation.StreamListener;
   import org.springframework.cloud.stream.messaging.Sink;
   import org.springframework.messaging.Message;
   import org.springframework.stereotype.Controller;
   
   
   @EnableBinding(Sink.class)
   @Controller
   public class ReceiveMessageListenerController {
   
       @Value("${server.port}")
       private String serverPort;
   
       @StreamListener(Sink.INPUT) //监听
       public void input(Message<String> message){
           System.out.println("消费者1号------>收到的消息：" + message.getPayload() + "\t port：" + serverPort);
       }
   
   }
   123456789101112131415161718192021
   ```

6. 测试
   启动7001，8801，8802

`http://localhost:8801/sendMessage`（8801发送消息）
![在这里插入图片描述](img/20200618205215922.png)
8802接收到消息：
![在这里插入图片描述](img/20200618205229873.png)

## 分组消费与持久化

![在这里插入图片描述](img/20200618205955304.png)
按照8802，新建8803。

### 消费

![在这里插入图片描述](img/2020061909151354.png)
![在这里插入图片描述](img/20200619091611259.png)
8801发送后，8802和8803都能接收到数据（重复消费）；正常情况下应该是有一个消费者消费了8801的消息后，另外的其他消费者就不能消费。这里要把8802和8803看成一个集群，如果8802和8803都接收到了，就都会去做业务，然后本来8801只想让这个集群来做一次消费的，就会变成每个消费者都来消费一次。这是因为8802和8803不是在同一个组（队列）里，不同组可以重复消费，而同一个组里，只有一个消费者能消费，所以需要对消费者进行分组，把所有相同的消费者分到一个组里。（主题会给每个队列发送消息，而每个队列只有一个消费者可以获得消息（同组广播，不同组轮询））

![在这里插入图片描述](img/20200619114943551.png)![在这里插入图片描述](img/20200619114830261.png)
![在这里插入图片描述](img/20200619114837454.png)
![在这里插入图片描述](img/20200619114845440.png)

![在这里插入图片描述](img/20200619113350669.png)

### 分组(队列)

![在这里插入图片描述](img/20200619115233467.png)

##### 设置不同分组

![在这里插入图片描述](img/20200619153622654.png)
修改8802的yml
![在这里插入图片描述](img/20200619153822282.png)
修改8803的yml
![在这里插入图片描述](img/2020061915513060.png)

![在这里插入图片描述](img/20200619155053698.png)
![在这里插入图片描述](img/20200619155151326.png)
![在这里插入图片描述](img/2020061915520268.png)
![在这里插入图片描述](img/20200619155209120.png)
结论：
![在这里插入图片描述](img/20200619154017678.png)

##### 设置相同分组

![在这里插入图片描述](img/2020061915494568.png)
修改8803的yml中group为angeninA，然后重启8003。
![在这里插入图片描述](img/20200619155738479.png)
![在这里插入图片描述](img/20200619155804508.png)
![在这里插入图片描述](img/20200619155827392.png)
![在这里插入图片描述](img/20200619155845387.png)
![在这里插入图片描述](img/20200619155853442.png)
![在这里插入图片描述](img/20200619155623141.png)

### 持久化

![在这里插入图片描述](img/20200619160337713.png)

1. 停掉8802和8803，去掉8802的`group: angeninA`。
2. 然后8801发送4条消息。
   ![在这里插入图片描述](img/20200619160636680.png)
3. 启动8802，8802并没有去拿取消息。（因为8802去掉了`group: angeninA`，所以启动后会再新建一个队列）
   ![在这里插入图片描述](img/20200619160926193.png)
4. 启动8803，启动后获取到8801的消息。（因为8803没删除`group: angeninA`，angeninA队列是在8801发送消息前存在的，所以当8803停机后再启动，就可以获取到停机时8801发送的信息（如果此时同组（队列）里有别的消费者，那么消息会被别的消费者消费掉））
   ![在这里插入图片描述](img/20200619161103584.png)

# 十六、SpringCloud Sleuth分布式请求链路追踪

![在这里插入图片描述](img/2020061916152067.png)
![在这里插入图片描述](img/20200619162105957.png)
https://github.com/spring-cloud/spring-cloud-sleuth

https://cloud.spring.io/spring-cloud-sleuth/reference/html/

![在这里插入图片描述](img/20200619162515998.png)

## 搭建链路监控步骤

![在这里插入图片描述](img/20200619162559968.png)

![在这里插入图片描述](img/20200619163203767.png)
下载jar包：http://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/
![在这里插入图片描述](img/2020061916340733.png)
![在这里插入图片描述](img/20200619163441550.png)

下载完后，终端jar包的目录里，然后输入：`java -jar zipkin-server-2.12.9-exec.jar`运行。
![在这里插入图片描述](img/20200619163826777.png)

浏览器输入：`http://localhost:9411/zipkin/`
![在这里插入图片描述](img/20200619164012522.png)

### 原理

![在这里插入图片描述](img/20200619164047621.png)
![在这里插入图片描述](img/20200619164248683.png)
![在这里插入图片描述](img/20200619164359860.png)

### 服务提供者cloud-provider-payment8001

1. 在pom中添加：

   ```xml
      <!--包含了sleuth+zipkin-->
      <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-zipkin</artifactId>
      </dependency>
   12345
   ```

2. 在yml中添加：

   ```yml
     zipkin:
       base-url: http://localhost:9411
     sleuth:
       sampler:
         probability: 1  #采样率值介于0到1之间，1则表示全部采集（一般不为1，不然高并发性能会有影响）
   12345
   ```

![在这里插入图片描述](img/20200619165609430.png)

1. 在PaymentController中添加：

   ```java
      @GetMapping("/payment/zipkin")
      public String paymentZipkin(){
          return "paymentZipkin...";
      }
   1234
   ```

### 服务消费者cloud-consumer-order80

1. 在pom中添加（和提供者一样）

2. 在yml中添加（和提供者一样）

3. 在OrderController中添加：

   ```java
       @GetMapping("/consumer/payment/zipkin")
       public String paymentZipkin(){
           String result = restTemplate.getForObject("http://localhost:8001" + "/payment/zipkin", String.class);
           return result;
       }
   12345
   ```

### 测试

启动7001，8001，80。

浏览器输入：`http://localhost/consumer/payment/zipkin`
![在这里插入图片描述](img/20200619170648369.png)

![在这里插入图片描述](img/20200619170954185.png)
![在这里插入图片描述](img/2020061917120464.png)
![在这里插入图片描述](img/20200619171220752.png)

# 十七、SpringCloud Alibaba入门简介

### Netflix进入维护模式

![在这里插入图片描述](img/20200619183546893.png)
![在这里插入图片描述](img/20200619183856506.png)
![在这里插入图片描述](img/20200619184015679.png)
![在这里插入图片描述](img/20200619183918150.png)
![在这里插入图片描述](img/20200619184039748.png)
![在这里插入图片描述](img/20200619184105791.png)
![在这里插入图片描述](img/20200619184811873.png)

## Spring Alibaba简介

Spring官网：https://spring.io/projects/spring-cloud-alibaba
GitHub：https://github.com/alibaba/spring-cloud-alibaba
GitHub中文文档：https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md
Spring Cloud Alibaba参考文档：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html
![在这里插入图片描述](img/20200619184855692.png)
![在这里插入图片描述](img/2020061918521220.png)
![在这里插入图片描述](img/20200619185242508.png)
SpringCloud Alibaba的依赖：（已经在父工程的pom中引入了）

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
1234567891011
```

![在这里插入图片描述](img/2020061918572158.png)

# 十八、SpringCloud Alibaba Nacos服务注册和配置中心

![在这里插入图片描述](img/20200619191018127.png)
![在这里插入图片描述](img/20200619191107585.png)
![在这里插入图片描述](img/20200619191201816.png)
![在这里插入图片描述](img/20200619191338649.png)
官网：[https://nacos.io/zh-cn/](https://nacos.io/zh-cn/)
GitHub：https://github.com/alibaba/Nacos
![在这里插入图片描述](img/20200619192255259.png)
![在这里插入图片描述](img/20200619192441543.png)
![在这里插入图片描述](img/20200619192453900.png)

## 安装并运行nacos

在docker上安装nacos

拉取nacos镜像：

```shell
docker pull nacos/nacos-server
1
```

运行nacos：

```shell
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server
1
```

在浏览器输入：`http://10.211.55.17:8848/nacos/`（10.211.55.17是我linux的IP地址）
账号和密码都是`nacos`。
![在这里插入图片描述](img/20200619195051798.png)
![在这里插入图片描述](img/20200619195131136.png)

## Nacos作为服务注册中心演示

官方文档：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html

### 基于Nacos的服务提供者

![在这里插入图片描述](img/20200619200435806.png)

1. 新建模块cloudalibaba-provider-payment9001

2. pom

   ```xml
   <dependencies>
       <!--SpringCloud Alibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   12345678910111213141516171819202122232425262728293031323334
   ```

3. yml

   ```yml
   server:
     port: 9001
   
   
   spring:
     application:
       name: nacos-payment-provider
     cloud:
       nacos:
         discovery:
           server-addr: 10.211.55.17:8848  #配置的Nacos地址（本机的写localhost:8848，服务器的写IP地址）
   
   
   management:
     endpoints:
       web:
         exposure:
           include: '*'
   123456789101112131415161718
   ```

4. 主启动类

   ```java
   @EnableDiscoveryClient
   @SpringBootApplication
   public class PaymentMain9001 {
   
       public static void main(String[] args) {
           SpringApplication.run(PaymentMain9001.class, args);
       }
   
   }
   123456789
   ```

5. 新建controller.PaymentController

   ```java
   @RestController
   public class PaymentController {
   
       @Value("${server.port}")
       private String serverPort;
   
   
       @GetMapping("/payment/nacos/{id}")
       public String getPayment(@PathVariable("id") Integer id){
           return "nacos registry, serverPort: " + serverPort + "\t id: " + id;
       }
   
   }
   12345678910111213
   ```

6. 测试
   启动9001
   ![在这里插入图片描述](img/20200619202637850.png)

7. 参照9001新建9002，建立提供者集群。
   ![在这里插入图片描述](img/20200619203625265.png)

### 基于Nacos的服务消费者

![在这里插入图片描述](img/20200619203739241.png)

1. 新建模块cloudalibaba-consumer-nacos-order83

2. pom（nacos集成了ribbon，实现负载均衡）

   ```xml
   <dependencies>
       <!--SpringCloud Alibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   123456789101112131415161718192021222324252627282930313233343536373839
   ```

3. yml

   ```yml
   server:
     port: 83
   
   
   spring:
     application:
       name: nacos-order-consumer
     cloud:
       nacos:
         discovery:
           server-addr: 10.211.55.17:8848  #配置的Nacos地址（本机的写localhost:8848，服务器的写IP地址）
   
   
   #消费者要访问的微服务名称（成功注册进nacos的服务提供者）
   service-url:
     nacos-user-service: http://nacos-payment-provider
   12345678910111213141516
   ```

4. 主启动类

   ```java
   @EnableDiscoveryClient
   @SpringBootApplication
   public class OrderNacosMain83 {
   
       public static void main(String[] args) {
           SpringApplication.run(OrderNacosMain83.class, args);
       }
   
   }
   123456789
   ```

5. 新建config.ApplicationContextConfig

   ```java
   @Configuration
   public class ApplicationContextConfig {
   
       @Bean
       public RestTemplate getRestTemplate(){
           return new RestTemplate();
       }
   
   }
   123456789
   ```

6. 新建controller.OrderNacosController

   ```java
   1
   ```

7. 测试
   启动9001，9002，83
   在浏览器输入：`http://localhost:83/consumer/payment/nacos/1`
   ![在这里插入图片描述](img/20200619212818370.png)
   ![在这里插入图片描述](img/20200619212739218.png)

### 整合Feign

#### 在消费者83

1. 在pom中导入

   ```xml
   <!-- openfeign -->
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   12345
   ```

2. 在主启动类上加上`@EnableFeignClients`，激活feign。

3. 注释掉config配置类的`@Configuration`注解，不使用RestTemplate。

4. 新建service.PaymentFeignService接口

   ```java
   @Component
   @FeignClient(value = "nacos-payment-provider")
   public interface PaymentFeignService {
   
       @GetMapping("/payment/nacos/{id}")
       public String getPayment(@PathVariable("id") Integer id);
   
   }
   12345678
   ```

5. 注释掉OrderNacosController中的restTemplate对象和paymentInfo方法。
   ![在这里插入图片描述](img/202006192145095.png)

6. 在OrderNacosController中添加

   ```java
   @Resource
   private PaymentFeignService paymentFeignService;
   
       @GetMapping("/consumer/payment/feign/nacos/{id}")
       public String paymentInfo2(@PathVariable("id") Long id){
           return restTemplate.getForObject(serverURL + "/payment/feign/nacos/" + id, String.class);
       }
   1234567
   ```

7. 测试
   重启83。
   浏览器输入：`http://localhost:83/consumer/payment/feign/nacos/1`
   ![在这里插入图片描述](img/20200619214945387.png)
   ![在这里插入图片描述](img/20200619215001128.png)

### 服务注册中心对比

#### Nacos全景图

![在这里插入图片描述](img/20200619192453900.png)

#### Nacos和CAP

![在这里插入图片描述](img/20200619215524730.png)
![在这里插入图片描述](img/20200619215724977.png)

#### AP和CP的切换

A：可用性
C：一致性
P：分区容错性
![在这里插入图片描述](img/20200619215757886.png)
*Nacos默认AP。*

切换CP：
![在这里插入图片描述](img/20200619220306505.png)

## Nacos作为服务配置中心演示

### Nacos作为配置中心——基础配置

![在这里插入图片描述](img/20200619222313761.png)

1. 新建模块cloudalibaba-config-nacos-client3377

2. pom

   ```xml
   <dependencies>
       <!-- nacos config-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
       </dependency>
       <!-- openfeign -->
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
       <!--SpringCloud Alibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   12345678910111213141516171819202122232425262728293031323334353637383940414243
   ```

3. xml
   ![在这里插入图片描述](img/20200619221227736.png)
   bootstrap.yml：

   ```yml
   server:
     port: 3377
   
   spring:
     application:
       name: nacos-config-client
     cloud:
       nacos:
         discovery:
           server-addr: 10.211.55.17:8848 #Nacos服务注册中心地址（本机的写localhost）
         config:
           server-addr: 10.211.55.17:8848 #Nacos作为配置中心地址（本机的写localhost）
           file-extension: yml #指定yml格式配置
   12345678910111213
   ```

   application.yml

   ```yml
   spring:
     profiles:
       active: dev #表示开发环境
   123
   ```

4. 主启动类

   ```java
   @EnableDiscoveryClient
   @SpringBootApplication
   public class NacosConfigClientMain3377 {
   
       public static void main(String[] args) {
           SpringApplication.run(NacosConfigClientMain3377.class, args);
       }
   
   }
   123456789
   ```

5. 新建controller.ConfigClientController

   ```java
   @RefreshScope   //支持Nacos的动态刷新功能
   @RestController
   public class ConfigClientController {
   
       @Value("${config.info}")
       private String configInfo;
   
   
       @GetMapping("/config/info")
       public String getConfigInfo(){
           return configInfo;
       }
   
   }
   1234567891011121314
   ```

6. 在Nacos中添加配置信息
   ![在这里插入图片描述](img/20200619222412384.png)
   https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html
   ![在这里插入图片描述](img/20200619223232875.png)
   ![在这里插入图片描述](img/20200619223724712.png)

   ```yml
   #${prefix}-${spring.profile.active}.${file-extension}
   # ${spring.application.name}-${spring.profile.active}.${file-extension}
   # nacos-config-client-dev.yml
   123
   ```

   ![在这里插入图片描述](img/20200619224655660.png)
   ![在这里插入图片描述](img/20200619224735257.png)
   ![在这里插入图片描述](img/20200619224312193.png)
   ![在这里插入图片描述](img/20200619224607767.png)
   ![在这里插入图片描述](img/20200619224835299.png)

7. 测试
   启动3377
   浏览器输入：`http://localhost:3377/config/info`
   ![在这里插入图片描述](img/20200619225337409.png)

8. 在nacos后台修改yml文件的版本号
   刷新页面，动态刷新
   ![在这里插入图片描述](img/20200619225656220.png)

### Nacos作为配置中心——分类配置

![在这里插入图片描述](img/20200619225942257.png)
![在这里插入图片描述](img/20200619225956666.png)
![在这里插入图片描述](img/20200619230054127.png)
![在这里插入图片描述](img/2020061923012572.png)

#### Namespace+Group+DataID三者的关系

![在这里插入图片描述](img/20200619230206531.png)
![在这里插入图片描述](img/20200619230638407.png)
![在这里插入图片描述](img/20200619230649569.png)

#### 三种方案加载配置

![在这里插入图片描述](img/2020061923174947.png)

##### DataID方案

![在这里插入图片描述](img/20200622104400699.png)

1. 新建DataId（test）
   ![在这里插入图片描述](img/20200622103543462.png)

   ```yml
   config: 
       info: test nacos config center, version = 2
   12
   ```

   ![在这里插入图片描述](img/20200622103622678.png)

2. 修改3377的application.yml的active为`test`。

3. 启动3377。
   `http://localhost:3377/config/info`
   ![在这里插入图片描述](img/2020062210420367.png)

##### Group方案

![在这里插入图片描述](img/20200622104422648.png)
*Group默认DEFAULT_GROUP。*

1. 新建配置nacos-config-client-info.yml（DEV_GROUP）
   ![在这里插入图片描述](img/20200622104615122.png)

   ```yml
   config:
   	info: nacos-config-client-info.yml DEV_GROUP
   12
   ```

   新建配置时nacos-config-client-info.yml（TEST_GROUP）
   ![在这里插入图片描述](img/20200622104923618.png)
   ![在这里插入图片描述](img/20200622105049418.png)

2. bootstrap.yml的config下新增`group: TEST_GROUP`
   ![在这里插入图片描述](img/20200622105238999.png)

3. 修改application.yml的active为`active: info`

4. 重启3377。
   `http://localhost:3377/config/info`
   ![在这里插入图片描述](img/20200622105408713.png)

5. 修改bootstrap.yml的group为`DEV_GROUP`

6. 重启3377。
   ![在这里插入图片描述](img/20200622105542992.png)

##### Namespace方案

![在这里插入图片描述](img/20200622105837810.png)
*Namespace默认有一个public。（不可删除）*
![在这里插入图片描述](img/20200622105645489.png)
![在这里插入图片描述](img/2020062210565733.png)

1. 新建dev和test的Namespace
   ![在这里插入图片描述](img/20200622110017996.png)
   ![在这里插入图片描述](img/20200622110053637.png)
   ![在这里插入图片描述](img/20200622110153682.png)
2. 给dev命名空间新增四个DataId，分三个Group。
   ![在这里插入图片描述](img/20200622111808599.png)
   ![在这里插入图片描述](img/20200622111927929.png)
3. 在bootstrap.yml的config里添加`namespace: xx`
   ![在这里插入图片描述](img/20200622111420821.png)
4. 重启3377。
   `http://localhost:3377/config/info`
   ![在这里插入图片描述](img/20200622112117324.png)

## Nacos集群和持久化配置（重要）

![在这里插入图片描述](img/2020062211245813.png)
https://nacos.io/zh-cn/docs/deployment.html

### 官网说明

官网说明：https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html
![在这里插入图片描述](img/20200622115341580.png)
![在这里插入图片描述](img/20200622114041637.png)
![在这里插入图片描述](img/20200622114031733.png)
![在这里插入图片描述](img/20200622114735512.png)
![在这里插入图片描述](img/20200622115206239.png)
![在这里插入图片描述](img/2020062211513644.png)

### Nacos持久化配置解释

![在这里插入图片描述](img/20200622120003643.png)
![在这里插入图片描述](img/20200622120026923.png)

nacos单机的持久化看这篇文章：[在Docker上用Nacos1.3容器连接MySQL5.6和8.0.18容器进入持久化的具体操作](https://blog.csdn.net/qq_36903261/article/details/106919191)
（步骤有点多，而且坑了我快一天的时间了，所以单独写在另一篇文章里，使用docker虽然启动时方便了，但是因为里面的配置已经被人改了，和官方文档不一样，想进行配置就变得不太方便。（个人吐槽））

### Linux版Nacos+MySQL生产环境配置

![在这里插入图片描述](img/20200623181749678.png)

#### 集群配置步骤

同样，因为是在docker上配置的，所以和阳哥的方式不一样，写在另一篇文章里了。
[在Docker上用3个Nacos1.3容器+一个MySQL5和8容器+一个Nginx容器进行集群的具体操作（Nacos集群版）](https://blog.csdn.net/qq_36903261/article/details/106835279)

阳哥的笔记还是会截的：
![在这里插入图片描述](img/20200623212316986.png)
![在这里插入图片描述](img/20200623213309815.png)
![在这里插入图片描述](img/20200623213643466.png)
![在这里插入图片描述](img/20200623213629833.png)

```shell
hostname -i
1
```

![在这里插入图片描述](img/20200623213725617.png)

第4
![在这里插入图片描述](img/20200623213904552.png)
这里和docker启动容器时的-p是类似的。
![在这里插入图片描述](img/20200624162526145.png)
![在这里插入图片描述](img/20200624163117313.png)

第5
![在这里插入图片描述](img/20200624163442568.png)
![在这里插入图片描述](img/20200624163511731.png)
![在这里插入图片描述](img/20200624164359936.png)
![在这里插入图片描述](img/20200624164302408.png)
![在这里插入图片描述](img/20200624164542216.png)
![在这里插入图片描述](img/20200624164945107.png)

##### 测试

![在这里插入图片描述](img/20200624165013348.png)
修改9002的yml文件

```yml
#        server-addr: 10.211.55.17:8848  #配置的Nacos地址（本机的写localhost:8848，服务器的写IP地址）
		#改为下面这个，填自己linux的IP地址
        server-addr: 10.211.55.17:1111  #nginx的地址
123
```

启动9002

出现`java.net.SocketException: Connection reset`异常。

```
2020-06-24 20:13:04.549  INFO 47941 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 9002 (http) with context path ''
2020-06-24 20:13:04.740 ERROR 47941 --- [  restartedMain] c.a.c.n.registry.NacosServiceRegistry    : nacos registry, nacos-payment-provider register failed...NacosRegistration{nacosDiscoveryProperties=NacosDiscoveryProperties{serverAddr='10.211.55.17:1111', endpoint='', namespace='', watchDelay=30000, logName='', service='nacos-payment-provider', weight=1.0, clusterName='DEFAULT', namingLoadCacheAtStart='false', metadata={preserved.register.source=SPRING_CLOUD}, registerEnabled=true, ip='192.168.0.109', networkInterface='', port=9002, secure=false, accessKey='', secretKey=''}},

java.lang.IllegalStateException: failed to req API:/nacos/v1/ns/instance after all servers([10.211.55.17:1111]) tried: failed to req API:10.211.55.17:1111/nacos/v1/ns/instance. code:500 msg: java.net.SocketException: Connection reset
	at com.alibaba.nacos.client.naming.net.NamingProxy.reqAPI(NamingProxy.java:464) ~[nacos-client-1.1.1.jar:na]
	at com.alibaba.nacos.client.naming.net.NamingProxy.reqAPI(NamingProxy.java:386) ~[nacos-client-1.1.1.jar:na]
	at com.alibaba.nacos.client.naming.net.NamingProxy.registerService(NamingProxy.java:188) ~[nacos-client-1.1.1.jar:na]
...
12345678
```

重新启动多次后出现`java.net.SocketTimeoutException: Read timed out`异常。

```
2020-06-24 20:32:01.365 ERROR 48776 --- [  restartedMain] c.a.c.n.registry.NacosServiceRegistry    : nacos registry, nacos-payment-provider register failed...NacosRegistration{nacosDiscoveryProperties=NacosDiscoveryProperties{serverAddr='10.211.55.17:1111', endpoint='', namespace='', watchDelay=30000, logName='', service='nacos-payment-provider', weight=1.0, clusterName='DEFAULT', namingLoadCacheAtStart='false', metadata={preserved.register.source=SPRING_CLOUD}, registerEnabled=true, ip='192.168.0.109', networkInterface='', port=9002, secure=false, accessKey='', secretKey=''}},

com.alibaba.nacos.api.exception.NacosException: failed to req API:/api//nacos/v1/ns/instance after all servers([10.211.55.17:1111]) tried: java.net.SocketTimeoutException: Read timed out
	at com.alibaba.nacos.client.naming.net.NamingProxy.reqAPI(NamingProxy.java:490) ~[nacos-client-1.2.0.jar:na]
	at com.alibaba.nacos.client.naming.net.NamingProxy.reqAPI(NamingProxy.java:395) ~[nacos-client-1.2.0.jar:na]
12345
```

在网上大部分问题都不一样，类似问题的解决办法尝试后还是解决不了，弄nacos真的把心态都搞崩了，希望也遇到这个问题并且已经解决了的兄弟，告知一下解决方法，谢谢。

##### 总结

![在这里插入图片描述](img/20200624181328483.png)

# 十九、SpringCloud Alibaba Sentinel实现熔断与限流

## 简介

![在这里插入图片描述](img/20200628122920708.png)
![在这里插入图片描述](img/20200627192219176.png)
官网：https://github.com/alibaba/sentinel
中文版：[https://github.com/alibaba/Sentinel/wiki/%E4%BB%8B%E7%BB%8D](https://github.com/alibaba/Sentinel/wiki/介绍)
![在这里插入图片描述](img/20200627192917693.png)
![在这里插入图片描述](img/20200627192938903.png)
![在这里插入图片描述](img/20200627193019324.png)
![在这里插入图片描述](img/20200627193029486.png)
文档：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_sentinel
![在这里插入图片描述](img/20200627194141340.png)

### 下载

下载：https://github.com/alibaba/Sentinel/releases
*本来想着只有一个jar包，就不使用docker了，直接下载到本地运行，但是是真的下不动，点下载转了半天没反应，真的是服了。*

#### docker

```shell
#拉取sentinel镜像
docker pull bladex/sentinel-dashboard

#运行sentinel（docker里的sentinel是8858端口）
docker run --name sentinel -d -p 8858:8858 bladex/sentinel-dashboard

#把nacos和mysql也启动起来
1234567
```

在浏览器输入：`http://10.211.55.26:8858/#/login`（因为linux出了点问题，重装了，所以ip也变了）
![在这里插入图片描述](img/2020062720135922.png)
账号和密码都是`sentinel`
![在这里插入图片描述](img/20200627201508130.png)

控制台使用说明：[https://github.com/alibaba/Sentinel/wiki/%E6%8E%A7%E5%88%B6%E5%8F%B0](https://github.com/alibaba/Sentinel/wiki/控制台)

## 初始化演示工程

![在这里插入图片描述](img/2020062719453513.png)

1. 新建模块cloudalibaba-sentinel-service8401

2. pom

   ```xml
   <dependencies>
       <!-- SpringCloud ailibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- SpringCloud ailibaba sentinel-datasource-nacos 持久化需要用到-->
       <dependency>
           <groupId>com.alibaba.csp</groupId>
           <artifactId>sentinel-datasource-nacos</artifactId>
       </dependency>
       <!-- SpringCloud ailibaba sentinel-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738394041424344454647
   ```

3. yml

   ```yml
   server:
     port: 8401
   
   spring:
     application:
       name: cloudalibaba-sentinal-service
     cloud:
       nacos:
         discovery:
           #Nacos服务注册中心地址（改成自己的服务器ip地址，本地用localhost‍）
           server-addr: 10.211.55.26:8848
       sentinel:
         transport:
           #配置Sentin dashboard地址（改成自己的服务器ip地址，本地用localhost‍）
           dashboard: 10.211.55.26:8858
           # 默认8719端口，假如被占用了会自动从8719端口+1进行扫描，直到找到未被占用的 端口
           port: 8719
           
   
   management:
     endpoints:
       web:
         exposure:
           include: '*'
   123456789101112131415161718192021222324
   ```

4. 主启动类

   ```java
   @EnableDiscoveryClient
   @SpringBootApplication
   public class MainApp8401 {
   
       public static void main(String[] args) {
           SpringApplication.run(MainApp8401.class, args);
       }
   }
   12345678
   ```

5. controller

   ```java
   @RestController
   public class FlowLimitController {
   
       @GetMapping("/testA")
       public String testA() {
           return "----testA";
       }
   
       @GetMapping("/testB")
       public String testB() {
           return "----testB";
       }
   
   }
   1234567891011121314
   ```

6. 测试，启动8401，然后刷新sentinel后台页面（因为sentinel采用懒加载策略，所以需要调用服务后才在后台显示）
   在浏览器分别输入，然后刷新sentinel后台页面：
   `http://localhost:8401/testA`
   `http://localhost:8401/testB`
   ![在这里插入图片描述](img/20200627203130401.png)

## 流控规则

![在这里插入图片描述](img/20200627203749625.png)
流量控制
![在这里插入图片描述](img/20200627221012644.png)
可在流控规则处新建：
![在这里插入图片描述](img/20200627203511275.png)
也可簇点链路处指定添加：
![在这里插入图片描述](img/20200627203934748.png)
![在这里插入图片描述](img/20200627204309211.png)
每秒请求数超过1个就会限流。
![在这里插入图片描述](img/20200627204359617.png)

#### 阈值类型

##### QPS与线程数的区别

![在这里插入图片描述](img/20200627221948269.png)
![在这里插入图片描述](img/20200627204923850.png)
QPS是直接挡在外面，而线程数是有多少个线程在处理，放进来后，有线程是空闲状态就对请求进行处理，都没空闲，就限流（关门打狗）。

#### 流控模式

![在这里插入图片描述](img/20200627222003178.png)

##### 直接

![在这里插入图片描述](img/20200627210923966.png)

##### 关联

![在这里插入图片描述](img/20200627210824986.png)
A去调用B，B如果资源不足了，就限流A。
![在这里插入图片描述](img/2020062721105934.png)
![在这里插入图片描述](img/20200627211144112.png)
此时不管调用多少次A都不会限流，而此时超过1秒调用1次B，则会限流A。

使用Postman进行多次访问。
![在这里插入图片描述](img/20200627211416496.png)
![在这里插入图片描述](img/20200627211753966.png)
![在这里插入图片描述](img/20200627211815816.png)
![在这里插入图片描述](img/20200627211837904.png)
![在这里插入图片描述](img/20200627212217329.png)
![在这里插入图片描述](img/20200627212410212.png)

##### 链路

在网上搜关于链路的是用下面这个例子，然而显示不了限流的效果。

新建一个TestService

```java
@Service
public class TestService {

    @SentinelResource("getTest")
    public void getTest(){
    System.out.println("getTest...");
    }

}
123456789
```

然后在FlowLimitController添加修改：

```java
    @Resource
    TestService testService;
    
    @GetMapping("/testA")
    public String testA() {
        testService.getTest();
        return "----testA";
    }

    @GetMapping("/testB")
    public String testB() {
        testService.getTest();
        return "----testB";
    }
1234567891011121314
```

然后重新启动8401，输入`http://localhost:8401/testA`和`http://localhost:8401/testB`
![在这里插入图片描述](img/20200627214203570.png)
给getTest添加流控链路规则。
![在这里插入图片描述](img/20200627214324203.png)

#### 流控效果

流控效果只有QPS有，线程数没有。
![在这里插入图片描述](img/20200627222022296.png)

##### 快速失败

![在这里插入图片描述](img/20200627222107396.png)

##### 预热（Warm Up）

![在这里插入图片描述](img/20200627222222108.png)
![在这里插入图片描述](img/20200627222250298.png)
![在这里插入图片描述](img/20200627222301981.png)
![在这里插入图片描述](img/20200627222337994.png)
![在这里插入图片描述](img/20200627222524337.png)
![在这里插入图片描述](img/2020062722253693.png)
对testA新增流控规则
![在这里插入图片描述](img/20200627222825688.png)
然后输入`http://localhost:8401/testA`，不停刷新，前5秒会限流，5秒后只要每秒不超过10个请求，就不会限流。

##### 排队等待

![在这里插入图片描述](img/20200627223349952.png)
![在这里插入图片描述](img/20200627223447141.png)
![在这里插入图片描述](img/20200627223511665.png)
![在这里插入图片描述](img/20200627223405881.png)
在testB方法中添加

```java
        log.info(Thread.currentThread().getName() + "\t ...testB");
1
```

然后重启8401
![在这里插入图片描述](img/20200627223831658.png)

![在这里插入图片描述](img/20200627224039842.png)
![在这里插入图片描述](img/20200627224407920.png)
postman发起的每秒10个请求进行排队，testB每秒处理一个请求。
![在这里插入图片描述](img/20200627224423675.png)

## 降级规则

![在这里插入图片描述](img/20200627225406426.png)
![在这里插入图片描述](img/2020062722552498.png)
![在这里插入图片描述](img/20200627225557865.png)
![在这里插入图片描述](img/20200627225620762.png)
![在这里插入图片描述](img/20200627225742704.png)
![在这里插入图片描述](img/20200627225800262.png)

### 降级策略实战

![在这里插入图片描述](img/2020062722591022.png)

#### RT

![在这里插入图片描述](img/20200628113651544.png)
![在这里插入图片描述](img/20200628113530801.png)
![在这里插入图片描述](img/20200628113708120.png)

在FlowLimitController中添加

```java
    @GetMapping("/testD")
    public String testD() {
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        log.info("testD 测试RT");
        return "----testD";
    }
12345678910
```

启动8401，在浏览器输入`http://localhost:8401/testD`，然后在sentinel设置testD降级规则。
![在这里插入图片描述](img/20200628114328868.png)
*请求处理完成的时间为200毫秒（阈值），超过这个时间熔断降级进入时间窗口期不处理请求，1秒后退出时间窗口期，继续处理请求。（前提是一秒请求数超过5个，如果请求数没超过5个，就算请求处理的时间超过阈值也不会熔断降级）*

然后用Jmeter进行压力测试。
![在这里插入图片描述](img/20200628114812157.png)
![在这里插入图片描述](img/20200628114852797.png)
![在这里插入图片描述](img/20200628115703379.png)
![在这里插入图片描述](img/20200628115816124.png)
![在这里插入图片描述](img/20200628115844609.png)
![在这里插入图片描述](img/20200628120412874.png)

#### 异常比例

![在这里插入图片描述](img/20200628120730628.png)
![在这里插入图片描述](img/20200628113536596.png)
![在这里插入图片描述](img/20200628120755666.png)

修改testD

```java
    @GetMapping("/testD")
    public String testD() {
        log.info("testD 异常比例");
        int age = 10 / 0;   //百分百出错

        return "----testD";
    }
1234567
```

启动8401，浏览器输入`http://localhost:8401/testD`，然后在后台设置testD的降级规则。
![在这里插入图片描述](img/20200628121252259.png)
*处理请求出错超过0.2（20%，阈值），熔断降级，进入时间窗口期不处理请求，3秒后退出时间窗口期继续处理请求。（前提也是每秒请求数超过5个，防止不会熔断降级）*

用Jmeter进行测试
![在这里插入图片描述](img/20200628121541624.png)
![在这里插入图片描述](img/20200628121627449.png)
![在这里插入图片描述](img/2020062812180318.png)

#### 异常数

![在这里插入图片描述](img/20200628121919162.png)
![在这里插入图片描述](img/20200628113543221.png)
因为是按照一分钟内的异常数对阈值进行比较，如果时间窗口小于60秒，可能会再次进入熔断状态。所以时间窗口一定要大于等于60秒。
![在这里插入图片描述](img/20200628122010188.png)

在FlowLimitController里添加：

```java
    @GetMapping("/testE")
    public String testE() {
        log.info("testE 测试异常数");
        int age = 10 / 0;
        return "----testE 测试异常数";
    }
123456
```

![在这里插入图片描述](img/202006281223570.png)
启动8401，浏览器输入`http://localhost:8401/testE`，然后在后台设置testE的降级规则。
![在这里插入图片描述](img/20200628122533938.png)
一分钟内，如果访问处理出现异常的次数超过5次，熔断降级，进入时间窗口期不处理请求，61秒后退出时间窗口期继续处理请求。（时间窗口必须大于等于60秒，防止再次熔断降级）

刷新6次，`http://localhost:8401/testE`
![在这里插入图片描述](img/20200628122834594.png)

## 热点key限流

![在这里插入图片描述](img/20200628160604379.png)
![在这里插入图片描述](img/20200628160658572.png)
![在这里插入图片描述](img/20200628160830330.png)

在FlowLimitController中添加：

```java
    @GetMapping("/testHotKey")
    @SentinelResource(value = "testHotKey", blockHandler = "deal_testHotKey")
    public String testHotKey(@RequestParam(value = "p1", required = false)String p1,
                             @RequestParam(value = "p2", required = false)String p2) {
        return "----testHotKey";
    }
    
    //兜底方法
    public String deal_testHotKey(String p1, String p2, BlockException exception) {
        // sentinel的默认提示都是： Blocked by Sentinel (flow limiting)
        return "----deal_testHotKey, o(╥﹏╥)o";
    }
123456789101112
```

重启8401，浏览器输入`http://localhost:8401/testHotKey`，然后在后台对testHotKey进行热点规则配置。
![在这里插入图片描述](img/20200628161807406.png)
*如果每秒的访问请求带有索引为0的参数的数量超过1，进入统计窗口期，然后调用兜底方法，1秒后退出统计窗口期，继续处理请求。*

`http://localhost:8401/testHotKey?p1=1`
`http://localhost:8401/testHotKey?p1=1&p2=2`
![在这里插入图片描述](img/20200628162306562.png)
![在这里插入图片描述](img/20200628162341599.png)

如果设置热点规则，而@SentinelResource注解里没有指明blockHandler兜底方法，就会把直接把错误页面信息打印到前台。
![在这里插入图片描述](img/20200628162942435.png)
![在这里插入图片描述](img/20200628162755227.png)
![在这里插入图片描述](img/20200628162853979.png)
![在这里插入图片描述](img/20200628163021393.png)

#### 参数例外项

![在这里插入图片描述](img/2020062816335540.png)
![在这里插入图片描述](img/20200628163517510.png)
高级选项只有在热点规则里有，簇点链路无法配置高级选项。
![在这里插入图片描述](img/2020062816391959.png)
`http://localhost:8401/testHotKey?p1=1`
![在这里插入图片描述](img/20200628164017123.png)
`http://localhost:8401/testHotKey?p1=vip`
![在这里插入图片描述](img/2020062816402910.png)

给testHotKey方法添加`int i = 1 / 0;`异常。
然后重新测试，发现兜底方法不适用于异常，有异常会直接打印到页面。
![在这里插入图片描述](img/20200628164347629.png)
![在这里插入图片描述](img/20200628164507961.png)

## 系统规则

Sentinel 系统自适应限流从整体维度对应用入口流量进行控制，结合应用的 Load、CPU 使用率、总体平均 RT、入口 QPS 和并发线程数等几个维度的监控指标，通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。
![在这里插入图片描述](img/20200628164839869.png)

![在这里插入图片描述](img/20200628165213677.png)
*针对整个系统的，每秒访问量超过1（阈值），限流。*

`http://localhost:8401/testA`
![在这里插入图片描述](img/20200628165238683.png)
`http://localhost:8401/testB`
![在这里插入图片描述](img/20200628165223455.png)

## @SentinelResource

![在这里插入图片描述](img/2020062816544930.png)

### 按资源名称限流+后续处理

资源名称
![在这里插入图片描述](img/2020062817093928.png)
![在这里插入图片描述](img/20200628165705316.png)
![在这里插入图片描述](img/20200628165717721.png)
在pom添加

```xml
 <!--换成你们直接的包名-->
 <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
 <dependency>
     <groupId>com.angenin.springcloud</groupId>
     <artifactId>cloud-api-commons</artifactId>
     <version>${project.version}</version>
 </dependency>
1234567
```

新建RateLimitController：

```java
@RestController
public class RateLimitController {

    @GetMapping("/byResource")
    @SentinelResource(value = "byResource",blockHandler = "handleException")
    public CommonResult byResource() {
        return  new CommonResult(200,"按照资源名称限流测试",new Payment(2020L,"serial001"));
    }

	//兜底方法
    public CommonResult handleException(BlockException exception) {
        return  new CommonResult(444,exception.getClass().getCanonicalName() + "\t 服务不可用");
    }
}
1234567891011121314
```

启动8401
`http://localhost:8401/byResource`
![在这里插入图片描述](img/20200628170450566.png)
![在这里插入图片描述](img/20200628170258305.png)
*用资源名称进行配置兜底方法才生效。*
![在这里插入图片描述](img/20200628170634336.png)
多次刷新页面
![在这里插入图片描述](img/20200628170721637.png)
![在这里插入图片描述](img/20200628170812694.png)
![在这里插入图片描述](img/2020062817090288.png)

### 按照URL地址限流+后续处理

URL地址：
![在这里插入图片描述](img/20200628171012195.png)
![在这里插入图片描述](img/20200628171040519.png)
在RateLimitController中添加：

```java
    @GetMapping("/rateLimit/byUrl")
    @SentinelResource(value = "byUrl")	//没有兜底方法，系统就用默认的
    public CommonResult byUrl() {
        return  new CommonResult(200,"按照byUrl限流测试",new Payment(2020L,"serial002"));
    }
12345
```

启动8401
`http://localhost:8401/rateLimit/byUrl`
![在这里插入图片描述](img/20200628171339293.png)
![在这里插入图片描述](img/20200628171421358.png)
多次刷新：
![在这里插入图片描述](img/20200628171527677.png)

### 上面兜底方案面临的问题

![在这里插入图片描述](img/2020062817164342.png)

### 客户自定义限流处理逻辑

![在这里插入图片描述](img/20200628171825850.png)
新建myhandler.CustomerBlockHandler自定义限流处理类：

```java
public class CustomerBlockHandler {

    public static CommonResult handlerException(BlockException exception) {
        return  new CommonResult(444,"按照客户自定义限流测试，Glogal handlerException ---- 1");
    }

    public static CommonResult handlerException2(BlockException exception) {
        return  new CommonResult(444,"按照客户自定义限流测试，Glogal handlerException ---- 2");
    }
}
12345678910
```

在RateLimitController中添加：

```java
    //CustomerBlockHandler
    @GetMapping("/rateLimit/customerBlockHandler")
    @SentinelResource(value = "customerBlockHandler",
            blockHandlerClass = CustomerBlockHandler.class, blockHandler = "handlerException2")
    public CommonResult customerBlockHandler() {
        return  new CommonResult(200,"按照客户自定义限流测试",new Payment(2020L,"serial003"));
    }
1234567
```

启动8401
`http://localhost:8401/rateLimit/customerBlockHandler`
![在这里插入图片描述](img/20200628173122362.png)
*用资源名称进行配置兜底方法才生效。*
![在这里插入图片描述](img/20200628173140835.png)
多次刷新：
![在这里插入图片描述](img/20200628173152873.png)
![在这里插入图片描述](img/20200628173415885.png)

### 更多注解属性说明

![在这里插入图片描述](img/20200628173844982.png)
![在这里插入图片描述](img/2020062817410013.png)
![在这里插入图片描述](img/20200628174002805.png)
![在这里插入图片描述](img/20200628174135795.png)
![在这里插入图片描述](img/20200628174204780.png)

## 服务熔断功能

![在这里插入图片描述](img/20200628174413410.png)

### Ribbon系列

![在这里插入图片描述](img/20200628174536631.png)
![在这里插入图片描述](img/20200628174745753.png)

#### 新建提供者

![在这里插入图片描述](img/20200628175307816.png)

1. 新建模块cloudalibaba-provider-payment9003

2. pom

   ```xml
   <dependencies>
       <!-- SpringCloud ailibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- SpringCloud ailibaba sentinel-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   123456789101112131415161718192021222324252627282930313233343536373839404142434445464748
   ```

3. yml

   ```yml
   server:
     port: 9003
   
   spring:
     application:
       name: nacos-payment-provider
     cloud:
       nacos:
         discovery:
           server-addr: 10.211.55.26:8848  #nacos
   
   management:
     endpoints:
       web:
         exposure:
           include: '*'
   12345678910111213141516
   ```

4. 主启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class PaymentMain9003 {
   
       public static void main(String[] args) {
           SpringApplication.run(PaymentMain9003.class,args);
       }
   }
   12345678
   ```

5. controller

   ```java
   @RestController
   public class PaymentController {
   
       @Value("${server.port}")    //spring的注解
       private  String serverPort;
   
       public static HashMap<Long, Payment> map = new HashMap<>();
       static {
           map.put(1L,new Payment(1L,"1111"));
           map.put(2L,new Payment(2L,"2222"));
           map.put(3L,new Payment(3L,"3333"));
       }
   
   
       @GetMapping(value = "/paymentSQL/{id}")
       public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id) {
           Payment payment = map.get(id);
           CommonResult<Payment> result = new CommonResult<>(200,"from mysql,serverPort: " + serverPort,payment);
           return result;
       }
   }
   123456789101112131415161718192021
   ```

6. 按照9003新建9004

#### 新建消费者

![在这里插入图片描述](img/20200628175532411.png)

1. 新建模块cloudalibaba-consumer-nacos-order84

2. pom

   ```xml
   <dependencies>
       <!-- SpringCloud ailibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- SpringCloud ailibaba sentinel-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
       </dependency>
       <!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
           <groupId>com.angenin.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   123456789101112131415161718192021222324252627282930313233343536373839404142434445464748
   ```

3. yml

   ```yml
   server:
     port: 84
   
   spring:
     application:
       name: nacos-order-consumer
     cloud:
       nacos:
         discovery:
           server-addr: 10.211.55.26:8848  #nacos
       sentinel:
         transport:
           dashboard: 10.211.55.26:8858    #sentinel
           port: 8719
   
   #消费者将去访问的微服务名称
   server-url:
     nacos-user-service: http://nacos-payment-provider
   
   #激活Sentinel对Feign的支持
   feign:
     sentinel:
       enabled: true
   1234567891011121314151617181920212223
   ```

4. 主启动类

   ```java
   @EnableDiscoveryClient
   @SpringBootApplication
   @EnableFeignClients
   public class OrderMain84 {
   
       public static void main(String[] args) {
           SpringApplication.run(OrderMain84.class,args);
       }
   }
   123456789
   ```

5. config

   ```java
   @Configuration
   public class ApplicationContextConfig {
   
       @Bean
       @LoadBalanced
       public RestTemplate getRestTemplate() {
           return new RestTemplate();
       }
   }
   123456789
   ```

6. controller

   ```java
   @RestController
   @Slf4j
   public class CircleBreakerController {
   
       public static  final  String SERVICE_URL = "http://nacos-payment-provider";
   
       @Resource
       private RestTemplate restTemplate;
   
       @RequestMapping("/consumer/fallback/{id}")
       @SentinelResource(value = "fallback")   //没有配置
       public CommonResult<Payment> fallback(@PathVariable Long id) {
           CommonResult<Payment> result = restTemplate.getForObject(
                   SERVICE_URL + "/paymentSQL/" + id,CommonResult.class,id);
   
           if(id == 4){
               throw new IllegalArgumentException("IllegalArgument,非法参数异常...");
           }else if(result.getData() == null) {
               throw new NullPointerException("NullPointerException,该ID没有对应记录，空指针异常");
           }
   
           return  result;
       }
       
   }
   12345678910111213141516171819202122232425
   ```

![在这里插入图片描述](img/20200628175652859.png)
![在这里插入图片描述](img/20200628175906431.png)
![在这里插入图片描述](img/20200628175921968.png)
启动9003，9004，84
`http://localhost:84/consumer/fallback/1`
![在这里插入图片描述](img/20200628182940868.png)
![在这里插入图片描述](img/20200628182953343.png)

`http://localhost:84/consumer/fallback/4`
![在这里插入图片描述](img/20200628185025935.png)
`http://localhost:84/consumer/fallback/5`
![在这里插入图片描述](img/20200628185046472.png)

#### 只配置fallback

修改84的CircleBreakerController类的fallback方法中的@SentinelResource注解，并在类中添加

```java
    @SentinelResource(value = "fallback",fallback ="handlerFallback")   //只配置fallback（只负责业务异常）


    //fallback兜底
    public CommonResult handlerFallback(@PathVariable Long id,Throwable e) {
        Payment payment = new Payment(id,"null");
        return new CommonResult(444,"异常handlerFallback，exception内容： " + e.getMessage(), payment);
    }
12345678
```

重新运行
`http://localhost:84/consumer/fallback/4`
![在这里插入图片描述](img/2020062821073051.png)
`http://localhost:84/consumer/fallback/5`
![在这里插入图片描述](img/20200628210717620.png)

#### 只配置blockHandler

修改@SentinelResource注解，并在类中添加

```java
    @SentinelResource(value = "fallback", blockHandler = "blockHandler")	//只配置blockHandler（只负责sentinel控制台配置违规）


    //blockHandler兜底
    public CommonResult blockHandler(@PathVariable Long id,BlockException e) {
        Payment payment = new Payment(id,"null");
        return new CommonResult(444,"blockHandler-sentinel 限流，BlockException： " + e.getMessage(), payment);
    }
12345678
```

重启项目
访问`http://localhost:84/consumer/fallback/1`，然后在sentinel后台进行配置。
![在这里插入图片描述](img/20200628212019453.png)
`http://localhost:84/consumer/fallback/5`
![在这里插入图片描述](img/20200628212513410.png)
因为没配置指定fallback兜底方法，所以会直接显示错误页面，配置了blockHandler兜底方法，所以当sentinel配置违规会执行blockHandler兜底方法。
![在这里插入图片描述](img/20200628212527646.png)

#### 配置fallback和blockHandler

修改@SentinelResource注解

```java
    @SentinelResource(value = "fallback", fallback ="handlerFallback", blockHandler = "blockHandler")
1
```

重启项目，输入`http://localhost:84/consumer/fallback/1`，然后到后台配置。
![在这里插入图片描述](img/20200628213022729.png)
`http://localhost:84/consumer/fallback/1`多次刷新执行blockHandler兜底方法。
![在这里插入图片描述](img/20200628213151592.png)
`http://localhost:84/consumer/fallback/5`执行fallback兜底方法。
![在这里插入图片描述](img/20200628213214555.png)
`http://localhost:84/consumer/fallback/5`多次刷新执行blockHandler兜底方法。
![在这里插入图片描述](img/20200628213327947.png)
当@SentinelResource注解fallback和blockHandler都指定后，然后同时符合，优先执行blockHandler兜底方法。

#### 忽略属性

![在这里插入图片描述](img/20200628213538558.png)
修改@SentinelResource注解：

```java
    @SentinelResource(value = "fallback", 
            fallback ="handlerFallback", 
            blockHandler = "blockHandler", 
            exceptionsToIgnore = {IllegalArgumentException.class})
    //如果出现exceptionsToIgnore中的异常，不运行fallback兜底方法。
12345
```

`http://localhost:84/consumer/fallback/4`
![在这里插入图片描述](img/2020062821402722.png)
`http://localhost:84/consumer/fallback/5`
![在这里插入图片描述](img/20200628213957364.png)

### Feign系列

![在这里插入图片描述](img/20200628214733858.png)
修改84

1. pom

   ```xml
   <!--前面已添加了-->
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   12345
   ```

2. yml

   ```yml
   #前面也已经添加了
   #激活Sentinel对Feign的支持
   feign:
     sentinel:
       enabled: true
   12345
   ```

3. 主启动类上添加`@EnableFeignClients`（也已经添加了）

4. service
   PaymentService接口

   ```java
   @FeignClient(value = "nacos-payment-provider",fallback = PaymentFallbackService.class)
   public interface PaymentService {
   
       @GetMapping(value = "/paymentSQL/{id}")
       public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id);
   
   }
   1234567
   ```

   PaymentFallbackService实现类

   ```java
   @Component
   public class PaymentFallbackService implements PaymentService{
       
       @Override
       public CommonResult<Payment> paymentSQL(Long id) {
           return new CommonResult<>(444,"服务降级返回，---PaymentFallbackService",new Payment(id,"ErrorSerial"));
       }
   }
   12345678
   ```

5. controller
   CircleBreakerController中添加：

   ```java
       //======= OpenFeign
       @Resource
       private PaymentService paymentService;
   
       @GetMapping(value = "/consumer/paymentSQL/{id}")
       public CommonResult< Payment > paymentSQL(@PathVariable("id") Long id){
           return paymentService.paymentSQL(id);
       }
   12345678
   ```

启动项目，`http://localhost:84/consumer/paymentSQL/1`
![在这里插入图片描述](img/2020062821535826.png)
关闭9003和9004，执行@FeignClient的fallback兜底方法PaymentFallbackService。
![在这里插入图片描述](img/20200628215530211.png)

## 熔断限流框架对比

![在这里插入图片描述](img/20200628215703610.png)
![在这里插入图片描述](img/20200628215724116.png)

## 规则持久化

![在这里插入图片描述](img/20200628215850432.png)
![在这里插入图片描述](img/20200628215912935.png)
![在这里插入图片描述](img/20200628220041669.png)
修改8401

1. pom

   ```xml
   	<!--之前添加了-->
       <!-- SpringCloud ailibaba sentinel-datasource-nacos 持久化需要用到-->
       <dependency>
           <groupId>com.alibaba.csp</groupId>
           <artifactId>sentinel-datasource-nacos</artifactId>
       </dependency>
   123456
   ```

2. yml

   ```yml
   	      datasource:
   	        ds1:
   	          nacos:
   	            server-addr: 10.211.55.26:8848  #nacos
           		dataId: ${spring.application.name}
   	            groupId: DEFAULT_GROUP
   	            data-type: json
   	            rule-type: flow
   
   feign:
     sentinel:
       enabled: true #激活Sentinel 对Feign的支持
   123456789101112
   ```

   ![在这里插入图片描述](img/2020062822093314.png)

3. 在nacos后台添加配置：
   ![在这里插入图片描述](img/20200628221616261.png)

   ```json
   [
       {
           "resource": "/rateLimit/byUrl",
           "limitApp": "default",
           "grade": 1,
           "count": 1,
           "strategy": 0,
           "controlBehavior": 0,
           "clusterMode": false
       }
   ]	
   1234567891011
   ```

   ![在这里插入图片描述](img/20200628221507808.png)
   ![在这里插入图片描述](img/20200628221654247.png)
   启动8401
   `http://localhost:8401/rateLimit/byUrl`多次刷新
   ![在这里插入图片描述](img/20200628221856196.png)
   ![在这里插入图片描述](img/20200628222015453.png)
   停止8401
   ![在这里插入图片描述](img/20200628222131689.png)
   再次启动8401，因为sentinel是懒加载模式，所以需要先访问`http://localhost:8401/rateLimit/byUrl`，然后查看sentinel后台。
   ![在这里插入图片描述](img/20200628222337960.png)
   多次刷新`http://localhost:8401/rateLimit/byUrl`
   ![在这里插入图片描述](img/20200628222353217.png)
   实现sentinel配置的持久化。

# 二十、SpringCloud Alibaba Seata处理分布式事务

![在这里插入图片描述](img/20200628223129748.png)

## 分布式事务问题

![在这里插入图片描述](img/20200628223340636.png)
![在这里插入图片描述](img/20200628223555665.png)
![在这里插入图片描述](img/20200628223605824.png)
![在这里插入图片描述](img/20200628223708688.png)

## Seata简介

![在这里插入图片描述](img/20200628223905268.png)
官网：http://seata.io/zh-cn/
![在这里插入图片描述](img/2020062910540184.png)
![在这里插入图片描述](img/20200629105550907.png)
![在这里插入图片描述](img/20200629105532847.png)
![在这里插入图片描述](img/20200629110259489.png)
![在这里插入图片描述](img/2020062912445482.png)
![在这里插入图片描述](img/20200629110707612.png)
![在这里插入图片描述](img/20200629124435961.png)

## Seata-Server安装

![在这里插入图片描述](img/20200629111029878.png)
下载：https://github.com/seata/seata/releases

![在这里插入图片描述](img/20200629111344875.png)
![在这里插入图片描述](img/2020062911115440.png)
![在这里插入图片描述](img/20200629112932421.png)
![在这里插入图片描述](img/20200629112940359.png)
![在这里插入图片描述](img/20200629113100845.png)
![在这里插入图片描述](img/20200629113118878.png)
![在这里插入图片描述](img/20200629113317528.png)
数据库建库建表
*mysql8的同学需要修改file.conf的驱动配置store.db.driver-class-name；并lib目录下删除mysql5驱动，添加mysql8驱动。*

启动nacos

启动seata

#### docker下载安装

mysql5.6：

```shell
#启动数据库容器（注意，我这里数据库暴露的是3305端口）
docker start 数据库容器ID
#docker run -p 3305:3306 --name mysql5.6 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6

#进入mysql5.6容器
docker exec -it 容器ID /bin/bash

#进入mysql
mysql -uroot -p123456  --default-character-set=utf8

#创建seata数据库
create database seata character set utf8;
use seata;

#创建seata数据库需要的表（三张表）
CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME(6),
    `gmt_modified`      DATETIME(6),
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(96),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(32),
    `pk`             VARCHAR(36),
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

#因为之前已经弄过了nacos的持久化，已建了nacos_config数据库了，所以这里就不再赘述。

#退出数据库
exit

#退出容器
exit
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475
```

nacos1.3：

```shell
#启动nacos
docker start nacos容器ID
#docker run --env MODE=standalone --name mynacos -d -p 8848:8848 -e MYSQL_SERVICE_HOST=10.211.55.26  -e MYSQL_SERVICE_PORT=3305  -e MYSQL_SERVICE_DB_NAME=nacos_config  -e MYSQL_SERVICE_USER=root  -e MYSQL_SERVICE_PASSWORD=123456  -e SPRING_DATASOURCE_PLATFORM=mysql  -e MYSQL_DATABASE_NUM=1 nacos/nacos-server
123
```

seata：

```shell
#拉取seata镜像（此时最新版为1.2）
docker pull seataio/seata-server

#运行seata
docker run --name myseata -d -h 10.211.55.26 -p 8091:8091 seataio/seata-server

#进入seata容器
docker exec -it 容器ID /bin/bash
cd resources
#因为容器没有装vim，所以我们要先安装vim
apt-get update
apt-get install vim
#备份文件
cp file.conf file.conf.bk
cp registry.conf registry.conf.bk

#修改file.conf文件（看下图）
vim file.conf
#seata1.2的file.conf里没有service模块，store的mode支持了redis
#mysql8的同学需要修改file.conf的驱动配置store.db.driver-class-name；并lib目录下删除mysql5驱动，添加mysql8驱动。
#按esc键然后:wq!退出

#修改文件（看下图）
vim registry.conf
#按esc键然后:wq!退出

#退出容器
exit

#重启容器
docker restart seata容器ID
12345678910111213141516171819202122232425262728293031
```

file.conf

```shell
#service {
#  vgroupMapping.my_test_tx_group = "fsp_tx_group"
#  default.grouplist = "10.211.55.26:8091"
#  enableDegrade = false
#  disableGlobalTransaction = false
#}
123456
```

![在这里插入图片描述](img/20200629222122110.png)
![在这里插入图片描述](img/20200629114507957.png)
![在这里插入图片描述](img/20200630091108446.png)

```shell
jdbc:mysql://10.211.55.26:3305/seata_order?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
1
```

registry.conf
![在这里插入图片描述](img/20200629124027771.png)
`http://10.211.55.26:8848/nacos`，到nacos后台看seata是否成功注册进nacos。
![在这里插入图片描述](img/2020063009152936.png)
查看注册进nacos的seata信息是否正确。
![在这里插入图片描述](img/20200630091607905.png)

## 订单/库存/账户业务数据库准备

![在这里插入图片描述](img/20200629130341511.png)
![在这里插入图片描述](img/20200629130524138.png)
![在这里插入图片描述](img/20200629130415656.png)
![在这里插入图片描述](img/20200629130540469.png)

```shell
#进入mysql5.6容器
docker exec -it 容器ID /bin/bash

#进入mysql
mysql -uroot -p123456  --default-character-set=utf8

#创建业务数据库和对应的业务表

#order
create database seata_order;

use seata_order;

CREATE TABLE t_order(
`id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
`user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
`product_id` BIGINT(11)DEFAULT NULL COMMENT '产品id',
`count` INT(11) DEFAULT NULL COMMENT '数量',
`money` DECIMAL(11,0) DEFAULT NULL COMMENT '金额',
`status` INT(1) DEFAULT NULL COMMENT '订单状态: 0:创建中; 1:已完结'
)ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;

select * from t_order;

CREATE TABLE IF NOT EXISTS `undo_log`
(
    `branch_id`     BIGINT(20)   NOT NULL COMMENT 'branch transaction id',
    `xid`           VARCHAR(100) NOT NULL COMMENT 'global transaction id',
    `context`       VARCHAR(128) NOT NULL COMMENT 'undo_log context,such as serialization',
    `rollback_info` LONGBLOB     NOT NULL COMMENT 'rollback info',
    `log_status`    INT(11)      NOT NULL COMMENT '0:normal status,1:defense status',
    `log_created`   DATETIME(6)  NOT NULL COMMENT 'create datetime',
    `log_modified`  DATETIME(6)  NOT NULL COMMENT 'modify datetime',
    UNIQUE KEY `ux_undo_log` (`xid`, `branch_id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 1
  DEFAULT CHARSET = utf8 COMMENT ='AT transaction mode undo table';


#storage
create database seata_storage;

use seata_storage;

CREATE TABLE t_storage(
`id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
`product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
`total` INT(11) DEFAULT NULL COMMENT '总库存',
`used` INT(11) DEFAULT NULL COMMENT '已用库存',
`residue` INT(11) DEFAULT NULL COMMENT '剩余库存'
)ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

INSERT INTO t_storage(`id`,`product_id`,`total`,`used`,`residue`)VALUES('1','1','100','0','100');

SELECT * FROM t_storage;

CREATE TABLE IF NOT EXISTS `undo_log`
(
    `branch_id`     BIGINT(20)   NOT NULL COMMENT 'branch transaction id',
    `xid`           VARCHAR(100) NOT NULL COMMENT 'global transaction id',
    `context`       VARCHAR(128) NOT NULL COMMENT 'undo_log context,such as serialization',
    `rollback_info` LONGBLOB     NOT NULL COMMENT 'rollback info',
    `log_status`    INT(11)      NOT NULL COMMENT '0:normal status,1:defense status',
    `log_created`   DATETIME(6)  NOT NULL COMMENT 'create datetime',
    `log_modified`  DATETIME(6)  NOT NULL COMMENT 'modify datetime',
    UNIQUE KEY `ux_undo_log` (`xid`, `branch_id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 1
  DEFAULT CHARSET = utf8 COMMENT ='AT transaction mode undo table';


#account
create database seata_account;

use seata_account;

CREATE TABLE t_account(
`id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'id',
`user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
`total` DECIMAL(10,0) DEFAULT NULL COMMENT '总额度',
`used` DECIMAL(10,0) DEFAULT NULL COMMENT '已用余额',
`residue` DECIMAL(10,0) DEFAULT '0' COMMENT '剩余可用额度'
)ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

INSERT INTO t_account(`id`,`user_id`,`total`,`used`,`residue`)VALUES('1','1','1000','0','1000');

SELECT * FROM t_account;

CREATE TABLE IF NOT EXISTS `undo_log`
(
    `branch_id`     BIGINT(20)   NOT NULL COMMENT 'branch transaction id',
    `xid`           VARCHAR(100) NOT NULL COMMENT 'global transaction id',
    `context`       VARCHAR(128) NOT NULL COMMENT 'undo_log context,such as serialization',
    `rollback_info` LONGBLOB     NOT NULL COMMENT 'rollback info',
    `log_status`    INT(11)      NOT NULL COMMENT '0:normal status,1:defense status',
    `log_created`   DATETIME(6)  NOT NULL COMMENT 'create datetime',
    `log_modified`  DATETIME(6)  NOT NULL COMMENT 'modify datetime',
    UNIQUE KEY `ux_undo_log` (`xid`, `branch_id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 1
  DEFAULT CHARSET = utf8 COMMENT ='AT transaction mode undo table';


#退出mysql
exit

#退出容器
exit
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108
```

## 订单/库存/账户业务微服务准备

![在这里插入图片描述](img/20200629163609356.png)
![在这里插入图片描述](img/20200629163657349.png)

### 订单模块

![在这里插入图片描述](img/20200629163729846.png)

1. 新建模块seata-order-service2001

2. pom

   ```xml
   <dependencies>
       <!-- nacos -->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- seata-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
           <exclusions>
               <exclusion>
                   <groupId>io.seata</groupId>
                   <artifactId>seata-all</artifactId>
               </exclusion>
           </exclusions>
       </dependency>
       <dependency>
           <groupId>io.seata</groupId>
           <artifactId>seata-all</artifactId>
           <version>1.2.0</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
               <dependency>
           <groupId>org.mybatis.spring.boot</groupId>
           <artifactId>mybatis-spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>com.alibaba</groupId>
           <artifactId>druid-spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
       </dependency>
       <!--jdbc-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-jdbc</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162
   ```

3. yml

   ```yml
   server:
     port: 2001
   
   spring:
     application:
       name: seata-order-service
     cloud:
       alibaba:
         seata:
           # 自定义事务组名称需要与seata-server中的对应
           tx-service-group: my_test_tx_group #因为seata的file.conf文件中没有service模块，事务组名默认为my_test_tx_group
           #service要与tx-service-group对齐，vgroupMapping和grouplist在service的下一级，my_test_tx_group在再下一级
           service:
             vgroupMapping:
               #要和tx-service-group的值一致
               my_test_tx_group: default
             grouplist:
               # seata seaver的 地址配置，此处可以集群配置是个数组
               default: 10.211.55.26:8091
       nacos:
         discovery:
           server-addr: 10.211.55.26:8848  #nacos
     datasource:
       # 当前数据源操作类型
       type: com.alibaba.druid.pool.DruidDataSource
       # mysql驱动类
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://10.211.55.26:3305/seata_storage?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
       username: root
       password: 123456
   feign:
     hystrix:
       enabled: false
   logging:
     level:
       io:
         seata: info
   
   mybatis:
     mapperLocations: classpath*:mapper/*.xml
   12345678910111213141516171819202122232425262728293031323334353637383940
   ```

4. file.conf

   ```shell
   transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     # the client batch send request enable
     enableClientBatchSendRequest = true
     #thread factory for netty
     threadFactory {
       bossThreadPrefix = "NettyBoss"
       workerThreadPrefix = "NettyServerNIOWorker"
       serverExecutorThread-prefix = "NettyServerBizHandler"
       shareBossWorker = false
       clientSelectorThreadPrefix = "NettyClientSelector"
       clientSelectorThreadSize = 1
       clientWorkerThreadPrefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       bossThreadSize = 1
       #auto default pin or 8
       workerThreadSize = "default"
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
   }
   service {
     vgroupMapping.my_test_tx_group = "default"
     default.grouplist = "10.211.55.26:8091"
     enableDegrade = false
     disableGlobalTransaction = false
   }
   
   client {
     rm {
       asyncCommitBufferLimit = 10000
       lock {
         retryInterval = 10
         retryTimes = 30
         retryPolicyBranchRollbackOnConflict = true
       }
       reportRetryCount = 5
       tableMetaCheckEnable = false
       reportSuccessEnable = false
       sagaBranchRegisterEnable = false
     }
     tm {
       commitRetryCount = 5
       rollbackRetryCount = 5
       degradeCheck = false
       degradeCheckPeriod = 2000
       degradeCheckAllowTimes = 10
     }
     undo {
       dataValidation = true
       onlyCareUpdateColumns = true
       logSerialization = "jackson"
       logTable = "undo_log"
     }
     log {
       exceptionRate = 100
     }
   }
   12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667
   ```

5. registry.conf

   ```shell
   registry {
     # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
     type = "nacos"
   
     nacos {
       application = "seata-server"
       serverAddr = "10.211.55.26:8848"    #nacos
       namespace = ""
       username = ""
       password = ""
     }
     eureka {
       serviceUrl = "http://localhost:8761/eureka"
       weight = "1"
     }
     redis {
       serverAddr = "localhost:6379"
       db = "0"
       password = ""
       timeout = "0"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       sessionTimeout = 6000
       connectTimeout = 2000
       username = ""
       password = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     sofa {
       serverAddr = "127.0.0.1:9603"
       region = "DEFAULT_ZONE"
       datacenter = "DefaultDataCenter"
       group = "SEATA_GROUP"
       addressWaitTime = "3000"
     }
     file {
       name = "file.conf"
     }
   }
   
   config {
     # file、nacos 、apollo、zk、consul、etcd3、springCloudConfig
     type = "file"
   
     nacos {
       serverAddr = "127.0.0.1:8848"
       namespace = ""
       group = "SEATA_GROUP"
       username = ""
       password = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     apollo {
       appId = "seata-server"
       apolloMeta = "http://192.168.1.204:8801"
       namespace = "application"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       sessionTimeout = 6000
       connectTimeout = 2000
       username = ""
       password = ""
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     file {
       name = "file.conf"
     }
   }
   12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879
   ```

6. domain
   CommonResult

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class CommonResult<T> {
       private Integer code;
       private String message;
       private T data;
   
       public CommonResult(Integer code, String message) {
           this(code, message, null);
       }
   }
   123456789101112
   ```

   Order

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Order {
   
       private Long id;
   
       private Long userId;
   
       private Long productId;
   
       private Integer count;
   
       private BigDecimal money;
   
       private Integer status; // 订单状态 0：创建中 1：已完结
   }
   1234567891011121314151617
   ```

7. Dao

   ```java
   @Mapper
   public interface OrderDao {
   
       //1 新建订单
       int create(Order order);
   
       //2 修改订单状态,从0改为1
       int update(@Param("userId") Long userId, @Param("status") Integer status);
   
   }
   12345678910
   ```

8. mapper
   OrderMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.angenin.springcloud.dao.OrderDao">
       
       <resultMap id="BaseResultMap" type="com.angenin.springcloud.domain.Order">
           <id column="id" property="id" jdbcType="BIGINT" />
           <result column="user_id" property="userId" jdbcType="BIGINT" />
           <result column="product_id" property="productId" jdbcType="BIGINT" />
           <result column="count" property="count" jdbcType="INTEGER" />
           <result column="money" property="money" jdbcType="DECIMAL" />
           <result column="status" property="status" jdbcType="INTEGER" />
       </resultMap>
       
       <insert id="create" parameterType="com.angenin.springcloud.domain.Order"
               useGeneratedKeys="true" keyProperty="id">
           insert into t_order(`user_id`, `product_id`, `count`, `money`, `status`)
               values(#{userId}, #{productId}, #{count}, #{money}, 0);
       </insert>
       <update id="update" parameterType="com.angenin.springcloud.domain.Order">
           update t_order set `status` = 1 
               where `user_id` = #{userId} and `status` = #{status};
       </update>
   </mapper>
   123456789101112131415161718192021222324
   ```

9. service
   StorageService

   ```java
   @FeignClient(value = "seata-storage-service")
   public interface StorageService {
   
       //减库存
       @PostMapping(value = "/storage/decrease")
       CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count);
   }
   1234567
   ```

   AccountService

   ```java
   @FeignClient(value = "seata-account-service")
   public interface AccountService {
   
       @PostMapping(value = "/account/decrease")
       CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
   }
   123456
   ```

   OrderService

   ```java
   public interface OrderService {
   
       void create(Order order);
   
   }
   12345
   ```

10. impl
    OderServiceImpl

    ```java
    @Slf4j
    @Service
    public class OderServiceImpl implements OrderService {
    
        @Resource
        private OrderDao orderDao;
        @Resource
        private StorageService storageService;
        @Resource
        private AccountService accountService;
    
        @Override
        public void create(Order order) {
            //1. 新建订单
            log.info("-------> 开始新建订单");
            orderDao.create(order);
    
            //2. 扣减库存
            log.info("-------> 订单微服务开始调用库存，做扣减count");
            storageService.decrease(order.getProductId(), order.getCount());
            log.info("-------> 订单微服务开始调用库存，做扣减完成");
    
            //3. 扣减账号余额
            log.info("-------> 订单微服务开始调用账号，做扣减money");
            accountService.decrease(order.getUserId(), order.getMoney());
            log.info("-------> 订单微服务开始调用账号，做扣减完成");
    
            //4. 修改订单状态，1代表已完成
            log.info("-------> 修改订单状态");
            orderDao.update(order.getUserId(), 0);
            log.info("-------> 修改订单状态完成");
    
            log.info("-------> 新建订单完成");
        }
    }
    1234567891011121314151617181920212223242526272829303132333435
    ```

11. controller
    OrderController

    ```java
    @RestController
    public class OrderController {
    
        @Resource
        private OrderService orderService;
    
        @GetMapping("/order/create")
        public CommonResult create(Order order){
            orderService.create(order);
            return new CommonResult(200, "订单创建成功!");
        }
    }
    123456789101112
    ```

12. config
    MybatisConfig

    ```java
    @MapperScan("com.angenin.springcloud.dao")
    @Configuration
    public class MybatisConfig {
    }
    1234
    ```

    DataSourceProxyConfig

    ```java
    //使用Seata对数据源进行代理
    @Configuration
    public class DataSourceProxyConfig {
    
        @Value("${mybatis.mapperLocations}")
        private String mapperLocations;
    
        @Bean
        @ConfigurationProperties(prefix = "spring.datasource")
        public DataSource druidDataSource() {
            return new DruidDataSource();
        }
    
        @Bean
        public DataSourceProxy dataSourceProxy(DataSource druidDataSource) {
            return new DataSourceProxy(druidDataSource);
        }
    
        @Bean
        public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
            SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
            bean.setDataSource(dataSourceProxy);
            ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
            bean.setMapperLocations(resolver.getResources(mapperLocations));
            return bean.getObject();
        }
    }
    123456789101112131415161718192021222324252627
    ```

13. 主启动类

    ```java
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class) //取消数据源的自动创建
    @EnableDiscoveryClient
    @EnableFeignClients
    public class SeataOrderMain2001 {
    
        public static void main(String[] args) {
            SpringApplication.run(SeataOrderMain2001.class,args);
        }
    }
    123456789
    ```

14. 启动2001
    ![在这里插入图片描述](img/20200630092213576.png)
    ![在这里插入图片描述](img/20200630092228516.png)
    官方列举的常见问题：https://seata.io/zh-cn/docs/overview/faq.html

### 库存模块

![在这里插入图片描述](img/20200630093104811.png)

1. 新建模块seata-storage-service2002

2. pom

   ```xml
   <dependencies>
       <!-- nacos -->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- seata-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
           <exclusions>
               <exclusion>
                   <groupId>io.seata</groupId>
                   <artifactId>seata-all</artifactId>
               </exclusion>
           </exclusions>
       </dependency>
       <dependency>
           <groupId>io.seata</groupId>
           <artifactId>seata-all</artifactId>
           <version>1.2.0</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
               <dependency>
           <groupId>org.mybatis.spring.boot</groupId>
           <artifactId>mybatis-spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>com.alibaba</groupId>
           <artifactId>druid-spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
       </dependency>
       <!--jdbc-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-jdbc</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162
   ```

3. yml

   ```yml
   server:
     port: 2002
   
   spring:
     application:
       name: seata-storage-service
     cloud:
       alibaba:
         seata:
           # 自定义事务组名称需要与seata-server中的对应
           tx-service-group: my_test_tx_group #因为seata的file.conf文件中没有service模块，事务组名默认为my_test_tx_group
   	    #service要与tx-service-group对齐，vgroupMapping和grouplist在service的下一级，my_test_tx_group在再下一级
           service:
             vgroupMapping:
               #要和tx-service-group的值一致
               my_test_tx_group: default
             grouplist:
               # seata seaver的 地址配置，此处可以集群配置是个数组
               default: 10.211.55.26:8091
       nacos:
         discovery:
           server-addr: 10.211.55.26:8848  #nacos
     datasource:
       # 当前数据源操作类型
       type: com.alibaba.druid.pool.DruidDataSource
       # mysql驱动类
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://10.211.55.26:3305/seata_account?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
       username: root
       password: 123456
   feign:
     hystrix:
       enabled: false
   logging:
     level:
       io:
         seata: info
   
   mybatis:
     mapperLocations: classpath*:mapper/*.xml
   12345678910111213141516171819202122232425262728293031323334353637383940
   ```

4. file.conf

   ```shell
   transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     # the client batch send request enable
     enableClientBatchSendRequest = true
     #thread factory for netty
     threadFactory {
       bossThreadPrefix = "NettyBoss"
       workerThreadPrefix = "NettyServerNIOWorker"
       serverExecutorThread-prefix = "NettyServerBizHandler"
       shareBossWorker = false
       clientSelectorThreadPrefix = "NettyClientSelector"
       clientSelectorThreadSize = 1
       clientWorkerThreadPrefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       bossThreadSize = 1
       #auto default pin or 8
       workerThreadSize = "default"
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
   }
   service {
     vgroupMapping.my_test_tx_group = "default"
     default.grouplist = "10.211.55.26:8091"
     enableDegrade = false
     disableGlobalTransaction = false
   }
   
   client {
     rm {
       asyncCommitBufferLimit = 10000
       lock {
         retryInterval = 10
         retryTimes = 30
         retryPolicyBranchRollbackOnConflict = true
       }
       reportRetryCount = 5
       tableMetaCheckEnable = false
       reportSuccessEnable = false
       sagaBranchRegisterEnable = false
     }
     tm {
       commitRetryCount = 5
       rollbackRetryCount = 5
       degradeCheck = false
       degradeCheckPeriod = 2000
       degradeCheckAllowTimes = 10
     }
     undo {
       dataValidation = true
       onlyCareUpdateColumns = true
       logSerialization = "jackson"
       logTable = "undo_log"
     }
     log {
       exceptionRate = 100
     }
   }
   12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667
   ```

5. registry.conf

   ```shell
   registry {
     # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
     type = "nacos"
   
     nacos {
       application = "seata-server"
       serverAddr = "10.211.55.26:8848"    #nacos
       namespace = ""
       username = ""
       password = ""
     }
     eureka {
       serviceUrl = "http://localhost:8761/eureka"
       weight = "1"
     }
     redis {
       serverAddr = "localhost:6379"
       db = "0"
       password = ""
       timeout = "0"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       sessionTimeout = 6000
       connectTimeout = 2000
       username = ""
       password = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     sofa {
       serverAddr = "127.0.0.1:9603"
       region = "DEFAULT_ZONE"
       datacenter = "DefaultDataCenter"
       group = "SEATA_GROUP"
       addressWaitTime = "3000"
     }
     file {
       name = "file.conf"
     }
   }
   
   config {
     # file、nacos 、apollo、zk、consul、etcd3、springCloudConfig
     type = "file"
   
     nacos {
       serverAddr = "127.0.0.1:8848"
       namespace = ""
       group = "SEATA_GROUP"
       username = ""
       password = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     apollo {
       appId = "seata-server"
       apolloMeta = "http://192.168.1.204:8801"
       namespace = "application"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       sessionTimeout = 6000
       connectTimeout = 2000
       username = ""
       password = ""
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     file {
       name = "file.conf"
     }
   }
   12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879
   ```

6. domain
   CommonResult

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class CommonResult<T> {
       private Integer code;
       private String message;
       private T data;
   
       public CommonResult(Integer code, String message) {
           this(code, message, null);
       }
   }
   123456789101112
   ```

   Storage

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Storage {
   
       private Long id;
   
       private Long productId;
   
       private Integer total;
   
       private Integer used;
   
       private Integer residue;
   }
   123456789101112131415
   ```

7. dao

   ```java
   @Mapper
   public interface StorageDao {
   
       void decrease(@Param("productId") Long productId, @Param("count") Integer count);
   
   }
   123456
   ```

8. mapper
   StorageMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.angenin.springcloud.dao.StorageDao">
   
       <resultMap id="BaseResultMap" type="com.angenin.springcloud.domain.Storage">
           <id column="id" property="id" jdbcType="BIGINT"/>
           <result column="product_id" property="productId" jdbcType="BIGINT"/>
           <result column="total" property="total" jdbcType="INTEGER"/>
           <result column="used" property="used" jdbcType="INTEGER"/>
           <result column="residue" property="residue" jdbcType="INTEGER"/>
       </resultMap>
       
       <update id="decrease">
           update t_storage
           set used = used + #{count}, residue = residue - #{count}
           where product_id= #{productId};
       </update>
   </mapper>
   12345678910111213141516171819
   ```

9. service
   StorageService

   ```java
   public interface StorageService {
   
       void decrease(Long productId, Integer count);
   
   }
   12345
   ```

10. impl
    StorageServiceImpl

    ```java
    @Service
    public class StorageServiceImpl implements StorageService {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(StorageServiceImpl.class);
    
        @Resource
        private StorageDao storageDao;
    
        @Override
        public void decrease(Long productId, Integer count) {
            LOGGER.info("----> StorageService中扣减库存");
            storageDao.decrease(productId, count);
            LOGGER.info("----> StorageService中扣减库存完成");
        }
    }
    123456789101112131415
    ```

11. controller
    StorageController

    ```java
    @RestController
    public class StorageController {
    
        @Resource
        private StorageService storageService;
    
    	@RequestMapping("/storage/decrease")
    	public CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count){
            storageService.decrease(productId, count);
            return new CommonResult(200, "扣减库存成功!");
        }
    
    }
    12345678910111213
    ```

12. config
    MyBatisConfig

    ```java
    @Configuration
    @MapperScan({"com.angenin.springcloud.dao"})
    public class MyBatisConfig {
    }
    1234
    ```

    DataSourceProxyConfig

    ```java
    @Configuration
    public class DataSourceProxyConfig {
    
        @Value("${mybatis.mapperLocations}")
        private String mapperLocations;
    
        @Bean
        @ConfigurationProperties(prefix = "spring.datasource")
        public DataSource druidDataSource() {
            return new DruidDataSource();
        }
    
        @Bean
        public DataSourceProxy dataSourceProxy(DataSource druidDataSource) {
            return new DataSourceProxy(druidDataSource);
        }
    
        @Bean
        public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
            SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
            bean.setDataSource(dataSourceProxy);
            ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
            bean.setMapperLocations(resolver.getResources(mapperLocations));
            return bean.getObject();
        }
    }
    1234567891011121314151617181920212223242526
    ```

13. 主启动类

    ```java
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
    @EnableFeignClients
    @EnableDiscoveryClient
    public class SeataStorageMain2002 {
    
        public static void main(String[] args) {
            SpringApplication.run(SeataStorageMain2002.class,args);
        }
    }
    123456789
    ```

14. 启动2002
    ![在这里插入图片描述](img/202006301037095.png)
    ![在这里插入图片描述](img/20200630103722417.png)

### 账户模块

![在这里插入图片描述](img/2020063009313419.png)

1. 新建模块seata-account-service2003

2. pom

   ```xml
   <dependencies>
       <!-- nacos -->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <!-- seata-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
           <exclusions>
               <exclusion>
                   <groupId>io.seata</groupId>
                   <artifactId>seata-all</artifactId>
               </exclusion>
           </exclusions>
       </dependency>
       <dependency>
           <groupId>io.seata</groupId>
           <artifactId>seata-all</artifactId>
           <version>1.2.0</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-starter-openfeign</artifactId>
       </dependency>
               <dependency>
           <groupId>org.mybatis.spring.boot</groupId>
           <artifactId>mybatis-spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>com.alibaba</groupId>
           <artifactId>druid-spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
       </dependency>
       <!--jdbc-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-jdbc</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162
   ```

3. yml

   ```yml
   server:
     port: 2003
   
   spring:
     application:
       name: seata-account-service
     cloud:
       alibaba:
         seata:
           # 自定义事务组名称需要与seata-server中的对应
           tx-service-group: my_test_tx_group #因为seata的file.conf文件中没有service模块，事务组名默认为my_test_tx_group
           #service要与tx-service-group对齐，vgroupMapping和grouplist在service的下一级，my_test_tx_group在再下一级
           service:	
             vgroupMapping:
               #要和tx-service-group的值一致
               my_test_tx_group: default
             grouplist:
               # seata seaver的 地址配置，此处可以集群配置是个数组
               default: 10.211.55.26:8091
       nacos:
         discovery:
           server-addr: 10.211.55.26:8848  #nacos
     datasource:
       # 当前数据源操作类型
       type: com.alibaba.druid.pool.DruidDataSource
       # mysql驱动类
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://10.211.55.26:3305/seata_order?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
       username: root
       password: 123456
   feign:
     hystrix:
       enabled: false
   logging:
     level:
       io:
         seata: info
   
   mybatis:
     mapperLocations: classpath*:mapper/*.xml
   12345678910111213141516171819202122232425262728293031323334353637383940
   ```

4. file.conf

   ```shell
   transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     # the client batch send request enable
     enableClientBatchSendRequest = true
     #thread factory for netty
     threadFactory {
       bossThreadPrefix = "NettyBoss"
       workerThreadPrefix = "NettyServerNIOWorker"
       serverExecutorThread-prefix = "NettyServerBizHandler"
       shareBossWorker = false
       clientSelectorThreadPrefix = "NettyClientSelector"
       clientSelectorThreadSize = 1
       clientWorkerThreadPrefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       bossThreadSize = 1
       #auto default pin or 8
       workerThreadSize = "default"
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
   }
   service {
     vgroupMapping.my_test_tx_group = "default"
     default.grouplist = "10.211.55.26:8091"
     enableDegrade = false
     disableGlobalTransaction = false
   }
   
   client {
     rm {
       asyncCommitBufferLimit = 10000
       lock {
         retryInterval = 10
         retryTimes = 30
         retryPolicyBranchRollbackOnConflict = true
       }
       reportRetryCount = 5
       tableMetaCheckEnable = false
       reportSuccessEnable = false
       sagaBranchRegisterEnable = false
     }
     tm {
       commitRetryCount = 5
       rollbackRetryCount = 5
       degradeCheck = false
       degradeCheckPeriod = 2000
       degradeCheckAllowTimes = 10
     }
     undo {
       dataValidation = true
       onlyCareUpdateColumns = true
       logSerialization = "jackson"
       logTable = "undo_log"
     }
     log {
       exceptionRate = 100
     }
   }
   12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667
   ```

5. registry.conf

   ```shell
   registry {
     # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
     type = "nacos"
   
     nacos {
       application = "seata-server"
       serverAddr = "10.211.55.26:8848"    #nacos
       namespace = ""
       username = ""
       password = ""
     }
     eureka {
       serviceUrl = "http://localhost:8761/eureka"
       weight = "1"
     }
     redis {
       serverAddr = "localhost:6379"
       db = "0"
       password = ""
       timeout = "0"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       sessionTimeout = 6000
       connectTimeout = 2000
       username = ""
       password = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     sofa {
       serverAddr = "127.0.0.1:9603"
       region = "DEFAULT_ZONE"
       datacenter = "DefaultDataCenter"
       group = "SEATA_GROUP"
       addressWaitTime = "3000"
     }
     file {
       name = "file.conf"
     }
   }
   
   config {
     # file、nacos 、apollo、zk、consul、etcd3、springCloudConfig
     type = "file"
   
     nacos {
       serverAddr = "127.0.0.1:8848"
       namespace = ""
       group = "SEATA_GROUP"
       username = ""
       password = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     apollo {
       appId = "seata-server"
       apolloMeta = "http://192.168.1.204:8801"
       namespace = "application"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       sessionTimeout = 6000
       connectTimeout = 2000
       username = ""
       password = ""
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     file {
       name = "file.conf"
     }
   }
   12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879
   ```

6. domain
   CommonResult

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class CommonResult<T> {
       private Integer code;
       private String message;
       private T data;
   
       public CommonResult(Integer code, String message) {
           this(code, message, null);
       }
   }
   123456789101112
   ```

   Account

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Account {
   
       private Long id;
   
       private Long userId;
   
       private BigDecimal total;
   
       private BigDecimal used;
   
       private BigDecimal  residue;
   }
   123456789101112131415
   ```

7. dao
   AccountDao

   ```java
   @Mapper
   public interface AccountDao {
   
       void decrease(@Param("userId") Long userId, @Param("money") BigDecimal money);
   
   }
   123456
   ```

8. mapper
   AccountMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.angenin.springcloud.dao.AccountDao">
   
       <resultMap id="BaseResultMap" type="com.angenin.springcloud.domain.Account">
           <id column="id" property="id" jdbcType="BIGINT"/>
           <result column="user_id" property="userId" jdbcType="BIGINT"/>
           <result column="total" property="total" jdbcType="DECIMAL"/>
           <result column="used" property="used" jdbcType="DECIMAL"/>
           <result column="residue" property="residue" jdbcType="DECIMAL"/>
       </resultMap>
   
       <update id="decrease">
           update t_account
           set used = used + #{money}, residue = residue - #{money}
           where user_id = #{userId};
       </update>
   </mapper>
   12345678910111213141516171819
   ```

9. service
   AccountService

   ```java
   public interface AccountService {
   
       void decrease(Long userId, BigDecimal money);
   
   }
   12345
   ```

10. impl
    AccountServiceImpl

    ```java
    @Service
    public class AccountServiceImpl implements AccountService {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(AccountServiceImpl.class);
    
        @Resource
        private AccountDao accountDao;
    
        @Override
        public void decrease(Long userId, BigDecimal money) {
            LOGGER.info("---> AccountService中扣减账户余额");
            accountDao.decrease(userId, money);
            LOGGER.info("---> AccountService中扣减账户余额完成");
        }
    }
    123456789101112131415
    ```

11. controller
    AccountController

    ```java
    @RestController
    public class AccountController {
    
        @Resource
        private AccountService accountService;
    
        @RequestMapping("/account/decrease")
        public CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money){
            accountService.decrease(userId, money);
            return new CommonResult(200, "扣减库存成功!");
        }
    
    }
    12345678910111213
    ```

12. config
    MyBatisConfig

    ```java
    @Configuration
    @MapperScan({"com.angenin.springcloud.dao"})
    public class MyBatisConfig {
    }
    1234
    ```

    DataSourceProxyConfig

    ```java
    @Configuration
    public class DataSourceProxyConfig {
    
        @Value("${mybatis.mapperLocations}")
        private String mapperLocations;
    
        @Bean
        @ConfigurationProperties(prefix = "spring.datasource")
        public DataSource druidDataSource() {
            return new DruidDataSource();
        }
    
        @Bean
        public DataSourceProxy dataSourceProxy(DataSource druidDataSource) {
            return new DataSourceProxy(druidDataSource);
        }
    
        @Bean
        public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
            SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
            bean.setDataSource(dataSourceProxy);
            ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
            bean.setMapperLocations(resolver.getResources(mapperLocations));
            return bean.getObject();
        }
    }
    1234567891011121314151617181920212223242526
    ```

13. 主启动类
    SeataAccountMain2003

    ```java
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
    @EnableFeignClients
    @EnableDiscoveryClient
    public class SeataAccountMain2003 {
    
        public static void main(String[] args) {
            SpringApplication.run(SeataAccountMain2003.class,args);
        }
    }
    123456789
    ```

14. 启动2003
    ![在这里插入图片描述](img/20200630111252329.png)
    ![在这里插入图片描述](img/2020063011131080.png)

## Test

![在这里插入图片描述](img/20200630111535666.png)
![在这里插入图片描述](img/20200630111513240.png)

### 正常下单

启动2001，2002，2003

在浏览器输入：`http://localhost:2001/order/create?userId=1&productId=1&count=10&money=10`
![在这里插入图片描述](img/20200630120105578.png)
![在这里插入图片描述](img/20200630121106373.png)
![在这里插入图片描述](img/20200630120748449.png)
![在这里插入图片描述](img/20200630120802695.png)
![在这里插入图片描述](img/20200630120818823.png)

### 超时异常

1. 停止2003。

2. 在2003的AccountServiceImpl里的decrease中添加

   ```java
           //模拟超时异常，暂停20秒
           try {
               TimeUnit.SECONDS.sleep(20);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
   123456
   ```

   ![在这里插入图片描述](img/20200630121727901.png)

3. 重新启动2003。

4. 刷新页面`http://localhost:2001/order/create?userId=1&productId=1&count=10&money=10`
   ![在这里插入图片描述](img/20200630121945934.png)
   ![在这里插入图片描述](img/20200630122043779.png)
   ![在这里插入图片描述](img/20200630122028486.png)
   ![在这里插入图片描述](img/20200630122009712.png)
   超时异常后，order添加了订单，而且storage的库存和account的余额都发生了变化。
   因为feign调用时间默认是1秒，超过1秒就不等待，直接返回超时异常，但是account在20秒后还是会去扣余额，而且没有回滚，所以order添加了订单，storage的库存也发生了变化。
   而且feign有超时重试机制，所以可能会多次扣款。

### 

1. 停止2001。

2. 在2001的OderServiceImpl里的create方法上加上：

   ```java
       //name随便命名，只要不重复即可
    	//rollbackFor = Exception.class表示出现所有异常都回滚
       //rollbackFor表示哪些需要回滚
       //noRollbackFor表示哪些不需要回滚
       @GlobalTransactional(name = "fsp-create-order", rollbackFor = Exception.class)
   12345
   ```

3. 重启2001。

4. 刷新页面`http://localhost:2001/order/create?userId=1&productId=1&count=10&money=10`
   ![在这里插入图片描述](img/20200630130652667.png)
   ![在这里插入图片描述](img/20200630130711645.png)
   ![在这里插入图片描述](img/20200630130731105.png)
   ![在这里插入图片描述](img/20200630130748817.png)

订单没有添加，storage和account也没变化，回滚成功。

## 补充

![在这里插入图片描述](img/20200630131045784.png)

#### Seata

![在这里插入图片描述](img/20200630131150888.png)

#### TC/TM/RM三组件

![在这里插入图片描述](img/2020063013160610.png)
![在这里插入图片描述](img/20200630131757543.png)

#### 分布式事务的执行流程

![在这里插入图片描述](img/20200630131834275.png)
![在这里插入图片描述](img/20200630132104786.png)
seata文档：http://seata.io/zh-cn/docs/overview/what-is-seata.html
![在这里插入图片描述](img/20200630132251697.png)
![在这里插入图片描述](img/20200630132332253.png)
![在这里插入图片描述](img/20200630151455614.png)
![在这里插入图片描述](img/20200630151502938.png)
![在这里插入图片描述](img/20200630151612251.png)
![在这里插入图片描述](img/20200630151710251.png)
![在这里插入图片描述](img/20200630151813118.png)
![在这里插入图片描述](img/20200630152640763.png)