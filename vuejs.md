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

## 3. 计算属性和侦听器

### 计算属性

1. 模板内的表达式方便，但是最好不要放很多的逻辑到里面，难以维护。
2. 这时候我们就应该使用计算属性。

#### 基础例子

```js
let vm = new Vue({
    el: '#root',
    data: {
        message: 'hello',
    },
    computed: {
        // the getter of the reverseMessage计算属性
        reverseMessage: function () {
            // this refer to vm here
        },
    },
})

// 在html中,我们可以通过
{{ reverseMessage }} or console里面通过vm.reversedMessage来获得
```

3. 注意两点： 1. 计算属性这个我们给了一个他的 getter， 2. 并且里面的 this 指向了 vm 这个 vue 实例本身, 3. 当我们在 console 里面使用 app.reversedMessage 修改值的时候，出现了错误，因为没有 setter，这就解释了我们在注释里面写 getter 的意思。

#### 计算属性缓存和方法

1. 理论上我们其实也是可以使用方法的。虽然在结果上这两种方式其实一致的，但是，不同的是计算属性是基于他们的响应式依赖进行缓存的。（注意这里的响应式依赖的定义： 只有在响应式依赖发生改变的时候，才会重新求值）
2. 也就是计算属性：当响应式的依赖发生了变化的时候，就会重新求值。而方法来说，每当触发重新渲染的时候，调用方法将总会再次执行函数。

### 计算属性和侦听属性

1. watch 是一种 vue 提供的更加通用的方式来观察和响应 vue 实例上的数据变动。

```js
let vm = new Vue({
    el: '#app',
    data: {
        firstName: 'A',
        lastName: 'B',
        fullName: 'A B',
    },
    watch: {
        firstName: function (val) {
            this.fullName = val + this.lastName
        },
        lastName: function (val) {
            this.fullName = this.firstName + val
        },
    },
    computed: {
        full: function () {
            return this.firstName + this.lastName
        },
    },
})
```

2. 我们看到监听属性相比较于计算属性，明显计算属性方便多了的

### 计算属性的 setter

1. 计算属性默认只有 getter，不过在需要的时候你可以提供一个 setter

```js
computed: {
  fullName: {
    get: function() {
      return this.firstName + this.lastName
    },
    set: function(val) {
      let names = val.split(' ')
      this.firstName = names[0]
      this.lastName = names[1]
    }
  }
}
```

### 侦听器

1. 大多数的情况是使用计算属性是非常合适的，但是在： 需要数据变化时执行异步或开销较大的操作时，这个方式是最有用的。
2. 看例子，watch 选项允许我们执行异步操作，并且显示我们执行该操作的频率，并在我们得到最终的结果前，设置了中间的状态，这些都是我们计算属性无法实现的。

## 4. class 和 style 绑定

1. 操作元素的 class 列表和内联样式是数据绑定的最常见的一个需求，vue 针对 class 和 style 属性的绑定专门做了增强，避免字符串繁琐的拼接。

### 绑定 html class

#### 对象语法

1. 给:class 传入一个对象

```html
<div :class="{ active: isActive, 'text-danger': isError}"></div>
```

2. 表示 active 这个 class 取决于 isActive 的真假。
3. **注意**： text-danger 这里我们使用了引号，没有的话会出错
4. 因为当 key 中包含特殊字符如横线“-”（不是下划线）， 空格等 js 不支持的变量时需要用引号。 因为 vue 会将"{}"中的内容应用到 js 中处理！
5. 即使我们在使用样式对象的时候也是一样，我们需要把 text-error 用引号括起来，这个因为就在 js 里面，所以好说
6. 我们可以绑定一个数据对象，或者是计算属性都可以。

#### 数组语法

1. :class 可以传入一个数组

```html
<div :class="[activeClass, errorClass]"></div>
<div :class="[isActive? activeClass : '', errorClass ]"></div>
```

```js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

2. 我们可以在数组中使用三元表达式
3. 针对三元表达式的写法，会比较繁琐,同样的效果我们可以在数组语法中使用对象语法：

```html
<div :class="[{active: isActive}, errorClass ]"></div>
```

4. 解析上面的方式，我们加入一个对象语法在数组语法中的

#### 用在组件上

1. @我们之后再看

### 绑定内联样式

#### 内联样式的对象语法

```html
<div
    :style="{ color: activeColor, fontSize: fontSize + 'px', 'font-Size': fm}"
></div>
<div
    :style="{
  styleObject
}"
></div>
```

```js
data: {
  activeColor: 'yellow',
  fontSize: 30,
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

1. 分析一下： style 的对象语法很直观，想 css，但是针对 css 的属性，我们需要使用驼峰式或者是短横线分隔加引号。

#### 内联样式的数组语法

1. 针对多个对象样式对象应用的同一个元素，使用数组语法
   `<div :style="[baseStyle, overStyle ]">`

#### 自动添加前缀

1. 当时用 v-bind:style 使用的时候，vuejs 会自动侦测并添加响应的前缀，我们不用管啦

#### 多重值，不懂的@@

1. `<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>`
2. 2.3 `style`绑定中的属性提供一个包含多值的数组。

## 5. 条件渲染

### v-if

1. v-if 用于条件性渲染一块内容。注意这里的用词： 是渲染

### <template>元素上使用 v-if 条件渲染分组

1. v-if 必须被添加到一个元素上，所以 template 可以当做一个不可见的包裹元素。

### v-else v-else-if

1. 必须跟在上下元素后面

```html
<div v-if="type === 'A'">
    A
</div>
<div v-if-else="type === 'B'">
    B
</div>
<div v-else>
    not A/b
</div>
```

### 用 key 管理可复用的元素

1. 针对相同的元素我们需要使用唯一值的 key 来区分，这样子 vue 才会重新渲染。可以再看例子官网的例子。

```html
<input placeholder="Enter" />
```

### v-show

1. 用于根据条件展示元素的选项是 v-show 指令
2. 但是 v-show 元素始终会被渲染并保留在 DOM 中，v-show 只是简单的切换元元素的 display 属性。
3. v-show 不支持 template 元素，也不支持 v-else
4. v-show 为 false 的时候，display: none

### v-if vs v-show

1. v-if 是真正的条件渲染，因为他会确保在切换过程中条件块内的事件监听器和子组件适当的被销毁和重建。
2. v-if 也是惰性的
3. v-show 就简单的多，不过初始条件是什么，元素都是会被渲染的。注意可是简单的 css 进行切换的。
4. **note**： 总而言之，v-show 有更高的初始渲染开销，v-if 有更高的切换开销。

### v-if 和 v-for

1. 不推荐一起用，v-for 具有比 v-if 更高的优先级
