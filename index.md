## TS 学习
安装

按window+x 选中 powershell管理员

运行 set-ExecutionPolicy RemoteSigned

运行 get-ExecutionPolicy

输入npm i-g typescript

创建ts文件，使用tsc ts文件  将ts  ->  js

 

tsc ts文件 -w  启动编译模式

 

前提是得有ts的配置文件 tsconfig.json

tsc 可以使所有ts文件转为js

复制代码
{
  "compilerOptions": {  // 编译器选项
    "module": "CommonJS",     // 指定要使用的模块化的规范 ES、amd、common.js、system标准或其他标准
    "target": "ES6",
    "sourceMap": true，
      "outDir": "./dist"          // 指定编译后文件所在目录
　　"allowJs": false            // 是否对js文件进行编译
　　　"checkJs": false,            // 是否检查js语法
     "noEmitOnError": false       // 报错时不生成编译后的文件

  },
  "exclude": [    // 不包含的文件   有默认值  一般可以不设置
    "node_modules"
  ],
  "include": [   // 指定某些文件进行转换  * 表示任意文件  ** 任意目录
    "./src/**/*"  // 根目录下 src 文件夹下 任意目录下的任意文件
  ]
}
复制代码
 

ts自定义类型

复制代码
// 自定义类型
  type myType = {
    name: string
    age:number
  }

  const obj:myType = {
    name:'123',
    age:58
  }
复制代码
 

使用webpack最基本的配置

在项目文件的目录终端输入npm init -y  初始化，生成package.json

终端安装4个依赖

cnpm i -D webpack webpack-cli typescript ts-loader 

 

1.先配置webpack.config.js文件

复制代码
// 引入包
const path = require('path')

// webpack中所有的配置信息都应该先在module.exports中
module.exports = {

  // 指定入口文件
  entry:"./src/index.ts",

  // 指定打包文件所在目录
  output: {
    
    // 指定打包文件的目录
    path:path.resolve(__dirname,'dist'), // 拼接
    // 打包后的文件名
    filename:"bundle.js"
  },

  // 指定webpack打包时使用的模块
  module:{

    // 指定要加载的规则
    rules:[{

      // test 指定的是规则成效的文件
      test: /\.ts$/,     //利用正则匹配以 .ts结尾的文件
      // 要使用的loader
      use: "ts-loader",   // 通过ts-loader将ts与webpack连接
      // 要排除的文件
      exclude: /node-modules/
    }]
  }
}
复制代码
2.配置package.json文件 需要在scripts对象里面加入"build":"webpack"

复制代码
{
  "name": "typescript",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "dependencies": {
    "typescript": "^4.4.4"
  },
  "devDependencies": {
    "ts-loader": "^9.2.6",
    "typescript": "^4.4.4",
    "webpack": "^5.64.0",
    "webpack-cli": "^4.9.1"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build":"webpack" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
复制代码
打包时在终端输入 npm run build 即可成功打包文件



 

 

 

index.ts编写内容

复制代码
function sum(a:number,b:number) {
  return a+b
}
console.log(sum(5,6))
sum(7,8)
复制代码
 

打包后生成的bundle.js文件内容

(()=>{function n(n,o){return n+o}console.log(n(5,6)),n(7,8)})();
 

 

webpack打包（二）

cnpm i -D html-webpack-plugin 安装这个插件（帮助自动生成html文件并引入资源）

1.在webpack.config.js引入插件require



 

 

 

2.在webpack.config.js中添加plugins配置项（对插件配置）



 

 

 3.使用npm run build 除了会打包ts文件还会自动生成html文件，并且引入了该js文件



 

 

 

webpack的开发服务器

1.安装 cnpm i -D webpack-dev-server

2.在package.json的scripts对象中添加start属性，并且指定开启服务后的浏览器



 

 

 

3.终端输入npm start 自动打开服务器和浏览器

 

webpack引入模块ts

import {b} from './01_test'  // 此时无法引入模块报错
输入npm run build打包时报错，webpack无法得知ts文件

1.此时需要对模块进行设置引用，配置模块信息（webpack.config.js）

2.设置引用模块 resolve，以ts和js结尾的都可作为模块使用



 

 

 3.再次输入npm run build 可以正常打包

 

babel新语法转为旧语法（实现浏览器不兼容问题）

 cnpm i -D @babel/core @babel/preset-env babel-loader core-js 

安装好后再webpack.config.js文件中对module进行修改

复制代码
module:{

    // 指定要加载的规则
    rules:[{

      // test 指定的是规则成效的文件
      test: /\.ts$/,
      // 要使用的loader
      use: [
        // 配置babel
        {
          // 指定加载器
          loader:"babel-loader",
          // 设置babel
          options: {
            // 设置需要兼容的浏览器
            presets:[
              [
                // 指定环境插件
                "@babel/preset-env",
                // 配置信息
                {
                  targets:{
                    "chrome":"58",  // 待兼容的浏览器和对应的版本号
                    "ie":"11",
                  },
                  "corejs":"3",     // 指定corejs的版本
                  "useBuiltIns":"usage", // 使用core-js的方式“usage”表示按需加载 
　　　　　　　　　　　　　　// 通过core-js 将 Promise等对象也可进行转换
                }
              ]
            ]
          }
        },

        "ts-loader"    // 简化配置ts容器
      ],   // 从后往前执行 先将ts转为js   再将js转为babel
      // 要排除的文件
      exclude: /node-modules/
    }]
  },
复制代码
 

此时再进行打包可以发现成功转换兼容，其中Promise转换会产生大量代码。

webpack本身在打包开始就使用了箭头函数，如果要兼容ie需要在配置文件中进行配置

在webpack.config.js里进行添加一个配置环境 environment



 

 此时没有再使用箭头函数



 

 

 

 类是使用

静态属性
　　在class里的属性前加上static

实例属性
　　在class里的定义的属性



 

 

 

可以通过加上readonly将属性设置为只读属性，此时该属性只可读不可以修改



 

 

 

 

 

 

静态方法
　　类下创建的方法名前加上static

实例方法
　　类下创建的方法



 

 

 

构造方法（构造器、构造函数）一个类只可以创建一个构造函数

 

 在创建实例化对象时会调用构造函数constructor函数

采用this.实例属性，赋值，要是class下定义过的实例属性（this表示当前对象）

 

继承（extends）

复制代码
class Pet{

    name:string
    age:number
    constructor(name:string,age:number){
      this.name = name
      this.age = age
      console.log(this);   // 打印当前调用的实例对象
      
    }
    say(){
      console.log(111222);
      
    }
  }
  const pet = new Pet("蔷薇",18)
  console.log(pet);
  
  //  继承
  class Dog extends Pet{

  }
  class Cat extends Pet{

  }

  // 创建实例对象
  const dog = new Dog("11",18)
  console.log(dog);
  
  const cat = new Cat("58",18)
  console.log(cat);
复制代码
可在子类中编写属性和方法  也可以对父类实例方法进行重写

 

super关键字（类方法中supper表示的是父类）
如果在子类中编写了构造函数，此时在子类构造函数中必须对父类构造函数进行调用

复制代码
//  继承
  class Dog extends Pet{
    run(){
      console.log(`${this.name}在跑`);
    }
    t:number
    
    constructor(name:string,age:number,t:number){
      super(name,age) // super调用父类构造方法
      this.t = t
    }
    
  }


// 创建实例对象
  const dog = new Dog("11",18,186)
  console.log(dog);
  console.log(dog.run());
复制代码
 

abstract抽象类（不可用于创建对象，专用于被继承，可以定义抽象方法）


 

 

 

 

抽象方法在父类实例方法中声明abstract可以定义为抽象方法，该方法继承的子类要去重写


 

 

 

 

interface接口（1.用于定义一个类的结构 2.用来定义类应该包含哪些属性和方法）

效果与类型声明类似（接口可以重复声明） 但是属性应该补全
接口中的所有属性不可有实际值
接口只定义对象的结构
实现接口用implements
复制代码
// 接口用来定义一个类结构
  interface myInterface{
    name:string,
    age:number,
    say():void
  }
  
  // 接口定义类
  class myClass implements myInterface{
    name:string
    age:number
    constructor(name:string,age:number){
      this.name = name
      this.age = age
    }
    say(){
      console.log(`${this.name}`);
      
    }
  }
复制代码
 

复制代码
// 接口用来定义属性
  interface myInterface{
    name:string,
    age:number,
    say():void
  }
  const obj1:myInterface = {
    name:'aaaa',
    age:12,
    say(){}
  }
复制代码
 

属性的封装

ts加入了public 、 private（只可在当前类访问）、protected（只能在当前类和当前类的子类中使用）等修饰符

 

泛型（参数数据类型不明确时采用泛型）

.

复制代码
// 泛型
  function t1<T>(a:T): T{  //返回类型为T

    return a
  }

  // 直接调用泛型函数，ts可以自动类型进行推断
  t1(10) 

  // 指定类型为string
  t1<string>("5555")

  // 指定2个泛型
  function t2<T,K>(a:T,b:K):T{
    return a
  }
  t2<string,number>('wwww',123456)

  // 利用接口
  interface inte{
    name:string    // 返回值类型为string
  }

　　// 泛型T必须是inte的实现类
  function t3<T extends inte>(a:T):string{   // 返回值类型要与实现接口的类型一致
    
    return a.name
  }
复制代码
 

TS练习

安装依赖npm i -D less less-loader css-loader style-loader

对webpack.config.js文件进行配置

复制代码
// 配置less文件的处理
    {
      test: /\.less$/,
      // 配置加载器
      use:[
        "style-loader",
        "css-loader",
        "less-loader"
      ]
    }
复制代码
 

安装postcss对css进行兼容性匹配，例如一些浏览器需要前缀等情况

npm i -D postcss postcss-loader postcss-preset-env

配置信息

复制代码
{
      test: /\.less$/,
      // 配置加载器
      use:[
        "style-loader",
        "css-loader",
        // 引入postcss
        {
          loader:"postcss-loader",
          options:{
            postcssOptions:{
              // 指定使用的插件
              plugins:[
                [
                  "postcss-preset-env",
                  // 配置兼容哪些浏览器
                  {
                    browsers:'last 2 version'// 每种浏览器的最新2个版本
                    
                  }
                ]
              ]
            }
          }
        },
        "less-loader",
      ]
    }
复制代码
 

 

bind用法  改变方法的this，此时调用的是方法，this不是指向当前类，由此传入一个this，此时this指向为当前类

　　
