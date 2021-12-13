### 1、v-show 与 v-if 有什么区别？

v-if会被销毁重建，v-show不会

v-show元素总是会被渲染，简单地基于 CSS 的 “display” 属性进行切换。

v-if 适用于不需要频繁切换条件的场景时；v-show 适用于非常频繁切换条件