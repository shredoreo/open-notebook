### [vm.$slots](https://cn.vuejs.org/v2/api/#vm-slots)

- **类型**：`{ [name: string]: ?Array<VNode> }`

- **只读**

- **详细**：

  用来访问被[插槽分发](https://cn.vuejs.org/v2/guide/components.html#通过插槽分发内容)的内容。每个[具名插槽](https://cn.vuejs.org/v2/guide/components-slots.html#具名插槽)有其相应的 property (例如：`v-slot:foo` 中的内容将会在 `vm.$slots.foo` 中被找到)。`default` property 包括了所有没有被包含在具名插槽中的节点，或 `v-slot:default` 的内容。

  **注意：**`v-slot:foo` 在 2.6 以上的版本才支持。对于之前的版本，你可以使用[废弃了的语法](https://cn.vuejs.org/v2/guide/components-slots.html#废弃了的语法)。

  在使用[渲染函数](https://cn.vuejs.org/v2/guide/render-function.html)书写一个组件时，访问 `vm.$slots` 最有帮助。

- **示例**：

  ```
  <blog-post>
    <template v-slot:header>
      <h1>About Me</h1>
    </template>
  
    <p>Here's some page content, which will be included in vm.$slots.default, because it's not inside a named slot.</p>
  
    <template v-slot:footer>
      <p>Copyright 2016 Evan You</p>
    </template>
  
    <p>If I have some content down here, it will also be included in vm.$slots.default.</p>.
  </blog-post>
  ```

  ```
  Vue.component('blog-post', {
    render: function (createElement) {
      var header = this.$slots.header
      var body   = this.$slots.default
      var footer = this.$slots.footer
      return createElement('div', [
        createElement('header', header),
        createElement('main', body),
        createElement('footer', footer)
      ])
    }
  })
  ```

- **参考**：

  - [`` 组件](https://cn.vuejs.org/v2/api/#slot)
  - [通过插槽分发内容](https://cn.vuejs.org/v2/guide/components.html#通过插槽分发内容)
  - [渲染函数 - 插槽](https://cn.vuejs.org/v2/guide/render-function.html#插槽)

### [vm.$scopedSlots](https://cn.vuejs.org/v2/api/#vm-scopedSlots)

> 2.1.0 新增

- **类型**：`{ [name: string]: props => Array<VNode> | undefined }`

- **只读**

- **详细**：

  用来访问[作用域插槽](https://cn.vuejs.org/v2/guide/components-slots.html#作用域插槽)。对于包括 `默认 slot` 在内的每一个插槽，该对象都包含一个返回相应 VNode 的函数。

  `vm.$scopedSlots` 在使用[渲染函数](https://cn.vuejs.org/v2/guide/render-function.html)开发一个组件时特别有用。

  **注意**：从 2.6.0 开始，这个 property 有两个变化：

  1. 作用域插槽函数现在保证返回一个 VNode 数组，除非在返回值无效的情况下返回 `undefined`。
  2. 所有的 `$slots` 现在都会作为函数暴露在 `$scopedSlots` 中。如果你在使用渲染函数，不论当前插槽是否带有作用域，我们都推荐始终通过 `$scopedSlots` 访问它们。这不仅仅使得在未来添加作用域变得简单，也可以让你最终轻松迁移到所有插槽都是函数的 Vue 3。

- **参考**：

  - [`` 组件](https://cn.vuejs.org/v2/api/#slot)
  - [作用域插槽](https://cn.vuejs.org/v2/guide/components-slots.html#作用域插槽)
  - [渲染函数 - 插槽](https://cn.vuejs.org/v2/guide/render-function.html#插槽)





### [v-slot](https://cn.vuejs.org/v2/api/#v-slot)

- **缩写**：`#`

- **预期**：可放置在函数参数位置的 JavaScript 表达式 (在[支持的环境下](https://cn.vuejs.org/v2/guide/components-slots.html#解构插槽-Props)可使用解构)。可选，即只需要在为插槽传入 prop 的时候使用。

- **参数**：插槽名 (可选，默认值是 `default`)

- **限用于**

  - `<template>`
  - [组件](https://cn.vuejs.org/v2/guide/components-slots.html#独占默认插槽的缩写语法) (对于一个单独的带 prop 的默认插槽)

- **用法**：

  提供具名插槽或需要接收 prop 的插槽。

## [具名插槽](https://cn.vuejs.org/v2/guide/components-slots.html#具名插槽)

> 自 2.6.0 起有所更新。已废弃的使用 `slot` attribute 的语法在[这里](https://cn.vuejs.org/v2/guide/components-slots.html#废弃了的语法)。

有时我们需要多个插槽。例如对于一个带有如下模板的 `<base-layout>` 组件：

```html
<div class="container">
  <header>
    <!-- 我们希望把页头放这里 -->
  </header>
  <main>
    <!-- 我们希望把主要内容放这里 -->
  </main>
  <footer>
    <!-- 我们希望把页脚放这里 -->
  </footer>
</div>
```

对于这样的情况，`<slot>` 元素有一个特殊的 attribute：`name`。这个 attribute 可以用来定义额外的插槽：

<<<<<<< HEAD
```HTML
=======
```html
>>>>>>> 5169c39aca560725b57a43df36f4315dc2e59a0a
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

- 一个不带 `name` 的 `<slot>` 出口会带有隐含的名字“**default**”。

在向具名插槽提供内容的时候，我们可以在一个 `<template>` 元素上使用 `v-slot` 指令，并以 `v-slot` 的参数的形式提供其名称：

<<<<<<< HEAD
```HTML
=======
```php+HTML
>>>>>>> 5169c39aca560725b57a43df36f4315dc2e59a0a
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

现在 `<template>` 元素中的所有内容都将会被传入相应的插槽。任何没有被包裹在带有 `v-slot` 的 `<template>` 中的内容都会被视为默认插槽的内容。

然而，如果你希望更明确一些，仍然可以在一个 `<template>` 中包裹默认插槽的内容：

<<<<<<< HEAD
```HTML
=======
```html
>>>>>>> 5169c39aca560725b57a43df36f4315dc2e59a0a
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

任何一种写法都会渲染出：

<<<<<<< HEAD
```HTML
=======
```php+HTML
>>>>>>> 5169c39aca560725b57a43df36f4315dc2e59a0a
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

注意 **`v-slot` 只能添加在 `<template>` 上** (只有[一种例外情况](https://cn.vuejs.org/v2/guide/components-slots.html#独占默认插槽的缩写语法))，这一点和已经废弃的 [`slot` attribute](https://cn.vuejs.org/v2/guide/components-slots.html#废弃了的语法) 不同。

说的就是独占默认插槽，当被提供的内容*只有*默认插槽时，组件的标签才可以被当作插槽的模板来使用。



## [作用域插槽](https://cn.vuejs.org/v2/guide/components-slots.html#作用域插槽)

> 自 2.6.0 起有所更新。已废弃的使用 `slot-scope` attribute 的语法在[这里](https://cn.vuejs.org/v2/guide/components-slots.html#废弃了的语法)。

有时让插槽内容能够访问子组件中才有的数据是很有用的。例如，设想一个带有如下模板的 `<current-user>` 组件：

```HTML
<span>
  <slot>{{ user.lastName }}</slot>
</span>
```

我们可能想换掉备用内容，用名而非姓来显示。如下：

```HTML
<current-user>
  {{ user.firstName }}
</current-user>
```

然而上述代码不会正常工作，因为只有 `<current-user>` 组件可以访问到 `user` 而我们提供的内容是在父级渲染的。

为了让 `user` 在父级的插槽内容中可用，我们可以将 `user` 作为 `<slot>` 元素的一个 attribute 绑定上去：

```HTML
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

绑定在 `<slot>` 元素上的 attribute 被称为**插槽 prop**。现在在父级作用域中，我们可以使用带值的 `v-slot` 来定义我们提供的插槽 prop 的名字：

```HTML
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

在这个例子中，我们选择将包含所有插槽 prop 的对象命名为 `slotProps`，但你也可以使用任意你喜欢的名字。

### [独占默认插槽的缩写语法](https://cn.vuejs.org/v2/guide/components-slots.html#独占默认插槽的缩写语法)

在上述情况下，当被提供的内容*只有*默认插槽时，组件的标签才可以被当作插槽的模板来使用。这样我们就可以把 `v-slot` 直接用在组件上：

```HTML
<current-user v-slot:default="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

这种写法还可以更简单。就像假定未指明的内容对应默认插槽一样，不带参数的 `v-slot` 被假定对应默认插槽：

```HTML
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

注意默认插槽的缩写语法**不能**和具名插槽混用，因为它会导致作用域不明确：

```HTML
<!-- 无效，会导致警告 -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps is NOT available here
  </template>
</current-user>
```

只要出现多个插槽，请始终为*所有的*插槽使用完整的基于 `<template>` 的语法：

```HTML
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
  <!--
    <template #default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
  -->

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</current-user>
```



