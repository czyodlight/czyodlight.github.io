# SpringMVC

SpringMVC是基于java实现的MVC设计模型的请求驱动类型的轻量级Web框架。支持RESTful编程风格。

## SpringMVC helloword

- 依赖

  ```xml
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-web</artifactId>
              <version>5.3.4</version>
          </dependency>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-webmvc</artifactId>
              <version>5.3.4</version>
          </dependency>
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>javax.servlet-api</artifactId>
              <version>4.0.1</version>
          </dependency>
          <dependency>
              <groupId>javax.servlet.jsp</groupId>
              <artifactId>javax.servlet.jsp-api</artifactId>
              <version>2.3.3</version>
          </dependency>
  ```

- web.xml

  ```xml
  导入相应包，在web.xml中创建DispatcherServlet
  <!-- springmvc本质上是一个servlet-->
   <servlet>
          <servlet-name>app</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!-- springmvc配置文件地址（可选），默认为WEB-INF下的以servletName-servlet.xml方式命名的文件-->
          <init-param>   
              <param-name>contextConfigLocation</param-name>
              <param-value>classpah:springmvc.xml</param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <servlet-mapping>
          <servlet-name>app</servlet-name>
          <url-pattern>/</url-pattern>
          <mvc:deafult-servlet-handler>
              
      </servlet-mapping>
  ```

  - url-pattern："/*" 表示将所有请求交由 DispatcherServlet 拦截处理 ， "/"所有未被其他servlet接受并处理的请求（静态资源也会接受）

    

- spring配置文件

  ```xml
   <!--   开启扫描，将Controller托管到springmvc,实现类对象，方法和url映射-->
  	<context:component-scan base-package="com.包名"/>
      <!--    修改视图解析器属性-->
      <bean  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <!--        url前缀-->
          <property name="prefix" value="/WEB-INF/views"/>
          <!--        url后缀-->
          <property name="suffix" value=".html"/>
      </bean>
  ```

  - ``` <mvc:default-servlet-handler> ```：访问到静态资源
  - ```<mvc:annotation-driven></mvc:annotation-driven>```

- controller

  ```java
  //前端控制器解析
  @Controller
  public class HelloWord {
      @RequestMapping("/helloWord")
      public ModelAndView toHelloWord(){
          System.out.println("speak");
          //结果数据和视图存储在modelandview对象
          ModelAndView modelAndView = new ModelAndView("/WEB-INF/views/helloWord.jsp");
          return modelAndView;
      }
  }
  //使用视图解析器解析返回页面
  @Controller
  public class HelloWord {
      @RequestMapping("/helloWord")
      public String toHelloWord(){
          System.out.println("speak");       
          return "index";
      }
  }
  ```

  

- views

  ![Demo_views](E:\note\imageOfSpringMVC\Demo_views.png)

## SpringMVC工作流程

![springMVC_flowChart](E:\note\imageOfSpringMVC\springMVC_flowChart.png)

1. DispatcherServlet拦截用户请求
2. DispatcherServlet调用HandlerMapping
3. HandlerMapping根据url找到具体处理器，生成处理器对象及处理器拦截器（如果有）返回DispatcherServlet
4. DispatcherServlet根据返回信息找到适合HandlerAdapter
5. HandlerAdapter调用执行Handler即**Controller**类（后端控制器）
6. 执行完返回ModelAndView对象，该对象包含视图名称或包含模型和视图
7. HandlerAdapter再将ModelAndView对象返回给DispatcherServlet
8. DispatcherServlet根据ModelAndView选择适合的**ViewReslover**(视图控制器)
9. ViewReslover解析后返回View
10. DispatcherServlet对View进行渲染即将模型数据填充到视图中

## SpringMVC数据响应

### 页面跳转

返回字符串与视图解析器前后缀拼接后跳转。

- **直接返回字符串**

  ```java
  return "index";
  ```

- **返回ModelAndView**

  ```java
  ModelAndView modelAndView =new ModelAndView();
  modelAndView.setViewName("index");
  modelAndView.addObject("user",user);
  ```
- **入参**
  
  ```java
  //mvc框架自动入参
  public ModelAndView test(ModelAndView modelandview){}
  public String test(Mode model){
      model.addAttribute(key,value);
  }
  public String test(HttpServletRequest req){
      req.setAttribute(key,value);
  }
  ```

  
  - HttpServletRequest：通过request对象获取请求信息
  - HttpServletResponse：通过response处理响应信息；
  - HttpSession：通过session对象得到session中存放的对象；
  - Model/ModelMap：Model是一个接口，ModelMap是一个接口实现，作用是将model数据填充到request域。

### 回写数据

- **返回字符串**

  - 注入response对象

  ```java
  public String test(HttpServletResponse rsp){
      rsp.getWriter().print("hello word");
  }
  ```
  - springmvc框架 
  
  ```java
  @ResponseBody
  public String test(){
      return"hello world";
  }
  ```

- **返回json**

  ​       将对象转json返回

  - 依赖

  ```xml
          <dependency>
              <groupId>com.fasterxml.jackson.core</groupId>
              <artifactId>jackson-databind</artifactId>
              <version>2.12.2</version>
          </dependency>
          <dependency>
              <groupId>com.fasterxml.jackson.core</groupId>
              <artifactId>jackson-annotations</artifactId>
              <version>2.12.2</version>
          </dependency>
          <dependency>
              <groupId>com.fasterxml.jackson.core</groupId>
              <artifactId>jackson-core</artifactId>
              <version>2.12.2</version>
          </dependency>
  ```

  - 使用

  ```java
      @RequestMapping("/json")
      @ResponseBody
      public String getObjectJson() throws Exception {
          System.out.println("json");
          User user = new User();
          user.setUserID(12);
          user.setUserName("zhangsan");
          user.setUserPassword("234");
          ObjectMapper objectMapper = new ObjectMapper();
          String s = objectMapper.writeValueAsString(user);
          return s;
      }
  ```

  - Spring交由框架

  ```xml
  <!--处理器映射器-->
  <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
      <property name="messageConverters">
          <list>
              <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
          </list>
      </property>
  </bean>
  <!--mvc注解驱动默认实现json转换-->
  <mvc:annotation-driven/>
  ```

  ```java
      @RequestMapping("/json")
      @ResponseBody
      public User getObjectJson() throws Exception {
          System.out.println("json");
          User user = new User();
          user.setUserID(12);
          user.setUserName("zhangsan");
          user.setUserPassword("234");
          return user;
      }
  ```

### 注解驱动

```
<mvc:annotation-driven/>
```

- 用< mvc:annotation-driven>自动加载 RequestMappingHandlerMapping（处理映射器）和RequestMappingHandlerAdapter（处理适配器）
- < mvc:annotation-driven>默认底层集成 jackson进行对象或集合的json格式字符串转换

## SpringMVC请求数据获取

浏览器get数据格式：name=value&name=value....

### 基本参数

- 形参和发送参数name一致，自动映射

  ```java
  //localhost/test?username=zhangsan
  @RequestMapping("/test")
  public void test(String username){}
  ```

### pojo类型参数

- 发送参数name和pojo属性名一致，自动映射

  ```java
  //localhost/test?username=zhangsan&age=80
  @RequestMapping("/test")
  public void test(User user){}
  ```

  

### 数组类型参数

- 发送参数name和数组名称一致，自动映射

  ```java
  //localhost/test?arrs=1&arrs=2&arrs=3
  @RequestMapping("/test")
  public void test(String[] arrs){}
  ```

  

### 集合类型参数

- 中间类

  ```java
  public class VO{
      private List<User> users;
  }
  ```

- 接收数据

  ```java
  @RequestMapping("/test")
  public void test(VO vo){}
  ```

- 表单

  ```html
  <form th:action="/test">
      <input type="text" name="users[0].username">
      <input type="text" name="users[0].userage">
      <input type="text" name="users[1].username">
      <input type="text" name="users[1].userage">
      <input type="submit" value="submit">
  </form>
  ```



## 参数绑定

提交参数和形参名字不一致，使用@RequestParam绑定

```java
//localhost/test?abc=zhangsan
@RequestMapping("/test")
public void test(@RequestParam(value="abc") String username){}
```

其他参数

- required：请求参数是否包含该参数，默认true。没有就会报错
- defaultValue：没有请求参数时，使用指定默认值

## Restful风格

- 示例： 
  - /order/1 HTTP GET ：得到 id = 1 的 order
  - /order/1 HTTP DELETE：删除 id = 1的 order
  - /order/1 HTTP PUT：更新id = 1的 order
  - /order HTTP POST：新增 order

- 请求方法共有：
  - @GetMapping
  - @PostMapping
  - @PutMapping
  - @DeleteMapping

- 参数匹配

  - url后跟{x}占位符绑定请求参数
  - **@PathVariable**匹配占位符和形参

  ```java
  @RequestMapping("/test/{name}")
  public void test(@PathVariable(value="name") String username){}
  ```

## 自定义类型转换

- ``Converter<S,T>``

  ```java
  public class StringConverter implements Converter<String, Date> {
      @Override
      public Date convert(String s) {
          SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
          Date date=null;
          try {
              date=simpleDateFormat.parse(s);
          } catch (ParseException e) {
              e.printStackTrace();
          }
          return date;
      }
  }
  ```

  声明转换器

  ```xml
  <mvc:annotation-driven conversion-service="dateConverter"/>
  <bean id="dateConverter" class="org.springframework.context.support.ConversionServiceFactoryBean">
      <property name="converters">
          <list>
              <bean class="com.halin.converter.StringConverter"></bean>
          </list>
      </property>
  </bean>
  ```

- ``Formatter<T>``

## 获取请求头

@RequestHeader

```java
@RequestMapping("/head")
public void getHead(@RequestHeader(value = "User-Agent",required = false) String userAgent){
    System.out.println(userAgent);
}
```

@CookieValue

```java
@RequestMapping("/head")
public void getHead(@CookieValue(value = "JSESSIONID",required = false) String JSESSIONID){
    System.out.println(JSESSIONID);
}
```

## 文件上传

- 依赖

  ```xml
  <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
  </dependency>
  <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.5</version>
  </dependency>
  ```

- springmvc配置

  ```java
  <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
      <property name="defaultEncoding" value="UTF-8"/>
      <property name="maxUploadSize" value="20000"/>
      </bean>
  ```

- 表单

  ```html
  <body>
      <form action="upload" method="post" enctype="multipart/form-data">
          <input type="file" name="file">
          <input type="submit" value="submit">
      </form>
  </body>
  ```

  - 当form表单修改为多部分表单时，request.getParametert()等api将失效

  - enctype="application/x-www-form-urlencoded"时，form表单的正文内容格式是：**key=value&key=value&key=value**

  - 当frm表单的 enctype取值为 Mutilpart/form-data 时，请求正文内容就变成多部分形式

    ![file_why](E:\note\imageOfSpringMVC\file_why.png)

- controller

  ```java
  @RequestMapping("/uploadTran")
  public void upload(MultipartFile file) throws IOException {
      String fileName = file.getOriginalFilename();
      System.out.println(fileName);
      file.transferTo(new File("E:\\upload\\"+fileName));
  
  }
  ```

  

## 请求数据乱码问题

```xml
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>	<filter-class>org.springframeworkwebfilter. CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name >encoding</param-name>
		<param-value UTF-8</param-value
	</init-param>
</filter>
<filter-mapping>
	<filter-name> CharacterEncodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>filter-mapping>
```

## Spring拦截器（Interceptor）

拦截器使用AOP思想实现

- interceptor和fliter区别：
  - 使用范围：fliter时servlet一部分，javaWeb工程都可以使用，interceptor仅在springMVC工程使用
  - 拦截范围：fliter根据url-pattern配置范围拦截，interceptor只拦截控制器方法

### Interceptor Demo

- 拦截器

  ```java
  //实现HandlerInterceptor
  public class MyFristInterceptor implements HandlerInterceptor {
      //方法之前
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          System.out.println("pre-------");
          return true;
      }
      //方法之后，返回视图之前
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          System.out.println("post-------");
      }
      //所有流程之后
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
          System.out.println("after---------");
      }
  }
  ```

- 配置文件

  ```xml
  <mvc:interceptors>
      <mvc:interceptor>
          <mvc:mapping path="/helloWorld"/>
          <bean class="com.halin.interceptor.MyFristInterceptor"/>
      </mvc:interceptor>
  </mvc:interceptors>
  ```

- 测试

  ```java
  @RequestMapping("/helloWorld")
  public String toHelloWord(){return "helloWorld";}
  ```

  

## SpringMVC异常处理

### SimpleMappingExceptionResolver

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="defaultErrorView" value="error"/>
    <property name="exceptionMappings">
        <map>
            <entry key="java.lang.NullPointerException" value="NPErrorPage"></entry>
            <entry key="com.halin.exception.MyException" value="myErrorPage"></entry>
        </map>
    </property>
</bean>
```

### 自定义异常处理器

```java
public class myER implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView modelAndView = new ModelAndView();
        if(e instanceof ClassCastException){
            modelAndView.setViewName("error");
        }else {
            modelAndView.setViewName("defaultError");
        }
        return modelAndView;
    }
}
```

```xml
<bean class="com.halin.exceptionResolver.myER"/>
```

