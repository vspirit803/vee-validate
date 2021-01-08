---
title: 总览
description: VeeValidate 入门
order: 1
---

# 总览

表单验证是前端工作中最难的问题之一，不仅需要确保提交正确的值，还必须确保为用户提供舒适地用户体验，我们还必须处理可访问性，并确保我们的表单具有包容性且适用于各种用户。

手工进行表单验证是一项艰巨的工作，需要大量工作，如果您决定自己构建，则可能无法覆盖您的所有需求。

**与其把时间花在定制表单验证解决方案上，不如花在构建应用程序逻辑上。**

大多数验证库将为你节省很多时间, 但 `vee-validate` 解决了表单验证的主要痛点，并且以非常灵活的方式解决了以下难题：

<div class="features">

- 追踪表单状态
- UI 与 UX
- 同步和异步验证
- 处理提交

</div>

## 入门

vee-validate 主要使用高阶组件(HOC)验证你的表单，在接下来的例子你会看到正在使用的 `Field`, `Form` 以及 `ErrorMessage` 组件，不必担心他们，因为稍后即可在文档中详细了解。

### 使用 script 标签

你可以像下面这样，使用 script 标签，通过 CDN 引入并使用 vee-validate：

```html
<script src="https://unpkg.com/vee-validate@next"></script>
```

这会注入全局 `VeeValidate` 对象, 你将通过该对象访问vee-validate提供的各种组件和方法。

下面是使用 vee-validate 的基础示例：

<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="js,result" data-user="logaretm" data-slug-hash="rNxbMzq" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Basic Example">
  <span>See the Pen <a href="https://codepen.io/logaretm/pen/rNxbMzq">
  Basic Example</a> by Abdelrahman Awad (<a href="https://codepen.io/logaretm">@logaretm</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

<doc-tip>

从现在开始，本文档将假定读者具有 [Vue 单文件组件](https://v3.vuejs.org/guide/single-file-component.html) 的基础知识，并将使用 ES6+ 代码示例。因此，如果你还没有准备好的话，请务必进行学习。

</doc-tip>

### 使用 NPM

对于使用打包器的更现代的工作流，你可以通过包管理器比如 `yarn` 或 `npm` 来安装 vee-validate :

```sh
yarn add vee-validate@next

npm i vee-validate@next --save
```

然后你可以从 vee-validate 导入各种组件：

```vue
<template>
  <Form v-slot="{ errors }">
    <Field name="field" as="input" :rules="isRequired" />

    <span>{{ errors.field }}</span>
  </Form>
</template>

<script>
import { Field, Form } from 'vee-validate';

export default {
  components: {
    Field,
    Form,
  },
  methods: {
    // Validator function
    isRequired(value) {
      return value ? true : 'This field is required';
    },
  },
};
</script>
```

<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
