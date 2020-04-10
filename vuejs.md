# vue 官网

## 1. 起步

### 1. 声明式渲染

1. 使用模板语法`{{ content }}`
2. 声明式将数据渲染进 DOM 的系统里面：

```js
let app = new Vue({
    el: '#app',
    data: {
        content: 'data',
    },
})
```

3. 但是这个里面其实很大量的工作在后面：
    1. 现在的数据和 dom 已经建立了关联，所有的东西都是响应式。我们在 console 里面修改 app.message,我们会看到网页上面响应的更新。

## 2. vue 实例

### 创建一个 vue 的实例

```js
let vm = new Vue({
    el: '#app',
    data: {
        content: 'data',
    },
})
```

2. 分析： 这个实例传入了一个`选项对象`，里面有些选项可以创建我们想要的行为。
3. new Vue 创建的是一个根 vue 实例，一个 vue 的应用是由一个通过 new Vue 创建的根 Vue 实例，以及可以嵌套复用的组件树组成的。

#### 数据和方法

##### 1. 数据

1. 当一个 vue 的实例被创建之后，他的 data 对象中的所有的属性都被加到了 vue 的响应式系统中。当属性的值发生变化的时候，视图也会产生响应。
2. 所以注意这里所谓的响应式单方向的： data -> view
3. 而且要注意的是： 只有当实例被创建的时候就已经存在于 data 中的属性才是响应式的。
4. 如果针对赋值给 data 的对象使用了`Object.freeze(obj)`, 这意味着响应式将无法追踪变化了，所以注意这里。

##### 2. 还有一些有用的实例属性和方法，都有\$前缀

1. vue 的实例暴露了这些属性和方法，所以是通过 vm 来获取
2. vm.$data, vm.$el, vm.\$watch, 可以查看 api

#### 实例的生命周期钩子

1. 实例的生命周期钩子，里面的 this 上下文都是指调用它的 vue 实例，所以不能使用箭头函数
2. ![avatar](./lifecycle.png)

## 3. 模板语法

1. vuejs 使用了基于 html 的模板语法，允许开发者声明式的将 DOM 绑定至底层 vue 实例的数据。所以所有的 vuejs 的模板都是合法的 html，所以能被遵循规则的浏览器和 html 解析器解析。
2. 在底层中，vue 将模板编译成虚拟的 dom 渲染函数，结合响应系统，vue 能够智能的计算出最少需要重新渲染多少的组件，并且把 dom 操作次数减少到最少。

### 1. 文本

1. 数据绑定最为常见的形式就是使用 mustache 语法，双大括号
   `<span>{{ message }} </span>`
2. 通过使用 v-once 指令，我们也能一次性的插值。
   `<span v-once>{{ message }}</span>`

### 原始的 html

1. 双大括号会将数据解释为普通的文本，而不是 html 代码，我们需要使用 v-html directive:
   ` <p>{{rawHTML}}</p>``<p v-html="rawHTML"></p> `
2. 但是我们需要注意的是直接作为 html，会忽略解析属性值中的数据绑定。也就是说如果数据中有响应的数据绑定，通过 v-html 传入是没有效果的。
3. 注意的是： 虽然有`v-html`这个属性，但是我们不建议使用这个来复合局部的模板，vue 并不是基于字符串的模板引擎。反而，我们最好使用组件。

### attribute

1. mustache 的方式不能运用在 html 的属性上面，遇到这种情况我们需要使用 v-bind directive。
2. 我们从前面的例子中可以发现，针对 vue directive，我们要使用引号来包含住 data 对象中的属性。
3. 针对布尔值的 attribute（存在就意味着 true），v-bind 工作起来就会略有不同，当值为 null, undefined, false 的时候，attribute 甚至不会有这个属性。感觉也能理解，毕竟存在就以为着 true，但是要注意至于这三个值。

### 使用 js 表达式

1. 针对所有的数据绑定，vuejs 都提供了完全的 js 表达式支持。
2. 针对 mustache 方式和属性的方式都可以的哦

```js
{{ number }}
<span v-bind:id="'list-' + id">
```

3. 这些表达式会在所属的 vue 的实例的数据作用域下作为 js 被解析。
4. **notice**: 要求必须是单个表达式，而且这些个模板表达式都被放在沙盒中，我们只能访问全局变量的一个白名单，比如 Math 或者 Date。我们绝对不能在模板表达式中试图访问用户定义的全局变量。

### 指令

1. directive： 带有 v-前缀的特殊 attribute。
2. 这个 attribute 的值预期是一个单个 js 表达式。当然 v-for 是个例外。

#### 参数

1. v-if=“seen”： 跟了一个值，值变化的时候，响应式的影响作用域 dom
2. 但是有些指令是可以接受参数的。比如`v-bind:id="idNumber"`
3. 冒号后面是是参数名称，比如上面的 v-bind 用来响应式的更新 html 的 attribute
4. 还有 v-on 参数

#### 动态参数

1.  前面的例子中 v-bind：id 中的参数是固定的，但是从 vue2.6 开始，我们可以通过 v-bind:[attributename], 得到动态求值参数。
2.  针对动态参数的值的约束： 我们最终预期得到一个字符串，异常的情况下是 null，这个特殊的 null 值可以被显示的用于移除绑定。
3.  针对动态参数表达式的约束： 在 html attribute 里面使用空格和引号是无效的，所以我们不能使用空格和引号，所以我们最好不使用或者使用计算属性来代替这种复杂的表达式。在 dom 中使用模板的时候，要避免使用大写符号来命名，因为浏览器器会强制小写的。

```html
<a v-bind:['list' + idNumber]="value">link</a>
<!-- 不可以因为这有引号了 -->
<a v-bind:[someAttribute]="value">link</a>
<!-- 不可以因为这里的property有大写，除非我们的有someattribute属性 -->
```

#### 修饰符

1. modifier： 修饰符是半角句号来指明后面的后缀，比如 v-on:submit.prevent, 告诉我们出发了事情调用 event.preventDefault()

### 缩写

1. v-bind: :
2. v-on: @
