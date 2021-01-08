---
title: VeeValidate
description: Vue.js的基于模板的验证
home: true
features:
  - title: 🍞 容易
    details: 熟悉且易于设置的声明式验证
  - title: 🧘‍♀️ 灵活
    details: 同步, 异步, 字段级或表单级验证
  - title: ⚡️ 快速
    details: 直观的API和较小的占用空间,更快地构建更快的表单
  - title: 🏏 极简
    details: 仅处理复杂而痛苦的表单问题,赋予你其它问题的全部控制权
  - title: 🍤 精练
    details: 小于5kb的占用空间,让你的应用加载更迅速
  - title: 😎 不依赖特定UI
    details: 可与原生HTML元素或任何你喜欢的UI库协同工作
  - title: 🦾 渐进式
    details: 无论你将Vue.js作为渐进式增强功能还是复杂应用,均可以使用
  - title: ✅ 内建规则
    details: 25条以上规则的规则库,可满足大多数web应用的需求
  - title: 🌐 I18N
    details: 来自世界各地的开发人员提供45种以上语言的内置规则本地化
---

## 快速设置

### 安装

```bash
# install with yarn
yarn add vee-validate@next

# install with npm
npm install vee-validate@next --save
```

或使用CDN

```html
<script src="https://unpkg.com/vee-validate@next"></script>
```

### 使用

注册 `Field` 和 `Form` 组件并创建一个 `required` 验证器：

```js
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
```

使用 `Form` 和 `Field` 组件渲染表单:

```vue
<Form v-slot="{ errors }">
  <Field name="field" as="input" :rules="isRequired" />

  <span>{{ errors.field }}</span>
</Form>
```

更多信息继续阅读 [指南](/guide/overview)。

### 支持本项目

如果你或你的公司正在使用 vee-validate 并且愿意支持其开发，到 [my patreon](https://www.patreon.com/logaretm) 选择你乐意的级别。每一分钱都有助于保持本项目的活跃。
