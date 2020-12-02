<!--
 * @Author: xiehongbin
 * @Date: 2020-12-02 20:44:55
 * @LastEditTime: 2020-12-02 21:22:23
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \Programming-Notes\FrontEnd\Vue\Vue-Basis\Vue专题.md
-->
# Vue 专题笔记
## computed 和 watch
### computed 用法
要点： computed类型，通过Vue实例直接访问计算属性，箭头函数问题，数据缓存问题
1. computed是一个对象
- 类型一： 对象键名是字符串，键值是函数
- 类型二： 对象键名是字符串，键值是一个对象，包含set/get属性。当通过vm.computedName访问时，调用的是get方法，当通过vm.computedName = 1时，访问的是set方法。
2. 计算属性将被混入到Vue实例中，所有的getter和setter的this上下文都自动绑定为Vue实例。
3. 不要使用箭头函数来作为计算属性的键值，否则this将不会指向当前实例，但是当前实例也会作为第一个参数传递给箭头函数，要用时只需要接收即可。
4. 计算属性的结果会被缓存，当依赖的响应式property发生改变时，才会触发重新计算。

### watch用法
要点: watch对象类型问题，回调函数问题，$watch问题
1. watch属性也是一个对象：键名是一个表达式的字符串，键值可以是表示方法的字符串，也可以是函数（接收两个参数：newValue/oldValue），对象（用于配置高级属性：handler，deep，immediate），也可以数组
键值类型一：methods里面的方法名
键值类型二：函数类型，接收2个参数：newValue，oldValue
键值类型三：配置对象的形式 => 该配置对象的属性可以有 handler（回调函数），deep（是否对深层次属性进行监听），immediate（添加监听器之后，是否立即执行回调函数）
键值类型四：数组=> 可以为同一个表达式增加多个回调函数，数组元素的类型可以是上述类型中的任一。
2. 不应该使用箭头函数来作为watch的回调函数，会存在this指向错误问题。
3. `$watch` 可以通过vm.$watch实现监听的功能。
- 接收3个参数：
参数一： 表达式或者函数
参数二： 回调函数（或者对象）
参数三：配置对象 
  - deep属性 => 当监听数组时不需要设置为true 
  - immediate属性 => 不能在immediate属性设置为true时，取消当前属性的监听
```js
vm.$watch(function(){
  return this.a + this.b // 表示只要表达式`this.a + this.b`的值发生改变
  // 对应的回调函数就会被调用
  // 就像监听一个未被定义的计算属性
},callback)
```
- `vm.$watch` 会返回一个取消观察函数，用来停止触发回调函数
`watch`和`$watch`的区别: `$watch`需要手动移除监听，`watch`属性不需要

### comptuted和watch区别
watch需要数据变化时执行异步或者开销较大时使用，当一个属性影响多个属性时
computed 当一个属性受多个属性影响时
## 插槽
## 渲染函数和JSX
## 自定义指令
## 过滤器
