##  **Vue+element-ui+node.js前端**

### vue.js

##### vue.js简介

前端三大框架：vue.JS,angular.JS,react.JS

Vue.js是一个轻巧、高性能、可组件化的MVVM库，同时拥有非常容易上手的API；**核心是一个响应的数据绑定系统。**

MVVM定义

MVVM是Model-View-ViewModel的简写。即模型-视图-视图模型。【模型】指的是后端传递的数据。【视图】指的是所看到的页面。【视图模型】mvvm模式的核心，它是连接view和model的桥梁。它有两个方向：一是将【模型】转化成【视图】，即将后端传递的数据转化成所看到的页面。实现的方式是：数据绑定。二是将【视图】转化成【模型】，即将所看到的页面转化成后端的数据。实现的方式是：DOM 事件监听。这两个方向都实现的，我们称之为数据的双向绑定。总结：在MVVM的框架下视图和模型是不能直接通信的。它们通过ViewModel来通信，ViewModel通常要实现一个observer观察者，当数据发生变化，ViewModel能够监听到数据的这种变化，然后通知到对应的视图做自动更新，而当用户操作视图，ViewModel也能监听到视图的变化，然后通知数据做改动，这实际上就实现了数据的双向绑定。并且MVVM中的View 和 ViewModel可以互相通信

#####  vue几种常用的指令 

v-bind绑定一个value属性

v-on指令给当前元素绑定input事件

v-for 、 v-if 、v-bind、v-on、v-show、v-else

v-if 和 v-show 区别 

v-if按照条件是否渲染，v-show是display的block或none

##### axios是什么？怎么使用？描述使用它实现登录功能的流程？

axios 是一个基于 Promise 的 HTTP 客户端，专门为浏览器和 node.js 服务。

Vue 2.0 官方推荐使用 axios 来代替原来的 Vue request，所以这里介绍一下 axios 的功能和基本的使用方法，希望能够对各位所有帮助。

1. 先要引入axios `<script src="../js/axios-0.18.0.js"></script>`,也可以用npm安装

2. example

   1. 执行GET请求

      ```html
      // 为给定 ID 的 user 创建请求
      axios.get('/user?ID=12345')
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });
      
      // 可选地，上面的请求可以这样做
      axios.get('/user', {
          params: {
            ID: 12345
          }
        })
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });
      
      ```

   2. 执行POST请求

      ```html
      axios.post('/user', {
          firstName: 'Fred',
          lastName: 'Flintstone'
        })
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });
      ```

   3. 执行多个并发请求

      ```html
      function getUserAccount() {
        return axios.get('/user/12345');
      }
      
      function getUserPermissions() {
        return axios.get('/user/12345/permissions');
      }
      
      axios.all([getUserAccount(), getUserPermissions()])
        .then(axios.spread(function (acct, perms) {
          // 两个请求现在都执行完成
        }));
      
      ```

### element-ui

##### element-ui 简介

ElementUI是由饿了么团队开发的一套基于VUE2.0的桌面端组件库，ElementUI提供了丰富的组件帮助开发

人员快速构建功能强大、风格统一的页面。

官网地址：http://element-cn.eleme.io/#/zh-CN

在页面上引入 js 和 css 文件即可开始使用，如下：

<img src="/img/elementui.png">

##### Element-ui常用组件

​	Container 布局容器

​	用于布局的容器组件，方便快速搭建页面的基本结构

​	Dropdown 下拉菜单

​	将动作或菜单折叠到下拉菜单中。

​	NavMenu 导航菜单

​	为网站提供导航功能的菜单。

​	Table 表格

​	用于展示多条结构类似的数据，可对数据进行排序、筛选、对比或其他自定义操作。

​	Pagination 分页

​	当数据量过多时，使用分页分解数据。

​	Message 消息提示

​	常用于主动操作后的反馈提示。

​	Tabs 标签页

​	分隔内容上有关联但属于不同类别的数据集合。

​	Form 表单

​	由输入框、选择器、单选框、多选框等控件组成，用以收集、校验、提交数据。在 Form

​	组件中，每一个表单域由一个 Form-Item 组件构成，表单域中可以放置各种类型的表单

​	控件，包括 Input、Select、Checkbox、Radio、Switch、DatePicker、TimePicker

### Node.js

##### Node.js简介

Node 是一个让 JavaScript 运行在服务端的开发平台，它让 JavaScript 成为与PHP、Python、Perl、Ruby 等服务端语言平起平坐的脚本语言。发布于2009年5月，由Ryan Dahl开发，实质是对Chrome V8引擎进行了封装。

Node对一些特殊用例进行优化，提供替代的API，使得V8在非浏览器环境下运行得更好。V8引擎执行Javascript的速度非常快，性能非常好。Node是一个基于Chrome JavaScript运行时建立的平台， 用于方便地搭建响应速度快、易于扩展的网络应用。**Node 使用事件驱动， 非阻塞I/O 模型而得以轻量和高效**，非常适合在分布式设备上运行数据密集型的实时应用。

##### Nodejs 中的模块化

Node 应用由模块组成，采用 CommonJS 模块规范。

I.在 Node 中，模块分为两类:

一类是 Node 提供的模块,称为核心模块；另一类是用户编写的模块，称为文件模块。

• 核心模块部分在 Node 源代码的编译过程中，编译进了二进制执行文件。在 Node 进程启动时，部分核心模块就被直接加载进内存中，所以这部分核心模块引入时，文件定位和编译执行这两个步骤可以省略掉，并且在路径分析中优先判断，所以它的加载速度是最快的。如：HTTP 模块 、URL 模块、Fs 模块都是 nodejs 内置的核心模块，可以直接引入使用。

• 文件模块则是在运行时动态加载，需要完整的路径分析、文件定位、编译执行过程、速度相比核心模块稍微慢一些，但是用的非常多。这些模块需要我们自己定义。接下来我们看一下 nodejs 中的自定义模块。

II.CommonJS（Nodejs）中自定义模块的规定：

• 我们可以把公共的功能抽离成为一个单独的 js 文件作为一个模块，默认情况下面这个模块里面的方法或者属性，外面是没法访问的。如果要让外部可以访问模块里面的方法或者属性，就必须在模块里面通过 exports 或者 module.exports 暴露属性或者方法。

#### 为什么要进行前后端分离？前后端分离的优势在哪里？劣势在哪里？

<img src="/img/nodejs.png">

优点：前端专门负责前端页面和特效的编写，后端专门负责后端业务逻辑的处理，前端追求的是页面美观、页面流畅、页面兼容等。后端追求的是三高（高并发、高可用、高性能）让他们各自负责各自的领域，让专业的人负责处理专业的事情，提高开发效率

缺点：  1 、当接口发生改变的时候，前后端都需要改变

2、 当发生异常的时候，前后端需要联调--联调是非常浪费时间的