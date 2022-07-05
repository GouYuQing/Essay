# package.json中的scripts命令解析

## 1.package.json基本概念

### （1）是什么？有什么用？

package.json是用来识别项目并且处理项目的依赖关系的，package.json可以让npm启动项目、运行脚本、安装依赖项，一个项目里面必须要有package.json文件才能用npm安装依赖包

### （2）常见字段

一个项目里面的package.json可以直接使用如下命令生成

```js
//两个命令均可
npm init -y
npm init //然后一直回车
```

```js
{
  //name和version最重要，如果没有模块无法被安装
  "name": "package.json", // 项目名称
  "version": "1.0.0", // 项目版本（格式：大版本.次要版本.小版本）
  "private":true,//是否是私有项目，一般公司的项目是私有的
  "description": "", // 项目描述
  "main": "index.js", // 入口文件
  "scripts": { //指定运行脚本命令的 npm 命令行缩写
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [], //关键词，一个字符串数组，方便别人搜索
  "author": "", //作者
  "license": "ISC" //许可证
  "bugs"://填写一个bug提交地址或者一个邮箱，被你的模块坑到的人可以通过这里吐槽
  	{ "url" : "https://github.com/owner/project/issues"
	, "email" : "project@hostname.com"
	},
  "dependencies":{}//指定了项目运行所依赖的模块,在生产环境中要用到的，比如antd
  "devDependencies":{}//指定了项目开发所需要的模块，开发环境要用的，项目运行的时候又用不到的，比如webpack和eslint
}
```

详细的内容解析如下[package.json详解](https://zoucz.com/blog/2016/02/17/npm-package/)

## 2.scripts解析

```js
{
    "scripts":{
        "start":"nodemon index.ts"，  
    }
}
//执行npm run start 
//相当于执行 nodemon index.ts
```

`package.json`里面的`scripts`字段，可以用于定义脚本命令，供npm直接调用

这些脚本就叫做npm脚本

```js
{
    "scripts":{
        "lint":"node hello.js"，  
        "test":"node test.js"
    }
}
```

`npm run-script lint`相当于`npm run lint`,就会执行`node hello.js`,

`npm run-script test`相当于`npm run test`,就会执行`node test.js`,

`npm run`是`npm run-script`的缩写

### （1）npm原理

每当执行npm run，就会自动新建一个Shell（一般是Bash），在这个Shell里面执行指定的脚本命令，所以只要是Shell可以执行的命令都可以卸载npm脚本里面，也就可以在scripts里面自定义脚本，任何可执行文件也都可以写在里面

npm run新建的这个 Shell，会将当前目录的node_modules/.bin子目录加入PATH变量，执行结束后，再将PATH变量恢复原样

当前目录的node_modules/.bin子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径

```js
"test": "mocha test"
"test": "./node_modules/.bin/mocha test"//不用加路径，可以不这样写
```

### （2）简写的npm脚本

```js
npm start //npm run start
npm stop // npm run stop
npm test //npm run test
npm restart// npm run stop && npm run restart && npm run start
npm run //会直接运行，列出package.json里面的所有的可执行脚本
```

### （3）执行顺序

```js
//并行执行 &
npm run hello.js &npm run world.js
//串行执行,前一个成功才能执行后一个 &&
npm run hello.js && npm run world.js
```

### （4）npm钩子

npm run 为每条命令提供了 pre- 和 post- 两个钩子（hook）。以 npm run test 为例，如果我们的 scripts 字段规定了 pretest 和 posttest，两个钩子的含义分别是预执行、后执行

```js
prepublish:在打包和发布包之前运行，在npm install没有任何参数的本地运行
postpublish:发布包后运行
preinstall:包安装之前运行
postinstall:包安装后运行
preuninstall:在包卸载之前运行
postuninstall:在包卸载之后运行
preversion:在升级包版本之前运行
postversion:在升级包版本之后，提交之后运行
pretest:单元测试之前
posttest：测试之后
prestop:项目停止前
poststop：停止运行后
prestart:启动前
poststart：启动后
prerestart:重启前
postrestart：重启后
```

### （5）变量

通过npm_package_前缀，npm 脚本可以拿到package.json里面的字段

```js
{
  "name": "package.json",
  "version": "1.0.0", 
  "repostitory":{
     "type":"git"
  }
   "script":{
       "view":"echo $npm_package_repository_type"//bash获得打印出来
    }
}
//通过环境变量procss.env对象，拿到package.json的字段值
console.log(process.env.npm_package_name) //packages.json
console.log(process.env.npm_package_version) //1.0.0
console.log(process.env.npm_package_repository_type) // git
```

## 3.参考资料

（1）[package.json指南](http://nodejs.cn/learn/the-package-json-guide)

（2）[package.json详解](https://www.jianshu.com/p/c86d511d99fd)

（3）[npm中文文档scripts部分](https://www.npmjs.cn/misc/scripts/)