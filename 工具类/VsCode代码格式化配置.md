# VsCode代码格式化自动化配置

## 1.代码风格

根据公司代码规范

## 2.代码格式化插件准备

- [`Vuter`](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Doctref.vetur) 提供`vue`代码片段、语法支持、代码高亮等，可以格式化html、标准css（有分号 、大括号的那种）、标准js（有分号 、双引号的那种）、vue文件
- [`ESlint`](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Ddbaeumer.vscode-eslint)检查`javascript`语法检查和代码规范，不包括css，html
- [`Prettier - Code formatter`](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Desbenp.prettier-vscode) 各种代码格式化，只关注格式化，并不具有eslint检查语法等能力，只关心格式化文件(最大长度、混合标签和空格、引用样式等)，包括JavaScript · Flow · TypeScript · CSS · SCSS · Less · JSX · Vue · GraphQL · JSON · Markdown

## 3.自动化配置

### （1）打开配置

- 打开`VS Code`配置文件`setting.json`
- 快捷键`ctrl + shirt + p`，搜索`Settings(JSON)`

![image-20220107141718812](images/image-20220107141718812.png)

### （2）setting配置

```js
{
    /*先配置eslint*/
    "eslint.validate": [
    "javascript",
    "javascriptreact",
    "html",
    "vue"
  ],
  "editor.codeActionsOnSave": { // #每次保存的时候将代码按eslint格式进行修复
    "source.fixAll.eslint": true
  },
  /*配置html css js的默认格式化程序*/  
    "[html]": {
		"editor.defaultFormatter": "esbenp.prettier-vscode"
	},
	"[javascript]": {
		"editor.defaultFormatter": "esbenp.prettier-vscode"
	},
	"[json]": {
		"editor.defaultFormatter": "vscode.json-language-features"
	},
	"[jsonc]": {
		"editor.defaultFormatter": "vscode.json-language-features"
	},
 "editor.defaultFormatter": "esbenp.prettier-vscode",//或者直接全部进行设置
   /*配置编辑器*/
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
   /*配置prettier*/
  "prettier.printWidth": 120, // 超过最大值换行
  "prettier.tabWidth": 4, // 缩进字节数
  "prettier.useTabs": true, // 缩进使用tab，不使用空格
  "prettier.semi": true, // 句尾添加分号
  "prettier.singleQuote": true, // 使用单引号代替双引号
  "prettier.proseWrap": "preserve", // 默认值。因为使用了一些折行敏感型的渲染器（如GitHub comment）而按照markdown文本样式进行折行
  "prettier.arrowParens": "avoid", //  (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号
  "prettier.bracketSpacing": true, // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
  "prettier.endOfLine": "auto", // 结尾是 \n \r \n\r auto
  "prettier.eslintIntegration": false, //不让prettier使用eslint的代码格式进行校验
  "prettier.htmlWhitespaceSensitivity": "ignore",
  "prettier.jsxBracketSameLine": false, // 在jsx中把'>' 是否单独放一行
  "prettier.jsxSingleQuote": false, // 在jsx中使用单引号代替双引号
  "prettier.requireConfig": false, // Require a 'prettierconfig' to format prettier
  "prettier.stylelintIntegration": false, //不让prettier使用stylelint的代码格式进行校验
  "prettier.trailingComma": "es5", // 在对象或数组最后一个元素后面是否加逗号（在ES5中加尾逗号）
  "prettier.tslintIntegration": false, // 不让prettier使用tslint的代码格式进行校验
}
```

### (3)eslint配置

```js
module.exports = {
	root: true,
	env: {
		browser: true,
		commonjs: true,
		node: true,
		es6: true,
	},
	extends: ["plugin:vue/recommended", "eslint:recommended", "prettier"],// 加上preitter，防止冲突，且需要本地安装prettier插件
	plugins: ["vue"],
	parserOptions: {
		parser: "babel-eslint",
		sourceType: "module",
	},
	rules: {/*规则自己配，但是配置的规则要和preitter一致，否则会有冲突*/
		"no-undef": 2, //不可以有未声明的变量
		"no-redeclare": 2, //禁止重复声明变量
		semi: [2, "always"], //语句强制分号结尾
		"no-func-assign": 2, //禁止重复的函数声明
		"spaced-comment": 2, //注释前要有空格
		"block-spacing": "always",
		"lines-between-class-members": "always",
		"key-spacing": ["error", { afterColon: true }], //冒号之后必须有空格
		"semi-spacing": ["error", { before: true, after: true }],
		quotes: ["error", "double", { allowTemplateLiterals: true }], // 允许使用双引号，允许使用模板字符串
	},
};

```



## 4.其他插件

1.`Trailing Spaces` 尾部空格删除插件，高亮空格，并提供了一键删除。

2.`Code Spell Checker`拼写检查

