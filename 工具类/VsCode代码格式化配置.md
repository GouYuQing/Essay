

# 配置VSCode满足代码规范

## 1.插件准备

### （1）至少安装以下插件

- [`Vuter`](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Doctref.vetur) 提供`vue`代码片段、语法支持、代码高亮等，可以格式化html、标准css（有分号 、大括号的那种）、标准js（有分号 、双引号的那种）、vue文件
- [`ESlint`](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Ddbaeumer.vscode-eslint)检查`javascript`语法检查和代码规范，不包括css，html

### （2）推荐安装以下插件，提高开发效率

- [`Prettier - Code formatter`](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Desbenp.prettier-vscode) 各种代码格式化，只关注格式化，并不具有eslint检查语法等能力，只关心格式化文件(最大长度、混合标签和空格、引用样式等)，包括JavaScript · Flow · TypeScript · CSS · SCSS · Less · JSX · Vue · GraphQL · JSON · Markdown

## 2.VSCode自动化配置

通过eslint配置代码校验+本地配置prettier插件进行整个项目的格式化

### （1）配置eslint

ESlint插件默认只提供代码风格检测功能，不能开启代码格式化功能，我们需要配置打开该功能，方便我们在开发时帮助我们格式化代码。

安装插件ESlint，打开扩展，搜索`Eslint`并安装

![image-20220112095856397](images/image-20220112095856397.png)

点击`文件->首选项->设置`，搜索`eslint`，勾选`Format:Enable`选项

![image-20220112094513773](images/image-20220112094513773.png)

也可以打开`VS Code`配置文件`setting.json`，快捷键`ctrl + shirt + p`，搜索`Settings(JSON)`进入`settings.json`进行配置

![image-20220107141718812](images/image-20220107141718812.png)

添加如下选项

```js
 /*先配置eslint*/
  "editor.codeActionsOnSave": { // 每次保存的时候将代码按eslint格式进行修复
    "source.fixAll.eslint": true
  },
```

如果要使用使用`eslint`格式化`vue`代码，需要先安装`eslint和eslint-plugin-vue`,如果有相关依赖就不用安装了

```js
npm install eslint eslint-plugin-vue --save-dev
eslint --init   
```

配置`.eslintrc.js`文件

```js
/*.eslintrc.js*/
module.exports = {
	root: true, //默认是底层文件，不再向上（父文件）搜索
	env: {//运行环境
		browser: true,
		commonjs: true,
		node: true,
		es6: true,
	},
	extends: ["plugin:vue/recommended", "eslint:recommended"],//规则继承，引入eslint-plugin-vue插件，并开启essential类别中的一系列规则，eslint:recommended 启用一些列核心规则
	plugins: ["vue"],//插件【属性值可以省略包名的前缀eslint-plugin-】 eslint-plugin-vue帮助检测.vue里面的template和script代码
	parserOptions: {
		parser: "babel-eslint",//解析器
		sourceType: "module",//解析器配置文件，指定js代码来源，比如script标签引入
	},
    globals: {//全局变量
		document,
		navigator,
		window,
	},
	rules: {/*根据规范选择想要的规则，可官网查询相应规则https://eslint.org/docs/rules/*/
		indent: [2, "tab"],
		"vue/script-indent": [2, "tab", { baseIndent: 1 }],
		"vue/html-indent": [2, "tab", { baseIndent: 1 }],
		"no-undef": 2, //不可以有未声明的变量
		"no-redeclare": 2, //禁止重复声明变量
		semi: [2, "always"], //语句强制分号结尾
		"no-func-assign": 2, //禁止重复的函数声明
		// "spaced-comment": 2, //注释前要有空格
		"arrow-spacing": [2, { before: true, after: true }], //箭头前后括号
		"block-spacing": [2, "always"], // 块级作用域缩进
		"brace-style": [2, "1tbs", { allowSingleLine: true }], // 大括号风格，允许写在一行
		"comma-spacing": [2, { before: false, after: true }], // 逗号前后的空格
		"comma-style": [2, "last"], // 逗号风格，换行时在行首还是行尾
		"eol-last": 2, // 文件以单一的换行符结束
		eqeqeq: [2, "allow-null"], // 必须使用全等
		"generator-star-spacing": [2, { before: true, after: true }], // generate函数的前后空格
		indent: [2, 2, { SwitchCase: 1 }], // 缩进风格，switch缩进风格
		"jsx-quotes": [2, "prefer-single"], // jsx使用单引号
		"key-spacing": [2, { beforeColon: false, afterColon: true }], // 对象字面量中冒号添加后空格
		"keyword-spacing": [2, { before: true, after: true }], // 关键字前后空格
		"new-parens": 2, // new时必须加小括号
		"no-cond-assign": 2, // 禁止在条件表达式中使用赋值语句
		"no-dupe-args": 2, //函数参数不能重复
		"no-dupe-class-members": 2, //对象成员不能重复
		"no-duplicate-case": 2, //switch中的case标签不能重复
		"no-empty-pattern": 2, // 禁止空解构
		"no-eval": 2, //禁止使用eval
		"no-extra-parens": [2, "functions"], //禁止非必要的括号
		"no-func-assign": 2, //禁止重复的函数声明
		"no-invalid-regexp": 2, //禁止无效的正则表达式
		"no-irregular-whitespace": 2, //不能有不规则的空格
		"no-mixed-spaces-and-tabs": 2, //禁止混用tab和空格
		"no-multi-spaces": [2, { ignoreEOLComments: true }], //不能用多余的空格
		"no-multiple-empty-lines": [2, { max: 1 }], //空行最多不能超过2行
		"no-redeclare": 2, //禁止重复声明变量
		"no-regex-spaces": 2, //禁止在正则表达式字面量中使用多个空格
		"no-trailing-spaces": 2, //一行结束后面不要有空格
		"no-undef": 2, //不能有未定义的变量
		"no-whitespace-before-property": 2, // 对象键之前无空格
		"operator-linebreak": [2, "after", { overrides: { "?": "before", ":": "before" } }], //换行时运算符在行尾还是行首
		"padded-blocks": [2, "never"], //块语句内行首行尾不能空行
		"semi-spacing": [2, { before: false, after: true }], //分号前后空格
		"space-before-blocks": [2, "always"], //不以新行开始的块{前面需要有空格
		// "space-before-function-paren": [2, "always"], //函数定义时括号前面需要有空格
		"space-in-parens": [2, "never"], //小括号里面不需要有空格
		"space-infix-ops": 2, //中缀操作符周围需要有空格
		"space-unary-ops": [2, { words: true, nonwords: false }], //一元运算符的前/后要不要加空格
		"spaced-comment": [
			2,
			"always",
			{ markers: ["global", "globals", "eslint", "eslint-disable", "*package", "!", ","] },
		], //注释风格需要有空格
		"template-curly-spacing": [2, "never"], //模板中{}包裹的变量不需要空格
		"wrap-iife": [2, "any"], //立即执行函数表达式的小括号风格任意一种都可以
		"yield-star-spacing": [2, "both"], // generate 函数 yeild风格
		// "prefer-const": 2, //优先使用const
		"object-curly-spacing": [2, "always", { objectsInObjects: false }], //大括号内是否允许不必要的空格
	},
};
```

```js
//.eslintignore eslint忽略文件和git忽略文件同理 需要忽略项目里面不想走eslint的地方
src/libs
node_modules
build/*.js
src/assets
src/utils/
public
dist
.eslintrc.js
```

配置完成之后直接`ctrl+s`可以进行代码格式化，出错的地方会红色波浪线提示，或者`ctrl+shift+f`也可以进行代码格式化，但是运行项目时错误会造成项目跑不起来，如果只想红色波浪线提示错误，可以在`vue.config.js`里面加上

```js
lintOnSave: false,//设置是否在开发环境下每次保存代码时都启用 eslint验证
```

### （2）推荐安装[Prettier]

pretter没有对代码的质量进行检查的能力，只关注格式化，并不具有eslint检查语法等能力，其只会对代码风格按照指定的规范进行统一，避免一个项目中出现多种不同的代码风格

安装插件ESlint，打开扩展，搜索`prettier`，找到`Prettier-Code formatter`并安装

![image-20220112100258310](images/image-20220112100258310.png)

`文件->首选项->设置`进入设置界面，可以在搜索框中搜索要配置的文件进行勾选，但是只有基本配置项

![](images/image-20220112101847802.png)



`settings.json`配置如下，配置时候注意，**Eslint和Prettier配置的规则要一致，否则会有冲突**

```js
{        
  /*配置html css js的默认格式化程序*/  
 "editor.defaultFormatter": "esbenp.prettier-vscode",
  /*配置prettier*/
  "prettier.printWidth": 120, // 超过最大值换行
  "prettier.tabWidth": 4, // 缩进字节数
  "prettier.useTabs": true, // 缩进使用tab，不使用空格
  "prettier.semi": true, // 句尾添加分号
  "prettier.singleQuote": true, // 使用单引号代替双引号
  "prettier.proseWrap": "preserve", // 默认值。因为使用了一些折行敏感型的渲染器（如GitHub comment）而按照markdown文本样式进行折行
  "prettier.arrowParens": "avoid", //  (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号
  "prettier.bracketSpacing": true, // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
  "prettier.eslintIntegration": false, //不让prettier使用eslint的代码格式进行校验
  "prettier.jsxBracketSameLine": false, // 在jsx中把'>' 是否单独放一行
  "prettier.jsxSingleQuote": false, // 在jsx中使用单引号代替双引号
  "prettier.requireConfig": false, // Require a 'prettierconfig' to format prettier
  "prettier.stylelintIntegration": false, //不让prettier使用stylelint的代码格式进行校验
  "prettier.trailingComma": "es5", // 在对象或数组最后一个元素后面是否加逗号（在ES5中加尾逗号）
  "prettier.tslintIntegration": false, // 不让prettier使用tslint的代码格式进行校验
   }
```

### （3）其他配置

可以根据自己的喜好配置，本地配置的文件不会影响到项目代码

```js
{
  /*配置编辑器 看个人习惯配置*/
  "editor.formatOnPaste": true,// 粘贴时格式化代码
  "editor.formatOnSave": true,// 每次保存的时候将代码按格式进行修复
  "editor.detectIndentation": false, // vscode默认启用了根据文件类型自动设置tabsize的选项
  "editor.tabSize": 2, // 重新设定tabsize,根据自己的习惯
  "editor.detectIndentation": false,
  "editor.insertSpaces": false,//tab时不插入空格
  "editor.formatOnType": true //开启自动格式化
    
   /*配置js*/
  "javascript.format.insertSpaceBeforeFunctionParenthesis": true,//  #让函数(名)和后面的括号之间加个空格
  "javascript.format.insertSpaceAfterConstructor": true,// 在构造函数和括号之间加空格
  "javascript.format.insertSpaceAfterOpeningAndBeforeClosingJsxExpressionBraces": true,
  "javascript.format.insertSpaceAfterOpeningAndBeforeClosingNonemptyParenthesis": true,
  "javascript.format.insertSpaceAfterOpeningAndBeforeClosingTemplateStringBraces": true,
  "javascript.format.insertSpaceAfterSemicolonInForStatements": true,
  "javascript.format.insertSpaceBeforeAndAfterBinaryOperators": true,// 在二进制运算符之后定义空间处理。
           
  /*css的一些校验 可加可不加*/
  "css.lint.float": "warning",
  "css.lint.idSelector": "warning",
  "css.lint.important": "warning",
  "css.lint.importStatement": "error",
  "css.lint.universalSelector": "warning",
  "less.lint.float": "warning",
  "scss.lint.idSelector": "warning",
  "scss.lint.hexColorLength": "warning",
  "scss.lint.float": "warning",
  "scss.lint.importStatement": "warning",
  "scss.lint.universalSelector": "warning",
}
```

配置完成之后普通html、css、js都可以格式化，普通文件`ctrl+shift+f`走`prettier`

## 3.其他插件

1.`Trailing Spaces` 尾部空格删除插件，高亮空格，并提供了一键删除。

2.`Code Spell Checker`拼写检查

3.`Color Highlight`颜色高亮

4.`Import Cost`依赖包大小提示

5.`Bracket Pair Colorizer 2`括号对齐，可以改变括号相应的颜色方便找到匹配括号

6.`vscode-icon`文件图标显示，不同文件不同图标



