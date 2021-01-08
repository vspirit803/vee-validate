---
title: 验证
description: 字段级和表单级的验证以及验证行为和错误消息
order: 2
---

# 验证

vee-validate 以简单的方式处理复杂的验证，它支持同步验证和异步验证，并且允许使用内置支持 [yup](https://github.com/jquense/yup) 的验证模式定义字段级或表单级规则。

借助Vue 3，vee-validate 提供了两种进行验证的方式：具有 composition API 的命令式验证方法和具有高阶组件(HOC)和作用域插槽的声明式方法。

本指南将讨论 vee-validate 的所有这些特点。

vee-validate 支持使用 composition API 或高阶组件(HOC)进行验证的两种方式，更着重于后者，因为它相比 composition API 提供了更多便利，并将使构建表单变得更快，更有趣。

vee-validate 导出了以下你将经常用于验证表单的组件：

- 表示单个表单输入的 `Field` 组件。
- 表示表单的 `Form` 组件。

## 字段级验证

你可以使用 `Field` 组件或 `useField` composition API 方法定义验证规则，你的规则可以像接受当前值并返回错误信息的方法一样简单。

这是一个使用 `<Field />` 组件的例子:

```vue
<template>
  <Form v-slot="{ errors }">
    <Field name="field" as="input" :rules="isRequired" />
    {{ errors.field }}
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
    isRequired(value) {
      if (value && value.trim()) {
        return true;
      }

      return 'This is required';
    },
  },
};
</script>
```

### 使用 yup 验证字段

yup 是一个流行、简单而强大的 Javascript 数据验证库，你可以将其与 vee-validate 结合使用，你可以使用 [`yup`](https://github.com/jquense/yup) 为该字段定义验证规则：

```vue
<template>
  <Form v-slot="{ errors }">
    <Field name="password" as="input" type="password" :rules="passwordRules" />
    {{ errors.field }}
  </Form>
</template>

<script>
import { Field, Form } from 'vee-validate';
import * as yup from 'yup';

export default {
  components: {
    Field,
    Form,
  },
  data() {
    return {
      passwordRules: yup.string().required().min(8),
    };
  },
};
</script>
```

更多有关 `Field` 组件的信息，请阅读 [API 手册](/api/field)。

## 表单级验证

vee-validate 支持用验证模式预先在字段上定义所有验证，因此你不必在字段上单独定义他们。如果你要构建大型表单并希望模板保持整洁，表单及验证非常方便。

一个简单的验证模式可以是一个对象，以字段名称为 key，验证方法作为这些 key 的值：

```vue
<template>
  <Form @submit="submit" :validation-schema="simpleSchema" v-slot="{ errors }">
    <Field name="email" as="input" />
    <span>{{ errors.email }}</span>

    <Field name="password" as="input" type="password" />
    <span>{{ errors.password }}</span>

    <button>Submit</button>
  </Form>
</template>

<script>
import { Form, Field } from 'vee-validate';
import * as yup from 'yup';

export default {
  components: {
    Form,
    Field,
  },
  data() {
    const simpleSchema = {
      email(value) {
        // validate email value...
      },
      name(value) {
        // validate name value...
      },
      // If you defined global rules you can also use them
      password: 'required|min:8',
      // ...
    };

    return {
      simpleSchema,
    };
  },
};
</script>
```

### 使用 yup 的验证模式

幸运的是，有种简单的方法，可以使用 `yup` 为你的表单构建验证模式，它允许你创建如下验证对象：

```js
const schema = yup.object().shape({
  email: yup.string().required().email(),
  name: yup.string().required(),
  password: yup.string().required().min(8),
});
```

vee-validate内置对 yup 模式的支持，你可以使用 `validation-schema` 参数将模式传递给  `<Form />` 组件：

```vue
<template>
  <Form @submit="submit" :validation-schema="schema" v-slot="{ errors }">
    <Field name="email" as="input" />
    <span>{{ errors.email }}</span>

    <Field name="password" as="input" type="password" />
    <span>{{ errors.password }}</span>

    <button>Submit</button>
  </Form>
</template>

<script>
import { Form, Field } from 'vee-validate';
import * as yup from 'yup';

export default {
  components: {
    Form,
    Field,
  },
  data() {
    const schema = yup.object().shape({
      email: yup.string().required().email(),
      password: yup.string().required().min(8),
    });

    return {
      schema,
    };
  },
};
</script>
```

更多有关 `Form` 组件的信息，请阅读 [API 手册](/api/form)。

<doc-tip title="yup 模式优化">
  关于使用 `yup` 模式来验证你的表单有一些优化警告，请务必查看 [最佳实践指南](/tutorials/best-practices)
</doc-tip>

## 验证行为

vee-validate 默认在以下情况进行验证：

**字段值变化后**

- `change` 事件 dispatch 或 emit 时
- 外部修改了值（修改 model 或其他方式）

请注意，`input` 事件不会触发验证，因为它会使其过于频繁，你可以在下一节配置适应需求的触发时机。

**验证规则变化后**

- 仅在用户交互之前通过了验证的字段

**字段失去焦点后**

- 字段失去焦点 (`blur` 事件被 emit)

**表单提交后**

- 当表单通过 `<Form />` 组件的 `handleSubmit` 或 `submitForm` 方法提交时

<doc-tip>

这仅与 `<Field />` 和 `<Form />` 组件有关

</doc-tip>

### 自定义验证触发器

默认情况 vee-validate 添加多个事件侦听器到你的字段：

- **input:** 添加 `handleInput` 处理方法以更新 `meta.dirty` 标志并更新字段值。
- **change:** 与 input 事件一样，添加一个 `handleInput` 处理方法，此外还添加一个 `handleChange` 更新字段值并验证。
- **blur:** 添加一个 `handleBlur` 处理方法以更新 `meta.touched` 标志。
- **update:modelValue** 添加一个 `handleChange` 处理方法到发出 `update:modelValue` 事件的组件。

注意，在这些侦听器中，`handleChange` 处理方法是唯一会触发验证的。你可以使用 `configure` 方法来配置处理程序是否应该进行验证：

```js
import { configure } from 'vee-validate';

// 默认值
configure({
  validateOnBlur: true, // 控制 `blur` 事件是否应使用 `handleChange` 处理方法触发验证
  validateOnChange: true, // 控制 `change` 事件是否应使用 `handleChange` 处理方法触发验证
  validateOnInput: false, // 控制 `input` 事件是否应使用 `handleChange` 处理方法触发验证
  validateOnModelUpdate: true, // 控制 `update:modelValue` 事件是否应使用 `handleChange` 处理方法触发验证
});
```

注意，这些选项中任何一个配置为 `false` 都不会删除事件，仅仅控制每个事件是否触发验证。

这可能不够灵活，无法满足你的需求，这就是为何你可以在每个 `Field` 组件上也同样可以配置这些选项：

```vue
<!-- 关闭所有验证事件 -->
<Field name="email" :validateOnBlur="false" :validateOnChange="false" :validateOnInput="false" />
```

另外，如果你需要使用不同的事件或者有特定的需求，可以使用 `<Field />` 组件作用域插槽的 `handleChange` 参数并将其绑定到所需的事件来控制要监听的事件：

```vue
<!-- 监听所有事件，这是默认行为 -->
<Field v-slot="{ field }">
  <input v-bind="field" />
</Field>

<!-- 仅在 change 事件触发时进行验证 -->
<Field v-slot="{ field, handleChange }">
  <input @change="handleChange" :value="field.value" />
</Field>
```

<doc-tip type="danger">

在作用域插槽中使用 `handleChange` 或与 `useField` 一起使用时，请确保传递事件对象或新的值，否则 vee-validate 拿不到新的值。

```js
handleChange(e);

handleChange(newValue);
```

</doc-tip>

`useField()` 组合方法与任何事件无关，它仅在 `value` ref 变化时验证。它为你提供了设置自己的验证体验所需的一切。

除了这些事件以外，带有 `validateOnMount` 参数的 `<Field />` 和 `<Form />` 组件在挂载到页面时也会进行验证：

```vue
<!-- 字段挂载时触发验证 (初次验证) -->
<Field name="name" validate-on-mount />

<!-- 表单挂载时，其中所有字段触发验证 -->
<Form validate-on-mount>
  <Field name="email" />
  <Field name="password" />
</Form>
```

## 展示错误信息

### 使用字段的插槽参数

如果你打算使用 `Field` 组件的作用域插槽，你可以访问作用域插槽参数上的 `errors` 和 `errorMessage` 来渲染信息：

```vue
<Field name="field" :rules="rules" v-slot="{ field, errors, errorMessage }">
  <input v-bind="field" type="text" />
  <span>{{ errors[0] }}</span>
  <!-- Or -->
  <span>{{ errorMessage }}</span>
</Field>
```

如果你的输入带有复杂的标记并且希望所有内容保留其中，这将非常方便，它也允许你使用内置验证来创建输入组件。
### 使用表单的插槽参数

如你所见，`<Form />` 组件使你能访问其作用于插槽参数上的 `errors`，你可以使用他们展示任何 `<Field />` 组件上的任何错误信息：

```vue
<Form v-slot="{ errors }">
  <Field name="field" as="input" :rules="rules" />
  {{ errors.field }}
</Form>
```

如果你愿意，可以通过遍历 `errors` 对象展示所有错误信息。
and if you would like, you could display all error messages for your fields by iterating over the `errors` object:

```vue
<Form v-slot="{ errors }">
  <template v-if="Object.keys(errors).length">
    <p>Please correct the following errors</p>
    <ul>
      <li v-for="(message, field) in errors" :key="field">
        {{ message }}
      </li>
    </ul>
  </template>

  <Field name="name" as="input" :rules="rules" />
  <Field name="email" as="input" :rules="rules" />
  <Field name="password" as="input" :rules="rules" />
</Form>
```

### 使用 ErrorMessage 组件

vee-validate 提供 `<ErrorMessage />` 组件以方便的方式展示错误信息。

```vue
<template>
  <Form>
    <Field name="field" as="input" :rules="rules" />
    <ErrorMessage name="field" />
  </Form>
</template>

<script>
import { Field, Form, ErrorMessage } from 'vee-validate';
import * as yup from 'yup';

export default {
  components: {
    Field,
    Form,
    ErrorMessage,
  },
  data() {
    const rules = yup.string().required();

    return {
      rules,
    };
  },
};
</script>
```

`<ErrorMessage />` 十分灵活，你可以使用作用域插槽来自定义其渲染输出以构建复杂的消息标记，阅读 [ErrorMessage API 手册](/api/error-message) 获取更多信息。

### 自定义字段标签

通常，您的字段会使用带下划线或简写的名称，这在错误消息中显示时不太友好。例如，您可能对字段名称进行了特定编码，因为它们可能是由后端生成的。 理想情况下，您希望避免出现类似以下的消息：

```
The down_p is required
```

而是向用户展示更有意义的内容

```
The down payment is required
```

你可以通过两种方式执行此操作，取决于你使用的验证器(yup 或 [全局验证器](/guide/global-validators))。

#### Custom Labels with Yup

使用 yup 非常简单，你只需要在定义字段级或表单级验证之后，调用 `label()` 方法：

```js
const schema = Yup.object().shape({
  email_addr: Yup.string().email().required().label('Email Address'),
  acc_pazzword: Yup.string().min(5).required().label('Your Password'),
});
```

这是一个在线例子：

<code-sandbox id="vee-validate-v4-custom-field-labels-with-yup-qikju" title="Custom Labels with yup"></code-sandbox>

如果你对如何在全局验证器上执行相同操作感兴趣，请查看 [i18n 指南](/guide/i18n#custom-labels)
