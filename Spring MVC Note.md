##Spring MVC Note

###Request生命周期

* Request会由DispatcherServlet前端控制器（单例）分配给* Controller控制器（由HandlerMapping处理器通过请求携带的URL信息来确定映射）。

* Controller会卸下Request的负载，处理信息。在Controller完成逻辑处理后，会产生需要返回用户并在浏览器上显示的信息，即Model模型。最后Controller将Model打包，并标示出用于渲染输出的View名称，Controller接下来将Model和View名称发送回DispatcherServlet。

* DispatcherServlet会使用ViewResolver视图解析器来为View名称匹配一个特定的View视图实现，它可能也可能不是JSP。随后交付Model数据给View。View使用Model数据渲染输出，并通过这个输出将响应对象传递给客户端。

###配置DispatcherServlet

* 配置DispatcherServlet的第一步是在web.xml中放入`<servlet>`声明:       

~~~xml
	<servlet>
		<servlet-name>application</servlet-name>
		<servlet-class>
			org.springframework.web.servlet.DispatcherServlet
		</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>
~~~
默认情况下，DispatcherServlet在加载时会从一个基于这个Servlet名字的XML文件中加载Spring应用上下文。本例中即为application-servlet.xml（位于WEB-INF目录下）。

* 一般推荐将DispatcherServlet映射到/，声明了它会作为默认的servlet并且会处理所有的请求，包括对静态资源的请求（Spring的mvc命名空间包含的`<mvc:resource>`元素处理静态资源的请求）。

* `<mvc:resources mapping=“*/resources/**” location=“/resources/*”>`表示所有以/resources路径开头的请求（包括其一下的子路径）都会自动由应用程序根目录下的/resources目录提供服务。

### Controller的用法

* 控制器类需要在类之前加上@Controller注解。@Controller是@Compnent注解的一种具体化，也就是说`<context:component-scan>`将查找使用@Controller注解的类并将其注册为Bean，就像它们使用@Component注解那样。因此需要在application-servlet.xml文件中配置一个 `<context:component-scan>`来扫描控制器类所在的文件夹。

* 按照约定优于配置的开发模式，Controller返回的逻辑视图名称，假设为“home”，会被Resolver自动加上前后缀来确定其JSP文件在Web应用程序中模板的路径。假设在application-servlet.xml中：  

~~~xml
<bean class=...>
<property name="prefix" value="/WEB-INF/views/" />
<property name="suffix" value=".jsp" />
</bean>
~~~

那么View路径就应该是_/WEB-INF/views/home.jsp_ 。

* 为了创建被所有Servlet和Filter共享的Spring容器，需要使用Servlet监听器。ContextLoaderListener能够加载其他的配置文件到一个Spring Context中，因此先在web.xml中声明:

~~~xml
<listener>
	<listener-class>  
		org.springframework.web.context.ContextLoaderListener
	</listener-class>
</listener>
~~~
然后需要给ContextLoaderListener指定加载的配置文件，默认为 _/WEB-INF/applicationContext.xml_ 这个Spring配置文件。但是这个文件本身没有做到将Context拆分为多个片段。所以在需要重写默认实现。

~~~xml
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>
		/WEB-INF/application-security.xml
		classpath:service-context.xml
		classpath:persistence-context.xml
		classpath:dataSource-context.xml
	</param-value>
</context-param>
~~~

* 在处理方法的参数前面加`@RequestParam`注解，如`@RequestParam("spitter") String username`，表明参数username的值应该根据Request中名为spitter的查询参数来获取。基于约定，如果处理方法的所有参数没有注解的话，将绑定到同名的查询参数上。在本例子中，如果处理方法参数名为spitter或者查询参数名为username，这样就可以省略掉`@RequestParam`注解了。如`@RequestParam(value="image", required=false) MultipartFile image`，表明参数image为MultipartFile类型，并且这个参数不是必需的。

* 如果在Controller中，一个方法，如

~~~java
@RequestMapping(method=RequestMethod.GET, params="new")
public String createSpitterProfile(Model model) {
	model.addAttribute(new Spitter());
	return "spitters/edit";
	}
~~~

它的`@RequestMapping`注解没有指定路径的话，那么这个方法会处理类级别`@RequestMapping`指定的路径，假设为 _/spitters_ 。此外，将params属性设置为new，这意味着这个方法只处理对 _/spitters_ 的HTTP GET请求并要求请求中必须包含名为new的查询参数。如下所示：

~~~url
http://localhost:8080/Spitter/spitters?new
~~~
其中 _/Spitter_ 由Servlet上下文决定，_/spitters_ 由上面的Controller来处理，_?new_ 就是“new”查询参数。

* View，如JSP，引用的对象存放在Model模型中。例如JSP的`<c:forEach>`标签的items属性以`${spittleList}`引用了Spittle列表，而spittleList就是Model的addAttribute()方法赋予它的名字。

* 在上面所指定的edit.jsp中，声明并使用Spring的表单绑定库：

~~~jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
...
<form:form method="POST" modelAttribute="spitter" enctype="multipart/form-data">
...
<form:input path="fullName" size="15" id="user_full_name"/>
...
<form:password path="password" size="30" showPassword="true" id="user_password">
~~~
`<form:form>`标签将上面createSpitterProfile()方法所放入Model的Spitter对象（通过modelAttribute属性来标识）绑定到表单中的各个输入域。通过将enctype设置为 _multipart/form-data_, 每个输入域都将作为POST请求的不同部分进行提交，而不仅仅是其他的名称-值。这使得在其中的某一部分包含上传的图片文件数据成为可能。 `<form:input>`和`<form:password>`标签的path属性引用表单所绑定的Spitter对象的属性。当提交表单时，这些输入域中的值会放到Spitter对象中并提交到服务器进行处理。

* 在Controller中，如果

~~~java
return "redirect:/spitters/" + spitter.getUsername();
~~~
那么执行该方法之后，请求将重新定向到指定路径，以防用户点击浏览器“刷新”按钮之后会造成表单的重复提交。

* 谁来响应 _/spitters/{username}_ 请求是个大问题，本例采用在负责对应操作的Controller的方法上打注释来解决：

~~~java
@RequestMapping(value="/{username}", method=RequestMethod.GET)
public String showSpitterProfile(@PathVariable String username, Model model) {
model.addAttribute(spitterService.getSpitter(username));
return "spitters/view";
}
~~~
路径中{username}部分实际上是占位符，它对应使用了@PathVariable注解的username方法参数。请求路径中的该位置的值将作为username的值传递进去。