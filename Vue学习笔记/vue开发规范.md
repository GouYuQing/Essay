### （1）vue规范

#### 1.组件名为多个单词

避免和HTML单个单词元素冲突

```
Vue.component('todo-item', {
  // ...
})
export default {
  name: 'TodoItem',
  // ...
}
```

#### 2.data必须是函数

```
// In a .vue file
export default {
  data () {
    return {
      foo: 'bar'
    }
  }
}
```

#### 3.prop尽量详细

```
props: {
  status: String
}
// 更好的做法！
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```

#### 4.v-for必须有key

加key优化性能，更新的时候，把DOM变更降到最低

#### 5.避免v-for和v-if一起用

#### 6.为组件样式设置作用域

**顶级 `App` 组件和布局组件中的样式可以是全局的，但是其它所有组件都应该是有作用域的。**

```
// css使用scope
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>

<!-- 使用 CSS Modules -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>

或者css样式加前缀
```

#### 7.私有属性加下划线

#### 8.把每个组件分开成单个文件

#### 9.组件文件名单词首字母大写或者横线连接

```
components/
|- MyComponent.vue
components/
|- my-component.vue
```

#### 10.和父组件紧密耦合的子组件应该以父组件名字为前缀名

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

#### 11.自闭合组件

```
<!-- 在单文件组件、字符串模板和 JSX 中 -->
<MyComponent/>
<!-- 在 DOM 模板中 -->
<my-component></my-component>
```

#### 12.组件名应该是完整的单词缩写

#### 13.prop应该使用小驼峰式

```
props: {
  greetingText: String
}
<WelcomeMessage greeting-text="hi"/>
```

