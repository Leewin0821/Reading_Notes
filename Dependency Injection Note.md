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