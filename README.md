# PC前端工程

## 1.前端构建

前端构建采用requireJs + gulp形式 （兼容IE8及以下浏览器版本），新项目（不兼容IE8及以下浏览器）统一使用webpack构建。具体构建形式看构建Demo。


## 2.开发规范

### 2.1 文件／资源命名

文件名用英文单词，多个单词采用中划线隔开。

### 2.2 代码变量命名

变量命名用英文单词，多个单词采用驼峰命名。

### 2.3 文本缩进与逗号

一次缩进两个空格，每行代码末尾不能以逗号结尾。

### 2.4 注释

每个模块文件、函数功能都必须有注释。阐述当前模块文件的模块含义、函数功能作用并署上原作者名字，若有改动则署上修改者名字。这里不做模版限制

例子，推荐：
```html
/**   
* @Description 。。。。
* @author Derek
* @date 2017/8/7
* @modifier Tom
*/
```
### 2.5 模块化规范（requireJs，可看构建demo）

#### 2.5.1 文件目录说明
![1.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/1.png)

开发目录如上图所示，主目录下有dist、dist_hash、src、tools文件夹，dist是requireJs的r.js工具大包好的工程文件夹、dist_hash为gulp用MD5计算出hash值并插入文件名进行版本更新、src为代码开发的根目录，tools则存放r.js工具。

在src文件夹下， components文件负责存放局部引用的组件、插件库，css存放全局引用的样式文件，lib存放全局使用的公共类库、插件。modules存放模块文件，所有主体业务都存放在此、services文件存放服务类的公共接口，它主要为modules中模块提供接口，sass为全局样式的预编译代码。

#### 2.5.2 模块定义

模块定义必须用如下方式：
```html
define( factory );
```

推荐采用define(factory)的方式进行模块定义。使用匿名moduleId，从而保证开发中模块与路径相关联，有利于模块的管理与整体迁移。
参数：
模块的factory默认有三个参数，分别是require, exports, module。
```html
define( function( require, exports, module ) {
    // blabla...
});
```


使用AMD推荐风格时，exports和module参数可以省略。
```html
define( function( require ) {
    // blabla...
});
```

开发者 不允许修改require, exports, module参数的形参名称。下面就是错误的用法：
```html
define( function( req, exp, mod ) {
    // blablabla...
});
```

#### 2.5.3 模块声明

所有业务模块都基于配置文件config.js声明的区域模块来引用资源：

![2.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/2.png)

上图中，config声明了components、modules、services三个模块的路径，因此引用这三个模块下面的资源必须基于该路径做相对路径引用，如car.js业务模块引用services、components资源方式如下：

![3.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/3.png)

#### 2.5.4 业务模块的入口文件引用模块代码

为了能够更好配合gulp插件做自动化插入MD5来更新静态资源的情况，业务模块的入口文件引用模块代码的方式以内嵌js代码方式，如car.html： 

![4.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/4.png)


#### 2.5.5 样式引用规范

样式引用有两种方式：
1.	在html文件引用直接以link标签引用样式文件。

```html
<link rel="stylesheet" type="text/css" href="./css/car.css">
```

2.	在js文件引用样式方式如下：

```html
define(function (require) {
    var $ = require('jquery');
    require(‘css!./sweetalert.css');
});
```

通过“css”前缀表示依赖的资源是样式资源，再以“！”与后面“./sweetalert.css”相对路径区分开来，该相对路径是相对当前js文件为基准。


#### 2.5.6 全局引用插件、类库

全局引用插件、类库，即任何业务模块通过require方法声明“类库名称”加载即可，其所需条件第一，类库、插件必须存放在lib文件夹里面；第二，若当前类库没有做兼容amd处理的情况下，必须在config文件下配置shim参数： 

![2.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/2.png)

上图所示，backbone和underscore没有做amd规范的兼容，需要做shim参数配置；backbone通过deps声明依赖关系，以exports接入backbone的全局变量Backbone。

#### 2.5.7 组件、插件定义

组件、插件是为提高用户体验、代码利用率高度封装的模块。每个业务模块可按需加载自己所需组件，以car.html模块为例，我们可以看到sweetalert模块被引用，它所在的模块define声明是包括js逻辑和样式，前面规范有提及到怎么引用，这里在统一说明下关于插件、组件声明的规范：
![5.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/5.png)

上图是sweetalert的模块声明，它把sweetalert依赖的jquery、sweetalert样式和逻辑通过require加载进来，其组件、插件目录如下： 

![6.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/6.png)

index.js是对外声明的模块代码，供外部引用，sweetalert.min.js和sweetalert.css是sweetalert内部实现的逻辑和样式文件。



#### 2.5.8 局部引用插件、类库

为避免所有插件、组件声明在config的shim参数造成维护困难的问题，可采用局部引用方式（每个业务模块自己引用插件、组件），减少config的shim声明的插件的数量。在特定的业务模块引用即可，看car模块的局部引用情况：

![3.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/3.png)

如上图所示，car模块引用sweetalert插件功能，通过

![7.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/7.png)

方式加载了sweetalert插件的功能。

以上的规范可看Demo查看详情。


#### 2.5.9 图片的路径引用

- **业务模块：对于业务模块部分可采用绝对路径和相对路径两种，因为它们在合并之后文件位置没有发生改变:

```html
html页面里，当前目录下imgs文件夹找到img.jpg：

<img src="./imgs/img.jpg" alt="">

css中，当前目录下往上一层级的imgs文件夹找到img.jpg：

background: url('../imgs/img.jpg') no-repeat;

```


- **组件、插件部分：图片路径统一采用绝对路径形式，因打包压缩之后的代码可能不会在原来的路径之下，这样的情况下采用相对路径则会找不到对应的图片资源:

```html
html页面里，在当前访问域名下找到/components/header/images三层级目录下的img.jpg：

<img src="/components/header/images/img.jpg" alt="">

css中，在当前访问域名下找到/components/header/images三层级目录下的img.jpg：

background: url('/components/header/images/img.jpg') no-repeat;

```


### 2.6 文件合并与类库单独打包

文件合并以tools文件夹下build.js文件的modules模块为基准，会根据modules的定义情况进行模块打包。可看下图定义的含义：

![8.png](http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/raw/29219e1143afb0cac06c0678faa90bfecd70c275/screenshot/8.png)

上图所示，代码定义了三个模块，分别是config、car、person模块，config模块会集成config文件的代码和jquery、css、css-builder、normalize、user模块，car与person模块则是按照define的声明执行按需合并的过程。
对于类库单独打包的情况，可统一集成在config文件里面去，若有特别情况可另行集成新的模块。


## 3.组件库


### 3.1 组件库地址

http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/tree/master/components
在该目录下存放组件

### 3.2 在插件库根目录下的文件／资源规范统一如下：
```html
Plugins                          插件文件名    
├── plugin.js                    插件逻辑        
├── plugin.css                   插件样式     
├── readme.md                    插件API使用      
├── fixHistory.md                插件维护历史   
└── dep                          插件的依赖   
    ├── MovieList.js             依赖具体文件 		
    ├── MovieList.style.js       …   	       
    ├── MovieCell.js             …			
    ├── MovieCell.style.js       …		
    ├── MovieView.js             …		
    └── MovieView.style.js       …
```

### 3.3 readme.md的内容必须包含以下三个部分：

（1）插件标题

（2）API使用规则

（3）Demo示例

具体请看readme.md文件

### 3.4 fixHistory.md的内容书写规范如下：

| 日期             | 版本          |   变更记录      |    记录人  |
| --------         | -----:       | :----:         | :----:    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |

## 4.插件库


### 4.1 插件库地址

http://git-ma.paic.com.cn/HUJIEXIONG262/PC_FE_specification/tree/master/plugins
在该目录下存放插件


### 4.2 在插件库根目录下的文件／资源规范统一如下：
```html
component                        组件文件名        
├── component.js                 组件逻辑  
├── style.css                    组件样式  
└── dep                          组件依赖  
    ├── MovieList.component.js   依赖的组件		
    ├── MovieList.style.js       …	
    ├── MovieCell.component.js   …			
    ├── MovieCell.style.js       …		
    ├── MovieView.component.js   …	
    └── MovieView.style.js       …			
```

### 4.3 readme.md的内容必须包含以下三个部分：

（1）插件标题

（2）API使用规则

（3）Demo示例

具体请看readme.md文件

### 4.4 fixHistory.md的内容书写规范如下：

| 日期             | 版本          |   变更记录      |    记录人  |
| --------         | -----:       | :----:         | :----:    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |
| 2017/07/11       |  1.0.0       |   修正某某bug   |    小王    |

## 5. ESLINT 规则

项目引入eslint作为开发规范的检测工具，详细可到官网了解情况：http://eslint.org/。



如果要把eslint作为命令行工具话，需要全局安装eslint:git
```html
npm install -g eslint
```


eslint作为命令行工具，有个特别的好处是：当项目遇到某些文件代码不符合规范可用eslint命令来自动化修复问题。



其使用方式：
```html
eslint --fix [file|dir|glob]
```


详细介绍可到官方文档查看：

http://eslint.org/docs/user-guide/command-line-interface#fix



eslint的规则众多，其中部分规则前面会有“打勾”或“扳手”的图标，分别表示eslint的默认规则和可自动化修复问题，其详情可到rules模块查看：http://eslint.org/docs/rules/



在这里特别地解说10个eslint规则：

### 5.1 缩进
本次的规则是使用tab来缩进，当出现空格情况下会出现报错，可用eslint命令行的“--fix” 选项来修复问题。

不推荐：
```html
/*eslint indent: ["error", "tab"]*/

if (a) {
     b=c;
function foo(d) {
           e=f;
 }
}
```

推荐：
```html
/*eslint indent: ["error", "tab"]*/

if (a) {
/*tab*/b=c;
/*tab*/function foo(d) {
/*tab*//*tab*/e=f;
/*tab*/}
}
```

### 5.2 断行类型
不推荐：
```html

```

推荐：
```html

```

### 5.3 引号
本次项目对引号要求统一使用双引号 “”

不推荐：
```html
/*eslint quotes: ["error", "double"]*/

var single = 'single';
var unescaped = 'a string containing "double" quotes';
```

推荐：
```html
/*eslint quotes: ["error", "double"]*/

var double = "double";
```


### 5.4 分号
本次项目对每条代码行 最后都必须使用分号作为结束 ;

不推荐：
```html
/*eslint semi: ["error", "always"]*/

var name = "ESLint"

object.method = function() {
    // ...
}
```

推荐：
```html
/*eslint semi: "error"*/

var name = "ESLint";

object.method = function() {
    // ...
};
```


### 5.5 多行的字符串
多行字符串连接可以使用如下形式：

```html
var x = "Line 1 \
         Line 2";
```

该特性未被Javascfript授权，后面才正式化，因此我们只能使用+号形式连接起来。

不推荐：
```html
/*eslint no-multi-str: "error"*/
var x = "Line 1 \
         Line 2";
```

推荐：
```html
/*eslint no-multi-str: "error"*/

var x = "Line 1\n" +
        "Line 2";
```


### 5.6 for 循环
为避免开发过程出现低级错误，我们引用它来避免无限循环的问题。

不推荐：
```html
/*eslint for-direction: "error"*/
for (var i = 0; i < 10; i--) {
}

for (var i = 10; i >= 0; i++) {
}

```

推荐：
```html
/*eslint for-direction: "error"*/
for (var i = 0; i < 10; i++) {
}
```


### 5.7 重复键值 （默认）
为避免开发过程出现低级错误，我们引用它来避免重复键值的问题。

不推荐：
```html
/*eslint no-dupe-keys: "error"*/

var foo = {
    bar: "baz",
    bar: "qux"
};
```

推荐：
```html
/*eslint no-dupe-keys: "error"*/

var foo = {
    bar: "baz",
    quxx: "qux"
};
```


### 5.8 重复声明 （默认）
为避免开发过程出现低级错误，我们引用它来避免重复声明的问题。

不推荐：
```html
/*eslint no-redeclare: "error"*/

var a = 3;
var a = 10;
```

推荐：
```html
/*eslint no-redeclare: "error"*/

var a = 3;
// ...
a = 10;
```


### 5.9 重复的参数（默认）
为避免开发过程出现低级错误，我们引用它来避免出现函数出现重复参数的问题。

不推荐：
```html
/*eslint no-dupe-args: "error"*/

function foo(a, b, a) {
    console.log("value of the second a:", a);
}

var bar = function (a, b, a) {
    console.log("value of the second a:", a);
};
```

推荐：
```html
/*eslint no-dupe-args: "error"*/

function foo(a, b, c) {
    console.log(a, b, c);
}

var bar = function (a, b, c) {
    console.log(a, b, c);
};
```


### 5.10 不可达到 （默认）

为避免开发过程出现低级错误，我们引用它来避免代码在return、throw、continue、break关键字后面的问题。

不推荐：
```html
/*eslint no-unreachable: "error"*/

function foo() {
    return true;
    console.log("done");
}

function bar() {
    throw new Error("Oops!");
    console.log("done");
}

while(value) {
    break;
    console.log("done");
}

throw new Error("Oops!");
console.log("done");
```

推荐：
```html
/*eslint no-unreachable: "error"*/

function foo() {
    return bar();
    function bar() {
        return 1;
    }
}

function bar() {
    return x;
    var x;
}

switch (foo) {
    case 1:
        break;
        var x;
}
```


