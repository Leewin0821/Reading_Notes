## 最小化Spring XML配置

###自动装配Bean属性
如果我们的应用上下文中只有一个 _javax.sql.DataSource_ 类型的Bean，那么任意一个依赖 _DataSource_ 的其他Bean就是需要这个 _DataSource_  Bean。毕竟这里只有一个 _DataSource_  Bean。为了应对这种明确的装配场景，Spring提供了四种自动装配（autowiring）方式：

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
上面的配置信息中，我们使用`<property>`元素显式配置了Kenny的instrument属性。假设使用`<bean>`元素在定义Saxophone时，把Bean的id属性设置成instrument，如：

~~~xml
<bean id="instrument" class="com.springinaction.springidol.Saxophone" />
~~~
那么 _Saxophone_ Bean的id属性就与 _Kenny_ Bean的instrument属性的名字是一样的。通过配置autowire属性，Spring就可以自动装配kenny的instrument属性：

~~~xml
<bean id="kenny" class="com.springinaction.springidol.Instrumentalist" autowire="byName">
	<property name="song" value="Jingle Bells" />
</bean>
~~~
使用byName自动装配的缺点是需要假设Bean的名字与其他Bean的属性的名字一样。在本例中，如果多个 _Instrumentalist_ Bean都被配置为byName自动装配，那么他们都将使用同一个instrument。

#### byType自动装配

byType的工作方式类似于byName自动装配，但是不再是匹配属性的名字而是检查属性的类型。

但是byType的自动装配存在一个局限性：如果Spring寻找到多个Bean，它们的类型与需要自动装配的属性的类型都相匹配，那么Spring不会猜测哪个Bean更适合自动装配，而是选择抛出异常。所以，应用只允许存在一个Bean与需要自动装配的属性类型相匹配。

我们可以使用Bean的primary属性设值为false（默认为true）来标识出非首选Bean，也可以在自动装配时，通过设值Bean的autowire-candidate属性为false来排除该候选Bean。

#### constructor自动装配

如果要通过构造器注入来配置Bean，那我们可以移除`<constructor-arg>`元素，由Spring在应用上下文中自动选择Bean注入到构造器入参中。例如：

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
要为Spring应用上下文中的每一个Bean或者其中绝大多数配置相同的autowire属性，那么所需要做的就是在根元素`<beans>`上增加一个default-autowire属性。

### 使用注解装配

使用注解自动装配与在XML中使用autowire属性自动装配并没有太大区别，但是使用注解方式允许更细粒度的自动装配，我们可以选择性地标注某一个属性来对其应用自动装配。

Spring容器默认禁用注解装配，所以我们需要使用Spring的context命名空间配置中的`<context:annotation-config>`元素来启用它：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- other spring framework declarations -->

<context:annotation-config />
~~~
Spring 3 支持几种不同的用于自动装配的注解：

#### 使用`@Autowired`

假设我们希望用`@Autowired`自动装配Instrumentalist的instrument属性，则可以对 _setInstrument()_ 方法进行标注，如下：

~~~java
@Autowired
public void setInstrument(Instrument instrument) {
	this.instrument = instrument;
}
~~~
当Spring发现我们对 _setInstrument()_ 方法使用了`@Autowired`注解时，Spring就会尝试对该方法执行byType自动装配。

`@Autowired`注解特别有趣的地方在于，我们不仅能使用它标注setter方法，还可以标注需要自动装配Bean引用的任意方法：

~~~java
@Autowired
public void heresYourInstrument(Instrument instrument) {
	this.instrument = instrument;
}
~~~
`@Autowired`注解甚至可以标注构造器：

~~~java
@Autowired
public Instrumentalist(Instrument instrument) {
	this.instrument = instrument;
}
~~~
当对构造器进行标注时，`@Autowired`注解表示当创建Bean时，即使在Spring XML文件中没有使用`<constructor-arg>`元素配置Bean，该构造器也需要进行自动装配。

另外，我们还可以使用`@Autowired`注解直接标注属性，并删除setter方法：

~~~java
@Autowired
private Instrument instrument;
~~~
`@Autowired`注解甚至不会受限于 _private_ 关键字，仍然可以被自动装配。

`@Autowired`的局限是如果Spring上下文中不存在匹配的Bean或者存在多个匹配的Bean，那么`@Autowired`就会失败。解决办法是使用`@Qualifier`来消除歧义，明确指定Bean。

例如，为了确保Spring为 _eddie_ Bean选择guitar来演奏，即使有其他Bean也可以装配到instrument属性中，但我们可以使用`@Qualifier`来明确指定名为guitar的Bean：

~~~java
@Autowired
@Qualifier("guitar")
private Instrument instrument;
~~~
表面上看起来使用`@Qualifier`意味着把`@Autowired`的byType自动装配转换为显式的byName装配。最重要的是，`@Qualifier`注解真正地缩小了自动装配挑选候选Bean的范围。

Spring的`@Autowired`注解是减少Spring XML配置的一种方式，但是使用它的类会引入对Spring的特定依赖。Spring还提供了标注的Java注解来替代`@Autowired`，即`@Inject`。

#### 使用`@Inject`


`@Inject`注解几乎可以完全替换Spring的`@Autowired`注解，它可以用来自动装配属性、方法和构造器。例如：

~~~java
@Inject
private Instrument instrument;
~~~
不同点是，`@Inject`没有 _required_ 属性。因此，`@Inject`注解所标注的依赖关系必须存在，否则会抛异常。

`@Inject`注解消除歧义使用的是`@Named`注解，工作方法类似`@Qualifier`。


#### 在注解注入中使用表达式

Spring中可以使用`@Value`来注解装配String类型的值和基本类型的值。例如：

~~~java
@Value("Eruption")
private String song;
~~~
但是在java代码中硬编码这些值没有什么意义，`@Value`更重要的用处是在运行期通过SpEL动态计算复杂表达式的值并把结果装配到Bean的属性中。如上例：

~~~java
@Value("#(systemProperties.myFavouriteSong)")
private String song;
~~~

### 自动检测Bean

`<context:component-scan>`元素除了完成与`<context:annotation-config>`一样的工作，还允许Spring自动检测Bean和定义Bean。这意味着不使用`<bean>`元素，Spring应用中的大多数（或者所有）Bean都能够实现定义和装配。为了配置Spring自动检测，需要使用`<context:component-scan>`来代替`<context:annotation-config>`元素：

~~~xml
<beans xmlns="http://www.springframework.org/schema/beans"
<!-- other spring framework declarations -->

<context:component-scan
	base-package="com.springinaction.springidol" >
</context:component-scan>

</beans>
~~~
`<context:component-scan>`元素会扫描指定的包及其所有子包，并查找出能够自动注册为Spring Bean的类。那么`<context:component-scan>`元素是如何知道哪些类需要注册为Spring Bean呢？

#### 为自动检测标注Bean

默认情况下，`<context:component-scan>`查找使用构造型（stereotype）注解所标注的类，这些特殊的注解如下：

* `@Component` —— 通用的构造型注解，标识该类为Spring组件
* `@Controller` —— 标识将该类定义为Spring MVC controller
* `@Repository` —— 标识将该类定义为数据仓库
* `@Service` —— 标识将该类定义为服务
* 使用`@Component`标注的任意自定义注解

假设我们的应用上下文仅仅包含eddie和guitar两个Bean。可以配置`<context:component-scan>`元素并使用`@Component`注解标注Instrumentalist和Guitar类，从而消除显式的`<bean>`定义：

~~~java
@Component
public class Guitar implements Instrument {
	public void play() {
		System.out.println("Strum strum strum");
	}
}
~~~
Spring扫描 _com.springinaction.springidol_ 包时，会发现使用`@Component`注解所标注的Guitar，并自动将它注册为Spring Bean。Bean的默认ID为无限定类名，在这种场景下， _Guitar_ Bean的ID为guitar。再标注Instrumentalist类：

~~~java
@Component("eddie")
public class Instrumentalist implements Performer {
	// ...
}
~~~
这种场景下，我们指定了一个Bean ID作为`@Component`注解的参数，该Bean ID不会像上一个示例一样默认设置为类的名称“instrumentalist”，而是显式命名为eddie。

### 使用Spring基于Java的配置

#### 创建基于Java的配置

即使Spring的Java配置可以让我们不使用XML就可以编写大多数的Spring配置，但是我们仍然需要极少量的XML来启用Java配置：

~~~xml
<beans xmlns="http://www.springframework.org/schema/beans"
<!-- other spring framework declarations -->

<context:component-scan
	base-package="com.springinaction.springidol" >
</context:component-scan>

</beans>
~~~
`<context:component-scan>`会自动注册哪些使用stereotype注解所标注的Bean，但是它也会自动加载使用`@Configuration`注解所标注的类。

#### 定义一个配置类

在Java的配置里使用`@Configuration`注解的Java类，等价于XML配置中的`<beans>`元素，例如：

~~~java
@Configuration
public class SpringIdolConfig {
	// Bean declaration methods go here
}
~~~
`@Configuration`注解会作为一个标识告知Spring：这个类将包含一个或多个Spring Bean定义。这些Bean的定义是使用@Bean注解所标注的方法。

#### 声明一个简单的Bean

下例使用`@Bean`注解标注一个方法来定义 _duke_ Bean：

~~~java
@Bean
public performer duke() {
	return new Juggler();
}
~~~
这个简单例子中`@Bean`告知Spring，这个方法将返回一个对象，该对象应该被注册为Spring应用上下文中的一个Bean，方法名将作为该Bean的ID，在该方法中所实现的所有逻辑本质上都是为了创建Bean。在XML配置中，如果重命名了Juggler类，或者会忘记修改相对应的XML配置，因为XML配置中String标识符的缺点是它们无法进行编译期检查。

在Spring基于Java的配置中，Bean的ID和类型都被视为方法签名的一部分，Bean的实际创建是在方法体中定义的，所以可以进行编译期检查来确保Bean的类型是合法类型，并且Bean的ID是唯一的。

#### 使用Spring的基于Java的配置进行注入

只需要把数字直接传入构造器中：

~~~java
@Bean
public Performer duke15() {
	return new Juggler(15);
}
~~~
使用setter也是一样自然的Java代码：

~~~java
@Bean
public Performer kenny() {
	Instrumentalist kenny = new Instrumentalist();
	kenny.setSong("Jingle Bells");
	return kenny;
}
~~~
装配简单值简单易懂，那么为Bean装配另一个Bean的引用呢？为了演示，首选用Java声明一个 _sonnet29_ Bean:

~~~java
@Bean
private Poem sonnet29() {
	return new Sonnet29();
}
~~~
再创建一个 _PoeticJuggler_ Bean，通过构造器为它装配 _sonnet29_ Bean:

~~~java
@Bean
public Performer poeticDuke() {
	return new PoeticJuggler(sonnet29());
}
~~~
在Spring的Java配置中，通过声明方法引用一个Bean并不等同于调用该方法。如果真的这样，每次调用 _sonnet29()_ ，都将得到该Bean的一个新的实例。实际上通过使用`@Bean`注解标注 _sonnet29()_ 方法，会告知Spring我们希望该方法定义的Bean要被注册进Spring的应用上下文中。因此，在其他Bean的声明方法中引用这个方法时，Spring都会拦截该方法的调用，并尝试在应用上下文中查找该Bean，而不是让方法创建一个新的实例。