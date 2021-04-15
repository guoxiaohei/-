![image-20201202042023172](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/61a69fed6062103fa90e1521edf0f8e3.png)

**名词解释：**

DispatcherServlet：前端控制器，是 SpringMVC 工作流程的中心，负责调用其他组件，在系统启动时就加载该类。

Handler：后端处理器，对用户具体请求进行处理，也就是我们编写的 Controller 类。

HandlerMapping：处理器映射器，根据用户访问的 URL 映射到对应的后端处理器 Handler，根据不同的映射处理器可实现不同的映射，比如 xml 配置（现在不常用）、注解配置（最常用）。

HandlerExecutionChain：后端处理器 Handler 相关对象，包括 Handler 对象和对应的拦截器对象，以 HandlerExecutionChain 对象包含了这些相关的对象。

ModelAndView：逻辑视图，包括数据模型和视图名。

HandlerAdapter：处理器适配器，调用后端处理器中的方法，返回逻辑视图 ModelAndView 对象。

ViewResolver：视图解析器，将 ModelAndView 逻辑视图解析为具体的视图（如 JSP，PDF等）。

**工作流程：**

1、用户发送请求至前端控制器DispatcherServlet。

2、DispatcherServlet收到请求调用HandlerMapping处理器映射器。

3、处理器映射器找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。

4、DispatcherServlet调用HandlerAdapter处理器适配器。

5、HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。

6、Controller执行完成返回ModelAndView。

7、HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。

8、DispatcherServlet将ModelAndView传给ViewReslover视图解析器。

9、ViewReslover解析后返回具体View。

10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。

11、DispatcherServlet响应用户。

