## Vue API部分记录

### （1）写在main.js里面的

### 1.silent

```js
Vue.config.silent = true //取消Vue的所有的日志与警告
```

#### 2.devtools

```js
Vue.config.devtools = true // 是否允许 vue-devtools 检查代码。开发版本默认为 true，生产版本默认为 false。生产版本设为 true 可以启用检查
```

#### 3.productionTip

```js
Vue.config.productionTip = false;// 设置为 false 以阻止 vue 在启动时生成生产提示。
```

### （2）全局API

#### 1.nextTick

在下次 DOM 更新循环结束之后执行延迟回调,更新DOM后调用

```js
this.$nextTick(()=>{//不需要全局Vue,回调函数中的this也直接绑定当前vue实例
   // xxxx
});
//$nextTick()返回Promise对象，也可以使用async和await完成
```

