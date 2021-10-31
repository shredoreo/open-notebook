

# 类型

- 字面量,限定变量的类型与值

```
let a: 'male' | 'female';
a = 'male';
console.log(a);

let b:true|false;
b=false;

```

### any

- 声明变量时，若不赋值且不声明类型，那么其类型就是any

### unknown

- 实际上就是类型安全的any
- 不能直接赋值给其他类型，需要特殊处理
  - 类型断言
  - 用typeof判断完再赋值

```typescript
//type any
let b;
b=1;
b="ss";

let str:string;
str="hello"
str=b;//success, any可扰乱类型检查

let u: unknown;
u='hi'
str =u;//error，unknown类型不可赋值给其他类型

//判断类型再赋值
if (typeof u === "string"){
    str = u
}

//类型断言
str = u as string;
str = <string> u;
```

### void

空，无返回值

```ts
let v:void;
v=undefined;
v=null;
```

### never

表示永不返回结果，永远执行不到

```ts
function fn2(){
    throw new Error('报错了');
}

function fn3(){
    while (true){}
}
```

### enum

```ts
enum Gender{
    male,
    female,
}
let person:{age:number, gender:Gender}

person = {age:1, gender:Gender.male}
```



### array

```ts
let arr: string[];
arr = ['1','2']

//泛型数组
let aRR: Array<string>;
aRR = ['1']

```



### 索引签名

```tsx
let propIdxObj: {
    name: string,
    [propName: string]: string | number }
propIdxObj = {name: 'prop', a: '1', b: 11}
```

### &

```tsx
let people:{name:string}& {age:number}
people= {name:'tom', age:11};
```

### 

```
//type alias
type myType = 1|2|3|4|5;
let mt1:myType = 1;
let mt2:myType = 9;//error
```



# 编译选项



```JSON
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es5",
    "sourceMap": true,
    "outDir":"./dist",
    "allowJs": true,
    "checkJs": true,
    //移除注释
    "removeComments": true,
    //出错时不生成编译后的文件
//    "noEmitOnError": true
    //编译后自动开启严格模式，
    //当代码为模块化代码时，（也即带有import、export等）那就会自动进入严格模式，代码开头不会显式使用"use strict";
    "alwaysStrict": true,
    //是否不允许隐式any
    "noImplicitAny": true,
    //不明确类型的this
    "noImplicitThis": true,
    //严格检查null
    "strictNullChecks": true,
    //开启所有的检查
    "strict": true
  },
  "exclude": [
    "node_modules"
  ]
}
```



# webpack

```BASH
npm i -D webpack webpack-cli typescript ts-loader

# 自动将文件引用到html
npm i -D html-webpack-plugin
# 开发编译
npm i -D webpack-dev-server 
# 自动清除上次编译好的文件
npm i -D clean-webpack-plugin
# const {CleanWebpackPlugin} = require('clean-webpack-plugin')

#babel
npm i -D @babel/core @babel/preset-env babel-loader core-js


```



### 配置示例

```json
const path = require('path')
const HtmlWp = require('html-webpack-plugin')
//引用清理打包后文件夹的插件
const {CleanWebpackPlugin} = require('clean-webpack-plugin')

//webpack红的所有配置信息都要写在module.exports中
module.exports = {
  entry: './src/index.ts',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
    environment: {
      //webpack打包时会使用箭头函数
      //告诉webpack不使用箭头
      arrowFunction:false
    }
  },
  //webpack打包时使用的模块
  module: {
    //指定loader规则
    rules: [
      {
        test: /\.ts$/,
        use: [
          {
            loader: 'babel-loader',
            options: {
              //设置预定义的环境
              presets: [
                [
                  //指定环境的插件
                  "@babel/preset-env",
                  //配置信息
                  {
                    //要兼容的目标浏览器
                    targets: {
                      "chrome": "88",
                      "ie":"11"
                    },
                    //指定corejs版本，用于支持高级语法，如promise
                    "corejs": "3",
                    //使用corejs的方式，使用usage表示按需加载
                    "useBuiltIns": "usage"
                  }
                ]
              ]
            }
          },
          'ts-loader'
        ],
        exclude: /node_modules/
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWp({
      template: './src/index.html'
    })
  ],
  //设置引用模块
  resolve: {
    extensions: ['.ts', '.js']
  }
}
```

