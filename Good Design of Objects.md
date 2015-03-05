## 良好对象的设计

###类和对象
类的主要职责是根据需要**创建**对象，以及它们不再被使用的时候**销毁**它们。类知道它的孩子长什么样、如何表现。换言之，类知道它们遵循的合约。

### 1. 对象存在于现实生活中
一个良好的对象，它一定可以映射到现实生活中的某个实体。如果不能，请重构。

### 2. 对象根据合约办事
一个良好的对象总是根据合约办事，它被期望工作是因为它遵循合约而不是它的个人优点。所有良好的对象的每个公共方法都应该实现接口中对应的方法，如果对象有公共方法没有实现任何接口，那就是糟糕的设计。

原因有二：

* 首先，一个没有合约的对象不能在单元测试中进行模拟（mock）。
* 另外，无合约的对象不能通过装饰（decoration）来扩展。

### 3. 对象应该是独特的
一个良好的对象应当总是封装一些东西以保持独特性，否则这个对象可能就有完全一样的克隆体，下面是一个可能有克隆的糟糕对象的例子：

~~~java
class HTTPStatus implements Status {
  private URL page = new URL("http://www.google.com");
  @Override
  public int read() throws IOException {
    return HttpURLConnection.class.cast(
      this.page.openConnection()
    ).getResponseCode();
  
~~~
我可以创建很多HTTPStatus类的实例，但是它们都是相等的：

~~~java
first = new HTTPStatus();
second = new HTTPStatus();
assert first.equals(second);
~~~
很明显，实用类（utility classes）可能只包含静态方法，不能实例化良好对象，严格意义上说甚至不能称作“类”。

### 4. 对象是不可变的
一个良好的对象应该永远不改变它封装的状态。值得注意的是，不可变性并不意味着所有方法都应该返回相同的值。相反，一个良好的不可变对象是非常动态的。然后它不应该改变它的内部状态。比如

~~~java
@Immutable
final class HTTPStatus implements Status {
  private URL page;
  public HTTPStatus(URL url) {
    this.page = url;
  }
  @Override
  public int read() throws IOException {
    return HttpURLConnection.class.cast(
      this.page.openConnection()
    ).getResponseCode();
  }
}
~~~
尽管read()方法返回不同的值，但是这个对象仍然是不可变的。关于对象不可变性的优点，有如下：

* 不可变对象创建、测试和使用更加简单
* 真正的不可变对象总是线程安全的
* 它们可以帮助避免时间耦合
* 它们的用法没有副作用，即没有防御性拷贝
* 它们总是具有失败原子性，即如果方法失败，那么对象状态应该与方法调用前一致
* 它们更容易缓存
* 它们可以防止空引用

当然，一个良好的对象不应该有setter方法，因为这些方法可以改变它的状态。

### 5. 对象的类不应该包含任何静态（static）的东西
一个静态方法实现了类的行为，而不是对象的。假设我们有个类File，它的实例都拥有size()方法：

~~~java
final class File implements Measurable {
  @Override
  public int size() {
    // calculate the size of the file and return
  }
}
~~~
目前为止，一切都还好；size()方法的存在是因为合约Measurable，每个File类的对象都可以测量自身的大小。**一个可怕的错误是将类的这个方法设计为静态方法**（这种类被称作实用类）：

~~~java
// 糟糕的设计，请勿使用！
class File {
  public static int size(String file) {
    // 计算文件大小并返回
  }
}
~~~
这种设计完全违背了面向对象编程（OOP）。为什么？因为静态方法将面向对象编程变成了“面向类”编程（COP）。size()方法将类的行为都暴露出去，而不是它的对象。OOP的强大是允许我们将对象作为一种作用域分解（Scope Decomposition）的工具来用。当我在方法中实例化一个对象，它将专注于我的特定任务。它与这个方法中的其他对象是完全隔离的。这个对象在此方法作用域中是个**局部变量**。

### 6. 对象的名字不是工作头衔
一个对象的名字应该告诉我们这个对象是什么，而不是它做什么。

### 7. 对象的类要么是Final，要么是Abstract
一个良好的对象要么来自一个最终类，要么来自一个抽象类。一个final类不能通过继承被扩展，一个abstract类不能实例化。最终类是个黑盒，你不能通过任何方式进行修改，唯一可以扩展最终类的方法是对它的实例进行包装。假如有个类HTTPStatus（见上），我们希望read()做得更多，在HTTP码大于400时能抛出一个异常。传统做法是扩展这个类，并重写它的方法：

~~~java
class OnlyValidStatus extends HTTPStatus {
  public OnlyValidStatus(URL url) {
    super(url);
  }
  @Override
  public int read() throws IOException {
    int code = super.read();
    if (code &gt; 400) {
      throw new RuntimException("unsuccessful HTTP code");
    }
    return code;
  }
}
~~~
为什么这是错的？因为我们冒险破坏了整个父类的逻辑，因为重写了它的一个方法。一旦在子类重写了read()方法，所有来自父类的方法都会使用新版本的read()方法。字面上讲，其实这是在将一份新的“实现片段”插入到类中。理论上讲，这是种冒犯。

另外，扩展一个最终类，你需要把它当做一个黑盒，然后使用自己的实现来包装它，即装饰者模式：

~~~java
final class OnlyValidStatus implements Status {
  private final Status origin;
  public OnlyValidStatus(Status status) {
    this.origin = status;
  }
  @Override
  public int read() throws IOException {
    int code = this.origin.read();
    if (code &gt; 400) {
      throw new RuntimException("unsuccessful HTTP code");
    }
    return code;
  }
}
~~~
确保该类实现了与原始类相同的接口：Status。HTTPStatus的实例将会通过构造函数被传递和封装给它。然后所有的调用将会被拦截，如果需要，可以通过其他方式来实现。这个设计中，我们把原始对象当做黑盒，而没有触及它的内部逻辑。

如果没有final关键字，任何人都可以扩展这个类并且冒犯它。所以没有final的类是个糟糕的设计。

抽象类则完全相反，它告诉我们它是不完整的，不能直接用它，我们需要将自己的实现逻辑插入其中，但是只插入到它开放给我们的位置。这些位置被显式地标记为abstract。比如：

~~~java
abstract class ValidatedHTTPStatus implements Status {
  @Override
  public final int read() throws IOException {
    int code = this.origin.read();
    if (!this.isValid()) {
      throw new RuntimException("unsuccessful HTTP code");
    }
    return code;
  }
  protected abstract boolean isValid();
}
~~~
如上，这个类不能准确知道如何去验证HTTP状态码，它期望我们通过继承或者重载isValid()方法来插入那一部分逻辑。我们没法通过继承来冒犯它，因为它通过final来保护其他方法。