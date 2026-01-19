---
tags:
  - tech/lang/java
  - type/concept
  - status/growing
description: SpringBoot
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[项目实践 MOC]]

---


# SpringBoot

**web入门**
- Spring Boot将传统Web开发的mvc，json，tomcat等框架整合提供了spring-boot-start-web组件，简化了Web应用配置。
- webmvc为Web开发的基础框架，json为JSON数据解析组件，tomcat为自带的容器以来

**控制器**
- Spring Boot提供了 **@Controller（请求页面和数据）** 和 **@RestController（只请求数据）**两种注解来标识此类负责接收和处理HTTP请求

Model 数据
View 页面
Controller 控制器

1. 用户向 Controller 发送**HTTP请求**
2. Controller 向 Model 请求信息
3. Model 响应信息给 Controller
4. Controller 返回数据给 View
5. View HTTP响应给用户

**路由映射**
- @RequestMapping 注解主要负责URL的路由映射。它可以添加在Controller类或具体的方法上。
- 添加在Controller类上，则对类中所有路由映射都加上此规则；加上方法上，则只对当前方法生效。
- @RequestMapping参数：
	**value**：请求URL的路径，支持URL模板，正则表达式
	**method**：HTTP请求方法
	consumes：请求的媒体类型（Content-Type），如application/json
	produces：相应的媒体类型
	params，headers：请求的参数及请求头的值

**参数传递**
- @RequestParam将请求参数绑定到控制器的方法参数上，传输名和参数名一致可省略
- @PathVaraible：用来处理动态的URL，URL的值可以作为控制器中处理方法的参数
- @RequestBody接收的参数是来自requestBody中，即请求体。一般处理非Content-Type：application/x-www-form-urlencoded编码格式的数据，如application/json

**静态资源访问**
- /static/目录
- 在application.properties中直接定义过滤规则和静态资源位置：
	spring.mvc.static-path-pattern=/static/**
	spring.web.resources.static-locations=classpath:/static/

**文件上传**
- 表单enctype属性规定发送到服务器之前应该如何对表单数据继续进行编码。
- 当表单的enctype="multipart/form-data"时，可以使用MultipartFile获取上传的文件数据，再通过transferTO方法将其写入到磁盘中

**拦截器**
- Spring Boot定义了HandlerInterceptor接口来实现自定义拦截器的功能
- HandlerINterceptir接口定义了preHandle，postHandle，afterCompletion三种方法，通过重写这三种方法实现请求前，请求后等操作

**拦截器注册**
- addPathPatterns方法定义拦截的地址
- excludePathPatterns定义排除某些地址不被拦截
- 添加的一个拦截器没有addPathPattern任何一个url则默认拦截所有请求
- 没有excludePathPattern任何一个url则默认不放过一个请求

**RESRful**
- 每一个URI代表一种资源
- GET获取资源，POST新建资源，PUT更新资源，DELETE删除资源
- 通过操作资源的表现形式来实现服务端请求操作
- 资源的表现形式是JSON或者HTML

**使用Swagger生成WebAPI文档**

#### 注解

@restcontroller
@requestmapping
@PathVaraible
@RequestBody


