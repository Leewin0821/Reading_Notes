##Grunt

###项目和任务的配置

大部分的Grunt Task都依赖于通过 `grunt.initConfig` 方法定义的配置。在如下文中：

~~~javascript
module.exports = function(grunt) {  
  
  // Project configuration.  
  grunt.initConfig({  
    pkg: grunt.file.readJSON('package.json'),  
    uglify: {  
      options: {  
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'  
      },  
      build: {  
        src: 'src/<%= pkg.name %>.js',  
        dest: 'build/<%= pkg.name %>.min.js'  
      }  
    }，
    concat:{
    	awe: {
    		files: [
    			{src: ['src/aa.js', 'src/aaa.js'], dest: 'dest/a.js'},
    			{src: ['src/aa1.js', 'src/aaa1.js'], dest: 'dest/a1.js'},
    		],
    	],
    	some: {
    		files: {
    			{src: ['src/bb.js', 'src/bbb.js'], dest: 'dest/b/', nonull: true},
    			{src: ['src/bb1.js', 'src/bbb1.js'], dest: 'dest/b1/', filter: 'isFile'},
    		},
    	},
    ],
    my_property: 'whatever',
    my_src_files: ['awe/*.js', 'some/*.js'],  
  });  
  
  // Load the plugin that provides the "uglify" task.  
  grunt.loadNpmTasks('grunt-contrib-uglify');  
  
  // Default task(s).  
  grunt.registerTask('default', ['uglify']);  
  
};  
~~~

`grunt.file.readJSON('package.json')` 把存储在`package.json`文件的中的元数据导入到 `GruntFile` 中。因为`<% %>`字符串模板可以在任何引用，所以像文件路径和文件配置这种配置数据可以通过这种方式来避免到处复杂粘贴。

在这里`grunt-contrib-uglify`插件的`uglify` Task定义了一个同名配置，定义了一个banner配置，同时还定义了一个Target叫build，把一个源文件压缩成另一个目标文件。如果运行`grunt uglify:build`，只会运行Target名指定的配置和Task，如果直接运行`grunt uglify`则会顺序执行所有的Target。在一个Task配置里，可以通过`options`属性来覆盖默认配置，Task中的每一个Target都可以有一个与之对应的`options`属性，Target中的`options`配置会覆盖Task中的`options`。`options`是可选的，没有必要可以省略。

上例中采用了文件数组格式将多个源文件在一个Target中映射到目标文件中，而且使用了附加属性：

   * filter: 过滤器，任意一个合法的`fs.stats` 方法名，或者任何一个接受src作为文件路径并返回True或者False的方法都可以。
   * nonull: 非空，如果没有任何一个匹配被找到，那么就返回一个包含这个模式本身的列表。否则返回一个空的列表。这个选项可以结合grunt的`--verbose`参数来帮助调试文件路径问题。

filter属性可以帮助自定义文件细节，最简单的，可以直接使用一个`fs.Stats`方法名。下面这段代码只会匹配真实的文件。

~~~javascript
grunt.initConfig({  
  clean: {  
    foo: {  
      src: ['tmp/**/*'],  
      filter: 'isFile',  
    },  
  },  
});
~~~

或者可以创建自定义的方法，对于应该被匹配的文件返回true，对于不该匹配的文件返回false。比如下面的例子只会匹配到空文件夹。

~~~javascript
grunt.initConfig({  
  clean: {  
    foo: {  
      src: ['tmp/**/*'],  
      filter: function(filepath) {  
        return (grunt.file.isDir(filepath) && require('fs').readdirSync(filepath).length === 0);  
      },  
    },  
  },  
});
~~~

### 加载grunt插件和任务

只要一个插件在`package.json`中定义了依赖关系，并且已经通过`npm install`安装好了，就可以在`GruntFile`中通过

~~~javascript
grunt.loadNpmTasks('grunt-contrib-uglify');
~~~
命令来启用。

### 定制任务

可以通过如下设置一个detault任务来让grunt默认运行uglify任务，在功能上该grunt和grunt uglify或者grunt default在功能上完全等价。

~~~javascript
grunt.registerTask('default', ['uglify']);
~~~

定制项目相关的任务不一定非要在GruntFile中，它们可以被定义在外部任意的js文件中，然后通过grunt.loadTasks来加载任务。

### 动态创建文件

如果想处理大量的单个文件，可以用一些附加属性来帮助动态创建文件。

* expand：设为 true 来启用下面这些属性。
* cwd：所有的 src 都相对于此路径（但是不包含）。
* src：需要匹配的模式，相对于cwd。
* dest：目标文件。
* ext：在dest中的所有文件后缀都替换掉。
* flatten：在dest中的所有路径的片段都替换掉。
* rename：每当匹配到一个src时，都会调用此方法（在ext和flatten执行之后）。dest和src属性会被当参数传入，这个函数必须返回一个新的dest值。如果相同的dest被返回超过一次，每一个用它的src都会被添加到一个源数组。

在下面这个例子中，名为 minify 的任务`static_mapping`和 `dynamic_mapping`执行的完全相同的任务。

~~~javascript
grunt.initConfig({  
  minify: {  
    static_mappings: {  
      // Because these src-dest file mappings are manually specified, every  
      // time a new file is added or removed, the Gruntfile has to be updated.  
      files: [  
        {src: 'lib/a.js', dest: 'build/a.min.js'},  
        {src: 'lib/b.js', dest: 'build/b.min.js'},  
        {src: 'lib/subdir/c.js', dest: 'build/subdir/c.min.js'},  
        {src: 'lib/subdir/d.js', dest: 'build/subdir/d.min.js'},  
      ],  
    },  
    dynamic_mappings: {  
      // Grunt will search for "**/*.js" under "lib/" when the "minify" task  
      // runs and build the appropriate src-dest file mappings then, so you  
      // don't need to update the Gruntfile when files are added or removed.  
      files: [  
        {  
          expand: true,     // Enable dynamic expansion.  
          cwd: 'lib/',      // Src matches are relative to this path.  
          src: ['**/*.js'], // Actual pattern(s) to match.  
          dest: 'build/',   // Destination path prefix.  
          ext: '.min.js',   // Dest filepaths will have this extension.  
        },  
      ],  
    },  
  },  
});
~~~

### 模板

通过 `<% %>` 来定义模板，任务执行时会自动把从配置中读取数据并拓展掉模板。模板会被递归拓展，直到没有任何模板为止。

整个config对象就是使用模板的上下文。并且，Grunt 和 它的方法都可以在模板中使用，比如 `<% grunt.template.today("yyy-mm-dd") %>`。

`<%= xx %>` 以config对象为上下文解析出 xx 的值，类型无关。字符串、数组或者对象都可。
`<% %>` 执行任意的js代码，可以用来进行流程控制或者执行循环等。
下面这个例子是一个concat任务，执行 `grunt concat:sample` 会把所有符合 `foo/*.js+bar/*.js+baz/*.js`的文件连接起来，再加上 `/* abcde */` 的baner，最后生成 `build/abcde.js`文件。

~~~javascript
grunt.initConfig({  
  concat: {  
    sample: {  
      options: {  
        banner: '/* <%= baz %> */\n',   // '/* abcde */\n'  
      },  
      src: ['<%= qux %>', 'baz/*.js'],  // [['foo/*.js', 'bar/*.js'], 'baz/*.js']  
      dest: 'build/<%= baz %>.js',      // 'build/abcde.js'  
    },  
  },  
  // Arbitrary properties used in task configuration templates.  
  foo: 'c',  
  bar: 'b<%= foo %>d', // 'bcd'  
  baz: 'a<%= bar %>e', // 'abcde'  
  qux: ['foo/*.js', 'bar/*.js'],  
}); 
~~~