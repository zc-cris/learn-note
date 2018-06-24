# 使用SpringBoot，thymeleaf，bootstrap 完成CRUD

## 一：环境搭建

在idea 中通过spring stater 加入web 和thymeleaf 开发环境，SpringBoot 采用2.0.1 版本，thymeleaf同样是3.0.9 最新版本，bootstrap 使用导入静态资源到static 文件夹下的方式，也可以使用导入webjar依赖的方式

## 二：项目目的

通过使用RESTful 格式的请求完成CRUD请求，同时熟悉 thymeleaf 的语法，使用自定义的springMVC配置文件完成springMVC拓展，修改SpringBoot 的默认配置，掌握SpringBoot 环境下的开发

## 三：导入静态资源（bootstrap以及html页面）并处理

![2018-05-09_114030](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/2018-05-09_114030.png)

### 1. 在所有的html页面中，使用thymeleaf 语法将静态资源文件的引入全部替换，格式如下：



![2](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/2-1525845664748.png)

**==注意==**：这里可以使用webjar 的方式导入静态资源



### 2. 抽取公共html 页面

![3](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/3.png)

将topbar 和sidebar 页面抽取成公共页面 bar.html

```html
<!--指定html代码为公共部分 topbar-->
<nav class="navbar navbar-dark sticky-top bg-dark flex-md-nowrap p-0" th:fragment="topbar">
    <a class="navbar-brand col-sm-3 col-md-2 mr-0" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#">[[${session.loginUser}]]</a>
    <input class="form-control form-control-dark w-100" type="text" placeholder="Search" aria-label="Search">
    <ul class="navbar-nav px-3">
        <li class="nav-item text-nowrap">
            <a class="nav-link" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#">Sign out</a>
        </li>
    </ul>
</nav>

<!--指定html代码为公共部分 sidebar-->
<nav class="col-md-2 d-none d-md-block bg-light sidebar" id="sidebar">
    <div class="sidebar-sticky">
        <ul class="nav flex-column">
            <li class="nav-item">
                <!--引入这个公共页面的页面传入参数，公共页面根据参数决定class 的样式-->
                <a class="nav-link active" href="#" th:href="@{/main}"
                   th:class="${activeUrl =='main'?'nav-link active':'nav-link'}">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
                         stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
                         class="feather feather-home">
                        <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
                        <polyline points="9 22 9 12 15 12 15 22"></polyline>
                    </svg>
                    Dashboard <span class="sr-only">(current)</span>
                </a>
            </li>
            .... //略
        </ul>
    </div>
</nav>
```



### 3. 导入公共的html 页面

```html
    <!--引入页面的公共部分 topbar-->
    <div th:replace="common/bar::topbar"></div>

		<div class="container-fluid">
			<div class="row">
                <!--第二种方式引入页面的公共部分，使用选择器，sidebar-->
                <div th:replace="common/bar::#sidebar(activeUrl = 'main')"></div>

```

**==注意：==**这里thymeleaf 为我们提供了两种方法导入公共的html 页面



### 4. 通过导入参数的方式自定义bootstrap 效果

```html
不同的页面引入公共html 需要导入的参数 
<!--第二种方式引入页面的公共部分，使用选择器，sidebar-->
                <div th:replace="common/bar::#sidebar(activeUrl = 'main')"></div>
<!--第二种方式引入页面的公共部分，使用选择器，sidebar 侧边栏-->
                <div th:replace="common/bar::#sidebar(activeUrl = 'emps')"></div>

公共html 页面需要对导入的参数进行判断
<!--引入这个公共页面的页面传入参数，公共页面根据参数决定class 的样式-->
<li>
    <a class="nav-link active" href="#" th:href="@{/main}"
    th:class="${activeUrl =='main'?'nav-link active':'nav-link'}">
        dashboard
    </a>
<li class="nav-item">
    
<!--引入这个公共页面的页面传入参数，公共页面根据参数决定class 的样式-->
<li>
	<a class="nav-link" th:href="@{/emps}" th:class="${activeUrl =='emps'?'nav-link active':'nav-link'}">
         员工
	</a>
</li>
```



### 5. 效果图

![4](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/4.png)

![5](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/5.png)

![6](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/6.png)



## 四：国际化模块

### 1. 登录页面代码

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
		<meta name="description" content="">
		<meta name="author" content="">
		<title>Signin Template for Bootstrap</title>
		<!-- Bootstrap core CSS -->
        <!-- th:href="@{/webjars/bootstrap/4.1.0/css/bootstrap.css}" -->
		<link href="asserts/css/bootstrap.min.css" th:href="@{/asserts/css/bootstrap.min.css}" rel="stylesheet">
		<!-- Custom styles for this template -->
		<link href="asserts/css/signin.css" th:href="@{/asserts/css/signin.css}" rel="stylesheet">
	</head>

	<body class="text-center">
		<form class="form-signin" th:action="@{/user/login}" method="post">
			<img class="mb-4" src="asserts/img/bootstrap-solid.svg" th:src="@{/asserts/img/bootstrap-solid.svg}" alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
            <!--逻辑判断后台返回的指定数据是否为空-->
            <p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
			<label class="sr-only" th:text="#{login.username}">Username</label>
			<input type="text" name="username" class="form-control" placeholder="Username" th:placeholder="#{login.username}" required="" autofocus="">
			<label class="sr-only" th:text="#{login.username}">Password</label>
			<input type="password" name="password" class="form-control" placeholder="Password" th:placeholder="#{login.password}" required="">
			<div class="checkbox mb-3">
				<label>
          <input type="checkbox" value="remember-me"> [[#{login.remember}]]
        </label>
			</div>
			<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.button}">Sign in</button>
			<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
			<a class="btn btn-sm" th:href="@{/index.html(l='zh-CN')}">中文</a>
			<a class="btn btn-sm" th:href="@{/index.html(l='en-US')}">English</a>
		</form>
	</body>
</html>
```

效果图：

![7](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/7.png)

### 2. 后台逻辑处理

1. 创建国际化文件

![8](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/8-1525846810667.png)

![9](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/9.png)



2. 自定义国际化解析类

   ```java
   /**
    * 自定义国际化解析器，根据前台用户点击中英切换按钮带来的请求参数来进行页面语言的切换
    */
   public class MyLocaleResolver implements LocaleResolver {

       Logger logger = LoggerFactory.getLogger(getClass());

       @Override
       public Locale resolveLocale(HttpServletRequest request) {
           String l = request.getParameter("l");
           // 处理方式一：获取到系统默认的语言和国家信息
           Locale locale = Locale.getDefault();
           // 处理方式二：根据用户请求头的信息来确定locale对象
           String header = request.getHeader("Accept-Language");
           String substring = header.substring(0, 2);
           switch (substring){
               case "en": {
                   locale = new Locale("en", "US");
                   break;
               }
               case "zh": {
                   locale = new Locale("zh", "CN");
                   break;
               }
           }
           if(StringUtils.isNotEmpty(l)){
               String[] split = l.split("-");
               locale = new Locale(split[0], split[1]);
           }
   //        logger.info(request.getHeader("Accept-Language"));
           return locale;
       }
       @Override
       public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {}
   }
   ```


3. 需要在application.properties 文件中指定国际化文件的路径及将自定义国际化解析类放进容器（见下SpringMVC 配置类）

   ```properties
   # 需要告诉SpringBoot 国际化资源文件的路径和前缀
   spring.messages.basename=i18n.login
   ```

   ​

4. 切换效果：

   ![10](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/10.png)

   ![11](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/11.png)

   **==注意：==**前台通过点击中文和English超链接即可完成国际化的切换

   ​

## 五：登录模块

### 1. application.properties 中设置

```properties
# 禁用掉模板引擎的缓存
spring.thymeleaf.cache=false
```

### 2. 登录controller

```java
@Controller
public class UserController {

    //  @RequestMapping(value = "/user/login", method = RequestMethod.POST)  不推荐使用这种方式了
    // 推荐使用这种更加简便的方式完成 restful 风格的映射
    //    @GetMapping
    //    @DeleteMapping
    //    @PutMapping
    @PostMapping(value = "/user/login")
    public String login(@RequestParam("username") String userName,
                        @RequestParam("password") String password,
                        Map<String, Object> map, HttpSession session) {
        if (StringUtils.isNotEmpty(userName) && StringUtils.equals("123", password)) {
            // 登录成功，将登录信息保存到session 中，然后使用重定向，映射规则定义在我们的自定义mvc配置类中
            session.setAttribute("loginUser", userName);
            return "redirect:/main";
        } else {
            map.put("msg", "用户名密码错误");
            return "index";
        }
    }
}
```

### 3. 自定义拦截器进行登录检查

```java
/**
 * 登录检查
 */
public class MyLoginHandlerInterceptor implements HandlerInterceptor {

    // 目标方法执行之前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
        String loginUser = (String) request.getSession().getAttribute("loginUser");

        // 如果当前访问请求中没有用户登录信息，即用户没有登录,返回登录页面
        if(StringUtils.isEmpty(loginUser)){
            // 错误提示消息
            request.setAttribute("msg", "请登录后访问！");
            // 转发至登录页面
            request.getRequestDispatcher("/index.html").forward(request, response);
            return false;
        }
        // 已登录，放行
        return true;
    }
}
```

### 4. config文件夹下自定义SpringMVC 配置类完成功能定制化

```java
// 使用实现 WebMvcConfigurer 接口的方式来扩展SpirngMVC 的各种功能
// 注意java8 之后的接口拥有默认实现方法，所以不需要再继承中间适配器类了
@Configuration
public class MyMVCConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 自定义用户请求处理结果；直接做视图映射，没有必要再写controller中的空映射方法了
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
        registry.addViewController("/main").setViewName("dashboard");
    }

    @Bean
    public LocaleResolver localeResolver() {
        return new MyLocaleResolver();
    }

    // 注册拦截器
//    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // SpringBoot 1.x 版本已经做好了静态资源的映射，这里就不再需要排除了
        registry.addInterceptor(new MyLoginHandlerInterceptor())
                .addPathPatterns("/**")
                // spring5.x 版本(springboot 2.x 版本)后需要对静态资源做出排除，否则还是会拦截
                .excludePathPatterns(Arrays.asList("/", "/index.html", "/user/login", "/asserts/**"));
    }
}
```

### 5. 效果图

![12](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/12.png)



## 六：用户数据展示模块

### 1. 前台list.html

```html
<div class="table-responsive">
                        <table class="table table-striped table-sm">
                            <thead>
                            <tr>
                                <th>#</th>
                                <th>lastName</th>
                                <th>email</th>
                                <th>gender</th>
                                <th>department</th>
                                <th>birth</th>
                                <th>operation</th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr th:each="emp:${emps}">
                                <td th:text="${emp.id}"></td>
                                <!--行内写法-->
                                <td>[[${emp.lastName}]]</td>
                                <td th:text="${emp.email}"></td>
                                <td th:text="${emp.gender}==0?'女':'男'"></td>
                                <td th:text="${emp.department.departmentName}"></td>
                                <td th:text="${#dates.format(emp.birth, 'yyyy-MM-dd')}"></td>
                                <td>
                                    <!--修改和删除员工需要带上对应的员工id-->
                                    <a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.id}">修改</a>
                                    <!--为每一个删除按钮设置一个deleteUri属性，用于设置表单的delete方法提交路径-->
                                    <button class="btn btn-sm btn-danger deleteBtn"
                                            th:attr="deleteUri=@{/emp/}+${emp.id}" style="color: white">删除
                                    </button>
                                </td>
                            </tr>
                            </tbody>
                        </table>
</div>
```



### 2. 后台EmployeeController（dao，service，entities略）

```java
@Controller
public class EmployeeController {

    @Autowired
    EmployeeDao employeeDao;

    @Autowired
    DepartmentDao departmentDao;

    /**
     * @return java.lang.String
     * @Author zc-cris
     * @Description 来到展示员工数据的首页
     * @Param [model]
     **/
    @GetMapping("/emps")
    public String emps(Model model) {
        Collection<Employee> employees = employeeDao.getAll();
        model.addAttribute("emps", employees);
//        employees.forEach(System.out::println);
        return "emp/list";
    }
```



### 3. 效果图

![13](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/13.png)

**==注意：==**通过配置文件完成时间格式转换，thymeleaf 语法取出引用类型的属性值，前台性别判断，自定义bootstrap 按钮



## 七：用户数据添加模块

### 1. 用户添加页面(用户修改页面二合一)

```html
        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
            <!--需要区分是员工添加请求还是员工修改请求-->
            <form th:action="@{/emp}" method="post">

                <!--发送put请求修改用户数据
                1. SpringMVC 中配置HiddenHttpMethodFilter：（SpringBoot 已经自动配置好了）
                2. 页面创建一个post表单
                3. 创建一个input 隐藏项，name=“_method”; value就是我们需要指定的put方法-->
                <input type="hidden" name="_method" value="put" th:if="${emp != null}"/>
                <!--修改员工数据还需要向后台发送对应的员工id-->
                <input type="hidden" name="id" th:if="${emp != null}" th:value="${emp.id}"/>

                <div class="form-group">
                    <label>LastName</label>
                    <input name="lastName" type="text" class="form-control" placeholder="zhangsan"
                           th:value="${emp!=null}?${emp.lastName}">
                </div>
                <div class="form-group">
                    <label>Email</label>
                    <input name="email" type="email" class="form-control" placeholder="zhangsan@atguigu.com"
                           th:value="${emp!=null}?${emp.email}">
                </div>
                <div class="form-group">
                    <label>Gender</label><br/>
                    <div class="form-check form-check-inline">
                        <input class="form-check-input" type="radio" name="gender" value="1"
                               th:checked="${emp!=null}?${emp.gender == 1}">
                        <label class="form-check-label">男</label>
                    </div>
                    <div class="form-check form-check-inline">
                        <input class="form-check-input" type="radio" name="gender" value="0"
                               th:checked="${emp!=null}?${emp.gender == 0}">
                        <label class="form-check-label">女</label>
                    </div>
                </div>
                <div class="form-group">
                    <!--提交部门的id-->
                    <label>department</label>
                    <select class="form-control" name="department.id">
                        <option th:value="${dept.id}" th:each="dept:${depts}" th:text="${dept.departmentName}"
                                th:selected="${emp!=null}?${emp.department.id} == ${dept.id}">1
                        </option>
                    </select>
                </div>
                <div class="form-group">
                    <label>Birth</label>
                    <input name="birth" type="date" class="form-control" placeholder="请选择您的生日"
                           th:value="${emp!=null}?${#dates.format(emp.birth, 'yyyy-MM-dd')}">
                    <!--虽然使用dates工具类格式化时间，但是前台还是显示yyyy/MM/dd 的格式-->
                </div>
                <button type="submit" class="btn btn-primary" th:text="${emp!=null}?'修改':'添加'"></button>
            </form>
        </main>
```



### 2. 点击来到用户添加页面

```java
    /**
     * @return java.lang.String
     * @Author zc-cris
     * @Description 去到添加员工的页面
     * @Param [Model]
     **/
    @GetMapping("/emp")
    public String toAddEmpPage(Model model) {
        // 部门数据需要从数据库查询出来展示在添加员工页面
        Collection<Department> departments = departmentDao.getDepartments();
        model.addAttribute("depts", departments);
        return "emp/addEmp";
    }
```



### 3. 完成用户添加

```java
    /**
     * @return java.lang.String
     * @Author zc-cris
     * @Description 添加员工信息成功并来到员工信息展示首页
     * @Param [employee]
     **/
    @PostMapping("/emp")
    public String addEmp(Employee employee) {
        System.out.println(employee);
        employeeDao.save(employee);

        // redirect: 表示重定向到一个地址 /表示当前项目路径
        // forward:表示转发到一个地址
        return "redirect:/emps";    // 默认是get请求
//        return "forward:/emps"; 默认是post请求
    }
```

**==注意：==**其中时间类型格式化通过配置文件搞定

### 4. 效果图

![1](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/1.png)



![2](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/2-1525853784304.png)

## 八：修改用户数据模块

### 1. 来到修改页面

```java
    /**
     * @return java.lang.String
     * @Author zc-cris
     * @Description 去到修改员工数据的页面（页面回显）
     * @Param [id, model]
     **/
    @GetMapping("/emp/{id}")
    public String toUpdateEmpPage(@PathVariable("id") Integer id, Model model) {
        Employee employee = employeeDao.get(id);
        Collection<Department> departments = departmentDao.getDepartments();
//        System.out.println(employee);
        model.addAttribute("emp", employee);
        model.addAttribute("depts", departments);

        // 添加员工和修改员工页面二合一
        return "emp/addEmp";
    }
```



### 2. 完成数据修改

```java
    /**
     * @return java.lang.String
     * @Author zc-cris
     * @Description 将修改后的数据保存到数据库并重定向到数据展示首页
     * @Param [employee]
     **/
    @PutMapping("/emp")
    public String updateEmp(Employee employee) {
//        System.out.println(employee);
        employeeDao.save(employee);
        return "redirect:/emps";
    }
```



**==注意：==**修改和添加页面二合一，前台数据回显和put请求方式的判断（使用thymeleaf 语法），前台页面代码参考用户添加模块

### 3. 效果图

![3](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/3-1525854079522.png)

![4](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/4-1525854121015.png)



## 九：删除用户数据模块

### 1. 后台逻辑

```java
    /**
     * @return java.lang.String
     * @Author zc-cris
     * @Description 使用delete 方式删除指定员工数据
     * @Param [id]
     **/
    @DeleteMapping("/emp/{id}")
    public String deleteEmp(@PathVariable("id") Integer id) {
        employeeDao.delete(id);
        return "redirect:/emps";
    }
```

### 2. 前台通过js代码发送delete 请求（重点）

```html
<!--为每一个删除按钮设置一个deleteUri属性，用于设置表单的delete方法提交路径-->
<button class="btn btn-sm btn-danger deleteBtn" th:attr="deleteUri=@{/emp/}+${emp.id}" style="color: white">删除
</button>

<!--注意：即便是delete请求，也是需要将form 表单的method属性修改为post请求为前提-->
<form id="deleteEmpForm" method="post">
	<input type="hidden" name="_method" value="delete">
</form>
```

```javascript
<script>
        $(".deleteBtn").click(function () {
            // alert("cris");
            // 通过点击按钮的方式手动提交delete 方式请求的form 表单
            $("#deleteEmpForm").attr("action", $(this).attr("deleteUri")).submit();
            // 取消按钮的默认行为
            return false;
        });
</script>
```

### 效果图

![6](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/6-1525854528319.png)



![7](C:\File\Typora\使用SpringBoot，thymeleaf，bootstrap 完成CRUD\使用SpringBoot，thymeleaf，bootstrap 完成CRUD.assets/7-1525854542109.png)

## 十：针对移动端和客户端定制响应数据

### 如何定制错误响应（针对不同的客户端：浏览器是一个错误页面；手机端是json数据--》根据请求头信息来判断b/s还是c/s）

​	**①. 错误页面可以放在template 路径下的error文件夹下，SpringBoot 默认去这个路径下找对应的错误页面（根据状态码精确或模糊匹配）,可以使用thymeleaf模板引擎得到错误信息**
	**②. 使用postman测试需要先将拦截器暂时失效（卧槽！搞了好久。。。postman主要用于测试后台和前台的数据交互，不能在后台针对请求做出逻辑判断（转发，重定向等），毕竟postman不是浏览器）**
	**③. springboot2.x.x 版本后默认错误消息处理没有exception对象**
	**④. 使用postman测试请求主要注意带上国际化请求头：Accept-Language（因为后台对国际化进行了处理，否则会报错）**



## 十一：项目总结

### 通过SpringBoot 的stater启动器搭建环境，前台页面使用bootstrap 完成，模板引擎使用thymeleaf，完成RESTful 风格的基础crud，其中对于RESTful 请求映射，国际化切换，登录逻辑判断，拦截器，时间格式转换，thymeleaf 语法格式等知识点均进行操练，是一个很不错的熟悉SpirngBoot 开发的上手项目
