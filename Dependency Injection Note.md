## Dependency Injection Note
###控制反转

假设有一个MovieLister类：

~~~java
class MovieLister...
    public Movie[] moviesDirectedBy(String arg)
    {
        List allMovies = finder.findAll();
        for (Iterator it = allMovies.iterator(); it.hasNext();)
        {
            Movie movie = (Movie) it.next();
            if (!movie.getDirector().equals(arg))
            {
                it.remove();
            }
        }
        return (Movie[]) allMovies.toArray(new Movie[allMovies.size()]);
    }
~~~
该类负责列出某位导演执导的所有影片，它依赖于MovieFinder接口和它的具体实现类。

~~~java
public interface MovieFinder
{
    List findAll();
}
~~~
目前看来还好，两个对象之间没有什么耦合关系，但是实际要搜寻影片时就必须使用MovieFinder的某个具体子类。

~~~java
class MovieLister...
    private MovieFinder finder;
    public MovieLister()
    {
        finder = new ColonDelimitedMovieFinder("movies1.txt");
    }
~~~
问题就出在，如果想更换不同的Finder，如针对SQL，xml，web service等不同的Finder，那么必不可少就需要新的类来获取数据。如果我们想保留MovieLister里面干净漂亮的代码，又希望降低耦合性，MovieLister类只依赖于接口，这样的话如何获得一个MovieFinder子类的实例就成了问题。

在真实的系统中，可能存在非常多的服务和组件，如果我们希望以不同的方式部署这个系统，就需要用插件机制来处理服务之间的交互过程，这样才能在不同的部署方案中使用不同的实现。轻量级容器解决这个问题的手段都是采用控制反转模式。那么问题来了，**控制反转究竟反转了哪方面的控制？**

在Martin Fowler看来，轻量级容器反转的是**如何定位插件的具体实现。**


###依赖注入

依赖注入模式的基本思想是：**用一个单独的对象（装配器）来获得MovieFinder的一个合适的实现，并将其实例赋给MovieLister类的一个字段。**

依赖注入的形式主要有三种：

* 构造函数注入（Constructor Injection）
* 设值方法注入（Setter Injection）
* 接口注入（Interface Injection）

下面举例使用Spring进行Setter Injection：

* 第一步，在MovieLister类中定义setter方法

~~~java
class MovieLister...
    private MovieFinder finder;
    public void setFinder(MovieFinder finder)
    {
        this.finder = finder;
    }
~~~

* 第二步，相似地，在MovieFinder中定义setter方法

~~~java
class ColonMovieFinder...
    public void setFilename(String filename)
    {
        this.filename = filename;
    }
~~~

* 第三步，设定配置文件spring.xml，这里使用XML文件配置

~~~xml
<beans>
    <bean id="MovieLister" class="spring.MovieLister">
        <property name="finder">
            <ref local="colonMovieFinder"/>
        </property>
    </bean>
    <bean id="colonMovieFinder" class="spring.ColonMovieFinder">
        <property name="filename">
            <value>movies1.txt</value>
        </property>
    </bean>
    <bean id="semicolonMovieFinder" class="spring.SemicolonMovieFinder">
        <property name="filename">
            <value>movies1.txt</value>
        </property>
    </bean>
</beans>
~~~

* 测试代码如下

~~~java
public void testWithSpring() throws Exception
    {
        ApplicationContext ctx = new FileSystemXmlApplicationContext("spring.xml");
        MovieLister lister = (MovieLister) ctx.getBean("MovieLister");
        Movie[] movies = lister.moviesDirectedBy("Sergio Leone");
        assertEquals("Once Upon a Time in the West", movies[0].getTitle());
    }
~~~
使用依赖注入最大的好处在于：**它消除了MovieLister类对具体MovieFinder实现类的依赖。**

###构造函数注入 VS. 设值方法注入

构造函数注入和设值方法注入之间的选择反映的是：**应该在哪里填充对象的字段，构造函数还是设值方法？**

构造函数初始化的一个好处是：你可以隐藏任何不可变的字段，只要不为它提供设值方法就行了。如果某个字段不应该被改变，那么它就不应该有设值方法。但是如果参数太多，构造函数就会显得凌乱不堪。这个地方正好是Factory Method模式适用的场合，工厂方法可以借助多个私有构造函数和设值方法的组合来完成自己的任务。问题是工厂往往以静态方法的形式出现，无法在接口中声明它们。也可以创建一个工厂类，但那就变成了另一个服务实体。

如果对象有多个构造函数，对象之间又存在继承关系，事情就会变得非常讨厌。为了让所有东西都正确地初始化，你必须将对子类构造函数的调用转发给超类的构造函数，然后处理自己的参数。这可能造成构造函数规模的进一步膨胀。

###代码配置 VS. 配置文件

对大多数需要在多处部署的应用程序来说，一个单独的配置文件更合适。

相对的，如果应用程序的组装非常复杂，涉及大量的条件步骤，配置逻辑变得复杂的话，那么就应该用一种合适的语言来描述配置信息，使程序逻辑更清晰。然后变成一个构造器类（builder）来完成装配工作。

###分离配置与使用

所有这一切的关键都在于：服务的配置应该与使用分开，正如基本的设计原则：分离接口和实现。在面向对象程序里，我们在一个地方用条件逻辑来决定具体实例化哪一个类，以后的条件分支都由多态来实现，而不是继续重复前面的条件逻辑，这就是分离接口与实现的原则。

###结论

开发的组件需要交给多个应用程序使用时，依赖注入模式会是更好的选择。在DI的风格中，首选构造函数注入，如果遇到了某些特定的问题，再改用设值方法注入。最重要的是==将服务的配置和应用程序内部对服务的使用分离。==