# Director.js

director.js：客户端的路由第三方插件
### 1、引子
　　最近学用director.js，那是相当的简单易学易使用。不过开始学的时候，搜搜过后，却没有发现相关的中文教程。于是决定硬啃E文，翻译备用的同时也当是给自己上课并加深对它的理解。
　　director.js 的官方地址请点击 这里 查看，本教程内容也主要来自于这里。
### 2、director.js是什么?
   director.js 按照我的理解就是客户端的路由注册/解析器，它在不刷新页面的情况下，利用“#”符号组织不同的URL路径，并根据不同的URL路径来匹配不同的回调方法。通俗点说就是什么样的路径干什么样的事情。

　　director.js 适用于客户端浏览器以及node.js的服务器端应用，在两者的应用场景里调用方法仅有少量的不同。它非常适合用来开发不需要刷新的单页程序以及node.js应用。尤其单页程序，对于不想介入手机APP开发的同学，可以利用这个实现类似APP的应用程序。

　　director.js 不依赖于任何其他的js库，例如jquery等。但是它又和jquery能很好的融合在一起进行使用。 

　　在本篇主要学习它在客户端的应用，node.js服务器端暂不讨论。
### 3、简单的开始

上文简单提到： director.js 是通过“#”符号进行路径组织的，例如：
```
http://www.demo.com/#/show

http://www.demo.com/#/show/list

http://www.demo.com/#/show/single
```

   路由注册在URL里的体现是用“#”符号来标识路由的开始，再利用"/"分隔符（分隔符可自定义，后面会讲到）来定义路由片段。
   客户端路由其实就是通过URL来区分应用程序的不同状态，并且定义在不同的状态下应该做什么事情。
   当用户访问不同的URL时，director.js 会解析路由信息并告知应用程序需要做什么事情。

　　路由格式具体可参考下图：
　　
　　下面将会就director.js 客户端相关的API进行详细的说明。
　　
### 4、初始化及路由注册

  director.js 的主要对象是Router对象，构造方法如下：
  
   ```
   var router = new Router(routes);
   ```


构造方法中传入的routes参数是一个路由表对象，它是一个具有键值对结构的对象，路由允许多层的嵌套定义。

键值对的键对应URL中传入的路径，一般一个键对应按分隔符切割后的某一部分；
而键值对的值则对应该路径需要触发的回调函数名，可以传入一个或多个函数名，传入多个函数名时请使用数组对象。
一般来说，回调函数要在路由表对象使用前先声明，否则js会报错。

另外，回调函数除非特殊情况，一般不推荐使用匿名函数，请尽量先声明后使用。
```
   var routes = {
          '/dog': bark,    
          '/cat': [meow, scratch]
    };
```
> 上面例子中，对应的URL分别为：#/dog 和 #/cat.
  
 声明Router对象后，需要调用init()方法进行初始化，如下：
  ```
  var routes = {
    '/dog': bark,    
    '/cat': [meow, scratch]
  };

  var router = Router(routes);
  router.init();
  ```
### 5、路由的即时注册
  
 　当我们在开发一些规模比较大的应用的时候，一般做不到一开始就将需要的路径和它对应的回调函数都预先准备好。
 　很多时候，我们都是在做到某一功能时，或者是开发一些独立性比较强耦合度比较低的模块时，才知道我们需要什么样的路径和回调函数。
 　这个时候我们就需要实时注册路由的功能了。 

  director.js 通过“on”方法，提供对即时注册功能的支持，示例如下:
  ```
    var router = Router(routes).init();
    ....
    router.on('/rabbit', function(){
　　...
    })
   ```
### 6、路由事件
    
  路由事件是路由注册表中一个有固定命名的属性，是指当路由方法router.dispatch()被调用时，路由匹配成功的时定义的需要触发的回调方法（允许定义多个回调方法）。
  上文即时注册功能里的"on"方法就是一个事件。具体信息如下：　　

      on ：当路由匹配成功后，需要执行的方法
      before：在触发“on”方法之前执行的方法
      　　
      仅在客户端有效的方法：
      
      after：当离开当前注册路径时，需要执行的方法
      once: 当前注册路径仅执行一次的方法
     
     以下为示例 
      var routes = {
        "/about/:id": {
              before: function (id) { alert("direct to : /Home/About/" + id); },
              on: function (id) { window.location = "/Home/About/" + id; }
      };
      
  *以上示例中使用了匿名函数，仅因为做为示例比较方便，不推荐这种使用方式.
      
### 7、配置参数
 
 director.js 通过配置一些可选项的参数从而提升Router对象的灵活性。而这些参数的设置需要通过router.configure()方法实现。
 ```
 var router = new director.Router(routes).configure(options);
 ```
 具体的配置参数有：

    recurse：控制路由递归触发方式的参数，可选值为"forward","backward"和"false"，
    客户端的默认值是"false"，而服务端的默认值是"backward"
    strict：当值为"false"时，路径允许以"/"结尾（也可以是其他自定义的分隔符）；
    默认值是"true"，说明默认不允许路径以"/"结尾
    async：同步异步控制器，值为"ture","false"，默认值为"false"
    delimiter：路由分隔符，默认值为"/"
    notfound：当路由方法router.dispatch()被调用时，没有匹配到任何路由时触发的方法
    on：当路由方法router.dispatch()被调用时，任何一个路由匹配成功后都需要执行的方法；与上文路由事件中的“on”事件的区别类似于全局和局部的概念，路由表中仅针对当前注册的路由；而configure方法中的"on"则针对全局的所有路由
    before：当路由方法router.dispatch()被调用时，当任何一个路由匹配成功并在"on"执行之前需要执行的方法；与上文路由事件中的 “before” 事件的区别同上
    
　　仅在客户端有效的参数：
```
    resource：用来进行回调函数绑定的基于字符串的对象。
    使用该参数能实现回调函数的延迟绑定（原词是 "late-binding"，后面有相关的详细说明）
    
    after：当给定的路径不再是当前激活的路径时触发的方法，
    可以理解为离开当前路径后触发的方法；
    
    与上文路由事件中的 “after” 事件的区别同上
    
    *以上的配置参数在后面均有详细的讲解
    
      *另外仅在客户端起效的参数还有html5history和run_handler_in_init，这两个参数是与html5相关，暂时也不讨论.
```
###  8、URL匹配
 
   在路由事件那一节的示例里，有这么一个路由表达式"/about/:id"，其中":"后面定义的部分表示实际路径对应的这部分是传入回调函数的参数，例如"#/about/5"中，5就是id参数的值。参数的匹配还可以用嵌套的方式来定义： 
   
   ```
         var router = Router({
            '/about': {
              '/:id': {
                on: function (id) { console.log(id) }
              }
            }
          });
  ```
  在实际应用的过程中，我们的路由可能会变得非常复杂，像"/about/:id"这样简单的表达式并不能满足我们的需求。而director.js 支持利用正则表达式来匹配复杂的路由名称，匹配到的值会作为参数传给回调函数，例如：
  ```
        var router = Router({
        '/hello': {
           '/(\\w+)': {
            on: function (who) { console.log(who) }
          }
        }
      });
   ```   
  当URL传入'#/hello/world'，则回调函数的who=world

　支持更为复杂的多参数的传递：
　```
    　var router = Router({
      '/hello': {
        '/world/?([^\/]*)\/([^\/]*)/?': function (a, b) {
          console.log(a, b);
         }
       }
     });
   ```
     当URL传入'#/hello/world/johny/appleseed'，则回调函数的a=johny,b=applesee
     
     
### 9、路由的递归匹配

  全局配置中的recurse参数决定了路径在路由表中的命中方式以及命中顺序。命中方式包括递归命中和精确命中；命中顺序包括正序，反序和中断。
  当参数设定为“forward、backward”时，表明路由的命中方式是递归命中，并按照规定的顺序命中，forward为正序，backward为反序。路由如果定义了多个路由片段，则它们对应的回调函数均可命中，也就是说一个路径可以命中多个函数。
  当没有特别指定该参数的值时（即默认值"false"），则标明路由的命中方式为精确命中，需要完全匹配整个路径后才可以命中，也就是说一个路径只能命中一个函数。
  　当URL传入"#/dog/angry"时，路由表注册如下：
  　  ```
      　var routes = {
          '/dog': {
            '/angry': {
              on: growl
            },
            on: bark
          }
        };
      ```
    当没有指定递归匹配的方式时，仅命中growl方法。

　　当指定递归匹配参数的值为backward时，首先命中growl方法，然后再命中bark方法，按照路径的注册顺序反序命中。如下：
　　```
　　var router = Router(routes).configure({ recurse: 'backward' });
　　```
　　当指定递归匹配参数的值为forward时，首先命中bark方法，然后再命中growl方法，按照路径的注册顺序正序命中。如下：
　　```
　　var router = Router(routes).configure({ recurse: 'forward' });
　　```
　　当指定了递归匹配参数后，如果命中的函数中使用了"return false;"的语句返回，则会中断递归命中，直接返回。如下： 
　　```
　　var routes = {
      '/dog': {
        '/angry': {
          on: function() { return false; }
        },
        on: bark
      }
    };
  var router = Router(routes).configure({ recurse: 'backward' });
    ```
  
  无中断时，首先会命中angry下的方法，然后命中bark方法；但是在使用return false语句之后，执行完angry命中的方法后将不再递归命中别的方法（bark）。
  
### 10、资源参数

全局配置中的resource参数仅在客户端应用中可用，它是一个文本对象，其中的文本属性值主要用来定义路由匹配命中后的回调方法名。它可以为程序提供更好的封装性，以便更好的进行结构设计。 
```
    var router = Router({
      '/hello': {
        '/usa': 'americas',
        '/china': 'asia'
      }
    }).configure({ resource: container }).init();
      var container = {
        americas: function() { return true; },
        china: function() { return true; }
      };
```    
    示例中，container对象定义在路由对象之后，我觉得这个就是之前说的“延迟绑定”功能。因为路由的回调函数绑定必须遵守先声明后使用的方法，这个示例明显是先使用后声明。但是我测试了一下，这样做会报错，还是不能改变先声明后使用的顺序。因此延迟绑定的意思我还没真正搞清楚。
    
### 11、小结

  director.js 在客户端应用的中文简明教程暂时就是这些，有了这些知识做一个简单的CRUD的DEMO程序是完全没问题了。不过我在做这个DEMO的过程中，觉得还是没有体会到director.js 使用的精髓。因为我在命中后调用了AJAX方法，我的问题是既然已经用了AJAX方法，由何必用director.js 呢？感觉还是得再详细看一下《bootstrap + requireJS+ director+ knockout + web API = 一个时髦的单页程序》这篇文章和它的示例才能体会director.js 的精髓。也感谢该文章作者的分享。
    
 以后有时间再研究和补齐服务端应用的教程，以及在html5应用下的几个配置参数和方法。需要了解更详细信息的同学还是可以去它的 官方地址 啃E文。

```
Over!
```
  
  
　　
　　
