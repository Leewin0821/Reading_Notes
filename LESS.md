## LESS 语法

LESS作为CSS的扩展，并没有阉割CSS的语言，而是增加了很多新的功能。

### 变量

~~~less
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header { color: @light-blue; }
~~~

输出：

~~~less
#header { color: #6c94be; }
~~~

甚至可以用变量名定义为变量：

~~~less
@fnord: "I am fnord.";
@var: 'fnord';
content: @@var;
~~~

解析后：

~~~less
content: "I am fnord.";
~~~

Less的变量为完全的常量，只能定义一次。

***

###混合

在LESS中我们可以定义一些通用的属性集为一个class，然后在另一个class中调用这些属性，如有下面这个class：

~~~less
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
~~~

如果想在其他class中引入那些通用的属性集，只需要这样：

~~~less
#menu a {
  color: #111;
  .bordered;
}
.post a {
  color: red;
  .bordered;
}
~~~

`.bordered` class 里面的属性样式都会在`#menu a` 和 ` .post a`中体现出来，等效于：

~~~less
#menu a {
  color: #111;
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
.post a {
  color: red;
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
~~~

***

### 带参数混合

在LESS中，还可以像函数一样定义一个带参数的属性集合：

~~~less
.border-radius (@radius: 5px) {
  border-radius: @radius;
  -moz-border-radius: @radius;
  -webkit-border-radius: @radius;
}
~~~

然后可以在其他class中这样调用它：

~~~less
#header {
  .border-radius;
}
.button {
  .border-radius(6px);  
}
~~~

第一个radius的值是5px，第二个radius的值是6px。

此外，也可以定义不带参数属性集合,如果想隐藏这个属性集合，不让它暴露到CSS中去，但是还想在其他的属性集合中引用，可以使用如下方法:

~~~less
.wrap () {
  text-wrap: wrap;
  white-space: pre-wrap;
  white-space: -moz-pre-wrap;
  word-wrap: break-word;
}

pre { .wrap }
~~~

输出：

~~~less
pre {
  text-wrap: wrap;
  white-space: pre-wrap;
  white-space: -moz-pre-wrap;
  word-wrap: break-word;
}
~~~

***

###模式匹配和导引表达式

有些情况下，可以根据传入参数来改变混合的默认呈现，比如下面：

~~~less
.mixin (@s, @color) { ... }

.class {
  .mixin(@switch, #888);
}
~~~

如果想让`.mixin`根据不同的`@switch`值而表现各异，可以如下设置：

~~~less
.mixin (dark, @color) {
  color: darken(@color, 10%);
}
.mixin (light, @color) {
  color: lighten(@color, 10%);
}
.mixin (@_, @color) {
  display: block;
}
~~~

现在，如果运行：

~~~less
@switch: light;

.class {
  .mixin(@switch, #888);
}
~~~

就会得到下列CSS：

~~~less
.class {
  color: #a2a2a2;
  display: block;
}
~~~

具体实现：

* 第一个混合定义未匹配，因为首参为dark；
* 第二个混合定义被匹配，因为它只接受light；
* 第三个混合定义被匹配，因为它接受任意值。

只有被匹配的混合才会被使用。变量可以匹配任意的传入值，而变量以外的固定值就仅仅匹配与其相等的传入值。

我们也可以匹配多个参数：

~~~less
.mixin (@a) {
  color: @a;
}
.mixin (@a, @b) {
  color: fade(@a, @b);
}
~~~

如果我们只有一个传入参数，那么第一个`.mixin`会被调用，如果有两个传入参数，那么我们会得到第二个`.mixin`。

***

### 导引

当我们想根据表达式进行匹配，而非根据值和参数匹配时，导引就显得非常有用。LESS通过导引混合而非if/else语句来实现条件判断。下例开始介绍：

~~~less
.mixin (@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin (@a) {
  color: @a;
}
~~~

when关键字用以定义一个导引序列（此例只有一个导引）。接下来运行下列代码：

~~~less
.class1 { .mixin(#ddd) }
.class2 { .mixin(#555) }
~~~

就会得到：

~~~less
.class1 {
  background-color: black;
  color: #ddd;
}
.class2 {
  background-color: white;
  color: #555;
}
~~~

导引中可用的全部比较运算有：`> >= = =< <`。此外，关键字true只表示布尔真值，下面两个混合是相同的：

~~~less
.truth (@a) when (@a) { ... }
.truth (@a) when (@a = true) { ... }
~~~

除去关键字true以外的值都被视为布尔假：

~~~less
.class {
  .truth(40); // Will not match any of the above definitions.
}
~~~


导引可以无参数，也可以对参数进行比较运算：

~~~less
@media: mobile;

.mixin (@a) when (@media = mobile) { ... }
.mixin (@a) when (@media = desktop) { ... }

.max (@a, @b) when (@a > @b) { width: @a }
.max (@a, @b) when (@a < @b) { width: @b }
~~~

如果想基于值的类型进行匹配，我们就可以使用`is`函式：

~~~less
.mixin (@a, @b: 0) when (isnumber(@b)) { ... }
.mixin (@a, @b: black) when (iscolor(@b)) { ... }
~~~

下面是常见的检测函式：

* iscolor
* isnumber
* isstring
* iskeyword
* isurl

如果想判断一个值是纯数字还是某个单位量，可以使用下列函式：

* ispixel
* ispercentage 
* isem

在导引序列中可以使用`and`关键字实现**与**条件，使用`，`关键字实现**或**条件，使用`not`关键字实现**非**条件：

~~~less
 .smaller (@a, @b) when (@a > @b) { 
    background-color: black; 
 } 
 .math (@a) when (@a > 10) and (@a < 20) { 
    background-color: red; 
 } 
 .math (@a) when (@a < 10)，(@a > 20) { 
    background-color: blue; 
 } 
 .math (@a) when not (@a = 10)  { 
    background-color: yellow; 
 } 
 .math (@a) when (@a = 10)  { 
    background-color: green; 
 } 

 .testSmall {.smaller(30, 10) } 
 .testMath1 {.math(15)} 
 .testMath2 {.math(7)} 
 .testMath3 {.math(10)}
~~~

输出的CSS：

~~~less
 .testSmall { 
  background-color: black; 
 } 
 .testMath1 { 
  background-color: red; 
  background-color: yellow; 
 } 
 .testMath2 { 
  background-color: blue; 
  background-color: yellow; 
 } 
 .testMath3 { 
  background-color: green; 
 }
~~~

***

### 嵌套规则

LESS可以以嵌套的方式编写层叠样式，如下段CSS：

~~~css
#header { color: black; }
#header .navigation {
  font-size: 12px;
}
#header .logo { 
  width: 300px; 
}
#header .logo:hover {
  text-decoration: none;
}
~~~

在LESS中可以这样写：

~~~less
#header {
  color: black;

  .navigation {
    font-size: 12px;
  }
  .logo {
    width: 300px;
    &:hover { text-decoration: none }
  }
}
~~~

或者这样写：

~~~less
#header        { color: black;
  .navigation  { font-size: 12px }
  .logo        { width: 300px;
    &:hover    { text-decoration: none }
  }
}
~~~

`&`符号用于写串联选择器，而不是后代选择器。这点对伪类尤其有用，如`:hover`和`:focus`. 例如：

~~~less
.bordered {
  &.float {
    float: left; 
  }
  .top {
    margin: 5px; 
  }
}
~~~

输出CSS则是：

~~~css
.bordered.float {
  float: left;  
}
.bordered .top {
  margin: 5px;
}
~~~

***

### 运算

任何数字、颜色或者变量都可以参与运算，例如：

~~~less
@base: 5%;
@filler: @base * 2;
@other: @base + @filler;

color: #888 / 4;
background-color: @base-color + #111;
height: 100% / 2 + @filler;
~~~

LESS可以分辨颜色和单位，可以允许使用括号，也可以在复合属性中进行运算：

~~~less
@var: 1px + 5;
width: (@var + 5) * 2;
border: (@width * 2) solid black;
~~~

***

### Color 函数

LESS 提供了一系列的颜色运算函数. 颜色会先被转化成 HSL 色彩空间, 然后在通道级别操作:

~~~less
lighten(@color, 10%);     // return a color which is 10% *lighter* than @color
darken(@color, 10%);      // return a color which is 10% *darker* than @color

saturate(@color, 10%);    // return a color 10% *more* saturated than @color
desaturate(@color, 10%);  // return a color 10% *less* saturated than @color

fadein(@color, 10%);      // return a color 10% *less* transparent than @color
fadeout(@color, 10%);     // return a color 10% *more* transparent than @color
fade(@color, 50%);        // return @color with 50% transparency

spin(@color, 10);         // return a color with a 10 degree larger in hue than @color
spin(@color, -10);        // return a color with a 10 degree smaller hue than @color

mix(@color1, @color2);    // return a mix of @color1 and @color2
~~~

用法很简单：

~~~less
@base: #f04615;

.class {
  color: saturate(@base, 5%);
  background-color: lighten(spin(@base, 8), 25%);
}
~~~

***

### Math 函数

LESS提供了一组方便的数学函数用于处理一些数字类型的值：

~~~less
round(1.67); // returns `2`
ceil(2.4);   // returns `3`
floor(2.6);  // returns `2`
percentage(0.5); // returns `50%`
~~~

***

### 命名空间

LESS首先会从本地查找变量或者混合模块，如果没找到的话会去父级作用域中查找，直到找到为止。

~~~less
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}

#footer {
  color: @var; // red  
}
~~~

***

### 字符串插值

变量可以用类似Ruby和PHP的方式嵌入到字符串中，像`@{name}`这样的结构：

~~~less
@base-url: "http://assets.fnord.com";
background-image: url("@{base-url}/images/bg.png");
~~~

***

### JavaScript 表达式

Javascript表达式也可以在`.less`文件中使用，可以通过反引号的方式使用：

~~~less
@var: `"hello".toUpperCase() + '!'`;
~~~

输出：

~~~css
@var: "HELLO!";
~~~

它也可以访问Javascript环境：

~~~less
@height: `document.body.clientHeight`;
~~~

还可以使用color函数将一个Javascript字符串解析成16进制的颜色值：

~~~less
@color: color(`window.colors.baseColor`);
@darkcolor: darken(@color, 10%);
~~~