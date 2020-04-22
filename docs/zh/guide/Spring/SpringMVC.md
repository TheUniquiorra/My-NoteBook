# SpringMVC技术总结
## 概述
### 什么是SpringMVC
SpringMVC是一个基于Java实现的MVC模式的轻量级web框架，通过把模型-视图-控制器分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的协作。
### SpringMVC的优点
1. 可以支持各种视图技术，并不仅仅是JSP
2. 与Spring框架集成
3. 清晰的角色分配：前端控制器（DispatcherServlet）,请求到处理器映射（handleMapping），处理器适配器（HandleAdapter）,视图解析器（ViewResolver）
4. 支持各种请求资源的策略映射
## 核心组件
### SprigMVC的主要组件
1. 前端控制器（DispatcherServlet）

接收请求，响应结果，相当于转发器，有了DispatcherServlet可以减少其他组件之间的耦合度。

2. handleMapping

用于查找对应的handle

3. handleAdapter

执行对应的handler并返回modelAndView

4. handle

执行对应的业务逻辑，返回modelAndView

5. viewReslover

对视图进行解析，根据视图名解析成真正的view

6. view

view是一个接口，它的实现类支持不同类型的视图（jsp，freemarker，pdf）

### SpringMVC工作流程
1. 用户发送请求到DispatcherServlet
2. DispatcherServlet根据请求调用对应的handleMapping，请求获取handle
3. handleMapping根据url找到具体的handle，生成处理器对象和拦截器规则（如果有）一并返回给DispatcherServlet
4. DispatcherServlet调用handleAdapter
5. handleAdapter调用对应的handle进行处理，
6. handle执行完返回modelAndView
7. handleAdapter将handle返回的modelAndView返回给 DispatcherServlet
8. DispatcherServlet调用viewReslover对modelAndView进行解析
9. viewReslover将解析好的view返回给 DispatcherServlet
10. DispatcherServlet调用view进行对应数据的渲染
11. view将渲染好的视图返回给 DispatcherServlet
12. DispatcherServlet将渲染好的数据返回给用户


