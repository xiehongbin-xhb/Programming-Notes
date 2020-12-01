<!--
 * @Author: your name
 * @Date: 2020-12-01 22:09:39
 * @LastEditTime: 2020-12-01 23:01:40
 * @LastEditors: your name
 * @Description: In User Settings Edit
 * @FilePath: \Programming-Notes\FrontEnd\Vue\Vue-Basis\Vue-Render.md
-->
<!--
 * @Author: xiehongbin
 * @Date: 2020-12-01 22:09:39
 * @LastEditTime: 2020-12-01 22:45:41
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \Programming-Notes\FrontEnd\Vue\Vue-Basis\Vue-Render.md
-->
# Vue 渲染函数
## 基础
```js
render: function(createElement) {
  return createElement(
    tag,// 标签名称
    data, // 传递数据
    children // 子节点数组
  )
}
```
createElement函数会返回VNode，VNode就是一个原生的JS对象，可以描述DOM。
用render方法实现header组件：
```js
Vue.component('header', {
  props:['level','title'],
  render(h) {
    return h(
      'h'+this.level,// 参数1 tagName
      // 参数2 如果
      { attrs: { title: this.title }},// 参数2 
      this.$slots.default// 参数3: 孩子节点数组
    )
  }
})
```

## 虚拟DOM
VNode属性
- tag 标识这个VNode是什么标签
- data
- children 孩子节点

## createElement参数
参数1 String Object Function
一个HTML标签名，数据对象

一个与模板中属性对应的数据对象


