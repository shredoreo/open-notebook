# CommonJS

- 每个文件都可以当作一个模块
- 在服务端：模块加载是运行时同步加载的
- 在浏览器端：模块需要提前编译打包处理

## 基本语法

### 暴露

```js
module.exports = value
exports.xx = value
```

暴露的是exports对象

### 引入

- 先引入第三方库

```js
require(xxx)
//第三方模块 xxx为包名
//自定义模块 xxx为模块文件路径
```

## 实现

### server

node.js

### browser

browserify：也称为commonJS的浏览器端打包工具

## 使用

### 浏览器端

1. 创建项目结构

   ```
   |-js
     |-dist //打包生成文件的目录
     |-src //源码所在的目录
       |-module1.js
       |-module2.js
       |-module3.js
       |-app.js //应用主源文件
   |-index.html
   |-package.json
     {
       "name": "browserify-test",
       "version": "1.0.0"
     }
   ```

2. 下载browserify

   - 全局: npm install browserify -g
   - 局部: npm install browserify --save-dev

3. 打包处理js:

   - browserify js/src/app.js -o js/dist/bundle.js 

4. 页面使用引入:

   ```
   <script type="text/javascript" src="js/dist/bundle.js"></script> 
   ```







# require.js使用教程

- require.js
-  `require.config({})`
- ``require( ['x1', 'x2'], function(x1, x2) {})`



1. 下载require.js, 并引入

   - 官网: http://www.requirejs.cn/
   - github : https://github.com/requirejs/requirejs
   - 将require.js导入项目: js/libs/require.js 

2. 创建项目结构

   ```
   |-js
     |-libs
       |-require.js
     |-modules
       |-alerter.js
       |-dataService.js
     |-main.js
   |-index.html
   ```

3. 定义require.js的模块代码

   - dataService.js

     ```
     define(function () {
       let msg = 'atguigu.com'
     
       function getMsg() {
         return msg.toUpperCase()
       }
     
       return {getMsg}
     })
     ```

   - alerter.js

     ```
     //显式声明依赖注入
     define(['dataService', 'jquery'], function (dataService, $) {
       let name = 'Tom2'
     
       function showMsg() {
         $('body').css('background', 'gray')
         alert(dataService.getMsg() + ', ' + name)
       }
     
       return {showMsg}
     })
     ```

4. 应用主(入口)js: main.js

   ```
   (function () {
     //配置
     requirejs.config({
       //基本路径
       baseUrl: "js/",
       //模块标识名与模块路径映射
       paths: {
         "alerter": "modules/alerter",
         "dataService": "modules/dataService",
       }
     })
     
     //引入使用模块
     requirejs( ['alerter'], function(alerter) {
       alerter.showMsg()
     })
   })()
   ```

5. 页面使用模块:

   <script data-main="js/main" src="js/libs/require.js"></script>

------

6. 使用第三方基于require.js的框架(jquery)

   - 将jquery的库文件导入到项目: 

     - js/libs/jquery-1.10.1.js

   - 在main.js中配置jquery路径

     ```
     paths: {
               'jquery': 'libs/jquery-1.10.1'
           }
     ```

   - 在alerter.js中使用jquery

     ```
     define(['dataService', 'jquery'], function (dataService, $) {
         var name = 'xfzhang'
         function showMsg() {
             $('body').css({background : 'red'})
             alert(name + ' '+dataService.getMsg())
         }
         return {showMsg}
     })
     ```

------

7. 使用第三方不基于require.js的框架(angular)
   - 将angular.js导入项目
   - js/libs/angular.js

- 在main.js中配置

   `require.config({})`
  
  `require( ['x1', 'x2'], function(x1, x2) {})`
  
  ```
  (function () {
    require.config({
      //基本路径
      baseUrl: "js/",
      //模块标识名与模块路径映射
      paths: {
        //第三方库
        'jquery' : './libs/jquery-1.10.1',
        'angular' : './libs/angular',
        //自定义模块
        "alerter": "./modules/alerter",
        "dataService": "./modules/dataService"
      },
      /*
       配置不兼容AMD的模块
       exports : 指定与相对应的模块名对应的模块对象
       */
      shim: {
        'angular' : {
          exports : 'angular'
        }
      }
    })
    //引入使用模块
    require( ['alerter', 'angular'], function(alerter, angular) {
      alerter.showMsg()
      console.log(angular);
    })
  })()
  ```





# ES6-Babel-Browserify

- **babel-cli, babel-preset-es2015 和 browserify**
- **`.babelrc`**



1. 定义package.json文件

   ```
   {
     "name" : "es6-babel-browserify",
     "version" : "1.0.0"
   }
   ```

2. 安装**babel-cli, babel-preset-es2015 和 browserify**

   * `npm install babel-cli browserify -g` 
   * `npm install babel-preset-es2015 --save-dev` 
   * preset 预设(将es6转换成es5的所有插件打包)

3. 定义**`.babelrc`**文件   run cotrol 

   ```
   {
     "presets": ["es2015"]
   }
   ```

4. 编码

   * js/src/module1.js  分别暴露

     ```
     export function foo() {
       console.log('module1 foo()');
     }
     export function bar() {
       console.log('module1 bar()');
     }
     export const DATA_ARR = [1, 3, 5, 1]
     ```

   * js/src/module2.js  统一暴露

     ```
     let data = 'module2 data'
     
     function fun1() {
       console.log('module2 fun1() ' + data);
     }
     
     function fun2() {
       console.log('module2 fun2() ' + data);
     }
     
     export {fun1, fun2}
     ```

   * js/src/module3.js

     ```
     export default {
       name: 'Tom',
       setName: function (name) {
         this.name = name
       }
     }
     ```

   * js/src/app.js

     ```js
     //使用分别暴露的模块，引入时需要用结构赋值的形式
     import {foo, bar} from './module1'
     import {DATA_ARR} from './module1'
     import {fun1, fun2} from './module2'
     import person from './module3'
     
     import $ from 'jquery'
     
     $('body').css('background', 'red')
     
     foo()
     bar()
     console.log(DATA_ARR);
     fun1()
     fun2()
     
     person.setName('JACK')
     console.log(person.name);
     ```

5. 编译

   * 1、使用Babel将ES6编译为ES5代码(但包含CommonJS语法) : 
     `babel js/src -d js/lib`
   * 2、使用Browserify编译js : 
     `browserify js/lib/app.js -o js/lib/bundle.js`

6. 页面中引入测试

   ```
   <script type="text/javascript" src="js/lib/bundle.js"></script>
   ```

7. 引入第三方模块(jQuery)
   1). 下载jQuery模块: 

     * npm install jquery@1 --save
       2). 在app.js中引入并使用

     ```
     import $ from 'jquery'
     $('body').css('background', 'red')
     ```