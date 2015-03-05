## 最小化Spring XML配置

###自动装配Bean属性
如果我们的应用上下文中只有一个_javax.sql.DataSource_类型的Bean，那么任意一个依赖_DataSource_的其他Bean就是需要这个_DataSource_ Bean。毕竟这里只有一个_DataSource_ Bean。为了应对这种明确的装配场景，Spring提供了四种自动装配（autowiring）方式：

* byName —— 把与Bean的属性具有相同名字（或者ID）的其他Bean自动装配到Bean的对应属性中。如果没有跟属性的名字相匹配的Bean，则该属性不进行装配。
* byType —— 把与Bean的属性具有相同类型的其他Bean自动装配到Bean的对应属性中。如果没有跟属性的类型相匹配的Bean，则该属性不被装配。
*  constructor —— 把与Bean的构造器入参具有相同类型的其他Bean自动装配到Bean构造器的对应入参中。
*  autodetect —— 首先尝试使用constructor进行自动装配。如果失败，再尝试使用byType进行自动装配。

#### byName自动装配

在Spring中，所有东西都会赋予一个名字，如果属性的名字恰好与要被装配到该属性的Bean的名字匹配，Spring就将把这个Bean自动装配到该属性中去：

~~~xml
<bean id="kenny2" class="com.springinaction.springidol.Instrumentalist">
	<property name="song" value="Jingle Bells" />
	<property name="instrument" ref="saxophone" />
</bean>
~~~
上面的配置信息中，我们使用<property>元素显式配置了Kenny的instrument属性。假设使用`<bean>`元素在定义Saxophone时，把Bean的id属性设置成instrument，如：

~~~xml
<bean id="instrument" class="com.springinaction.springidol.Saxophone" />
~~~
那么Saxophone Bean的id属性就与Kenny Bean的instrument属性的名字是一样的。通过配置autowire属性，Spring就可以自动装配kenny的instrument属性：

~~~xml
<bean id="kenny" class="com.springinaction.springidol.Instrumentalist" autowire="byName">
	<property name="song" value="Jingle Bells" />
</bean>
~~~
使用byName自动装配的缺点是需要假设Bean的名字与其他Bean的属性的名字一样。在本例中，如果多个Instrumentalist Bean都被配置为byName自动装配，那么他们都将使用同一个instrument。

#### byType自动装配

byType的工作方式类似于byName自动装配，但是不再是匹配属性的名字而是检查属性的类型。

但是byType的自动装配存在一个局限性：如果Spring寻找到多个Bean，它们的类型与需要自动装配的属性的类型都相匹配，那么Spring不会猜测哪个Bean更适合自动装配，而是选择抛出异常。所以，应用只允许存在一个Bean与需要自动装配的属性类型相匹配。

我们可以使用Bean的primary属性设值为false（默认为true）来标识出非首选Bean，也可以在自动装配时，通过设值Bean的autowire-candidate属性为false来排除该候选Bean。

#### constructor自动装配

如果要通过构造器注入来配置Bean，那我们可以移除<constructor-arg>元素，由Spring在应用上下文中自动选择Bean注入到构造器入参中。例如：

~~~xml
<bean id="duke" class="com.springinaction.springidol.PoeticJuggler" autowire="constructor" />
~~~
上述声明告诉Spring去审视PoeticJuggler的构造器，并尝试在Spring配置中寻找匹配PoeticJuggler某一个构造器所有入参的Bean。

constructor自动装配具有和byType自动装配相同的局限性。

#### 最佳自动装配

配置最佳自动装配使用如下声明：

~~~xml
<bean id="duke" class="com.springinaction.springidol.PoeticJuggler" autowire="autodetect" />
~~~
当配置一个Bean的autowire属性为autodetect时，Spring将首先尝试使用constructor自动装配，如果不行再使用byType自动装配。

#### 默认自动装配
要为Spring应用上下文中的每一个Bean或者其中绝大多数配置相同的autowire属性，那么所需要做的就是在根元素<beans>上增加一个default-autowire属性。

### ==使用注解装配==

