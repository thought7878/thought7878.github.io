## Why

### 解决的问题
- 它简化了表单的*状态*管理和*验证*流程
- 使得在 React 应用中处理表单变得非常简单和高效

`react-hook-form` 是一个用于表单处理的 React Hook 库，**它简化了表单的状态管理和验证流程**。`react-hook-form` 提供了一套易于使用的 Hooks，**使得在 React 应用中处理表单变得非常简单和高效**。

### 特点
1. **高性能**：`react-hook-form` 采用了优化策略，避免不必要的渲染，提高了表单处理的性能。
2. **类型安全**：与 TypeScript 紧密集成，提供类型安全的表单处理。
3. **简洁易用**：API 设计简洁，易于理解。
4. **可定制性强**：支持自定义验证逻辑，可以根据具体需求进行扩展。
5. **自动化处理**：自动管理表单状态，如字段值、验证状态等。
6. **社区支持**：拥有活跃的社区和丰富的插件生态系统。

## 使用示例：

首先，你需要安装 `react-hook-form`：

```bash
npm install react-hook-form
```

接下来是一个简单的使用示例：

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';

function ExampleForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label htmlFor="username">Username</label>
      <input
        id="username"
        type="text"
        {...register('username', { required: true, minLength: 5 })}
      />
      {errors.username && <span>This field is required</span>}

      <label htmlFor="email">Email</label>
      <input
        id="email"
        type="email"
        {...register('email', { required: true, pattern: /^\S+@\S+$/i })}
      />
      {errors.email && <span>This field is required</span>}

      <input type="submit" value="Submit" />
    </form>
  );
}
```

### 解释：
1. **`useForm`**: 初始化表单实例，返回一个对象，其中包含 `register`, `handleSubmit`, `reset`, `setError`, `clearErrors`, `watch`, `formState`, 等方法。
2. **`register`**: 用于注册表单中的输入字段。它可以接受一个对象参数，用于*定义验证规则*。
3. **`handleSubmit`**: 用于处理表单提交，当表单被提交时，它会调用提供的回调函数，并将表单数据作为参数传递。
4. **`formState`**: 包含表单的状态信息，如 `errors`（验证错误），`isSubmitting`（提交中），`isSubmitted`（已提交）等。

## useForm

`useForm` 是 `react-hook-form` 中的核心 Hook，用于*初始化表单实例*，*并返回一系列的方法和状态，以便于管理和控制表单的数据/状态和行为*。

### Input 
`useForm` Hook 可以接受一个可选的配置对象作为参数，这个配置对象*包含了用于自定义表单行为的各种选项*。

#### 参数类型

```typescript
export type UseFormProps<
  TFieldValues extends FieldValues = FieldValues,
  TContext extends object = object
> = Partial<{
  mode: Mode
  reValidateMode: Mode
  defaultValues: DeepPartial<TFieldValues>
  resolver: Resolver<TFieldValues, TContext>
  context: TContext
  shouldFocusError: boolean
  shouldUnregister: boolean
  criteriaMode: "firstError" | "all"
}>
```

#### 配置选项详解

1. **`defaultValues`**: 表单字段的初始值。这可以是一个对象，其中的键是字段名，值是字段的初始值。
2. **`mode`**: (`'onSubmit'` | `'onChange'` | `'onBlur'`) 指定何时触发验证。默认值为 `'onChange'`。
   - `'onSubmit'`: 只在表单提交时触发验证。
   - `'onChange'`: 在字段更改时触发验证。
   - `'onBlur'`: 在字段失去焦点时触发验证。
3. **`reValidateMode`**: (`'onChange'` | `'onBlur'`) 指定何时重新验证字段。默认值为 `'onChange'`。
4. **`shouldUnregister`**: (`boolean`) 是否在卸载组件时自动取消注册表单字段。默认值为 `false`。
5. **`shouldFocusError`**: (`boolean`) 是否在验证失败时自动聚焦到第一个错误字段。默认值为 `false`。
6. **`context`**: (`any`) 可以用于传递额外的数据到 `resolver` 函数。
7. **`criteriaMode`**: (`'firstError'` | `'all'`) 指定如何处理验证规则。默认值为 `'firstError'`。
   - `'firstError'`: 验证停止在第一个失败的规则。
   - `'all'`: 所有规则都会被验证，即使有一些失败。
8. **`resolver`**: (`Resolver<TFormValues, any>`) 一个可选的自定义验证函数，用于处理表单验证逻辑。
9. **`values`**: (`TFormValues`) 表单字段的初始值。与 `defaultValues` 类似，但优先级更高。
10. **`delayError`**: (`boolean`) 是否延迟显示验证错误直到用户完成输入。默认值为 `false`。
11. **`debug`**: (`boolean`) 是否开启调试模式。默认值为 `false`。

#### 示例：

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';

function ExampleForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    defaultValues: {
      username: 'JohnDoe',
      email: 'john.doe@example.com',
    },
    mode: 'onSubmit',
    shouldUnregister: true,
    shouldFocusError: true,
    resolver: async (values) => {
      // 自定义验证逻辑
      // 返回 { values, errors }
      return { values, errors: {} };
    },
  });

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username')} />
      {errors.username && <span>This field is required</span>}

      <input {...register('email')} />
      {errors.email && <span>This field is required</span>}

      <button type="submit">Submit</button>
    </form>
  );
}
```

在这个示例中，我们使用了 `defaultValues` 来设置表单字段的初始值，并且配置了 `mode` 为 `'onSubmit'`，这意味着只有在表单提交时才会触发验证。我们还启用了 `shouldUnregister` 和 `shouldFocusError` 选项，以在卸载组件时自动取消注册表单字段，并在验证失败时自动聚焦到第一个错误字段。

通过使用这些配置选项，你可以根据自己的需求定制表单的行为。`react-hook-form` 提供了许多强大的功能，可以帮助你轻松地管理表单的状态和验证。


### Output

`useForm` 返回*一个对象*，该对象包含了一系列的方法和状态，用于处理表单的各种方面。一些关键属性和方法：

1. **`register`**: 用于注册表单中的输入字段，并应用验证规则。
2. **`handleSubmit`**: 用于处理表单提交，当表单被提交时，它会调用提供的回调函数，并将表单数据作为参数传递。
3. **`formState`**: 包含表单的状态信息，如 `errors`（验证错误），`isSubmitting`（提交中），`isSubmitted`（已提交）等。
4. **`reset`**: 重置表单状态。
5. **`watch`**: 监听表单字段的值。
6. **`setError`**: 设置验证错误。
7. **`clearErrors`**: 清除验证错误。
8. **`getValues`**: 获取表单字段的当前值。
9. **`setValue`**: 设置表单字段的值。
10. **`trigger`**: 触发表单验证。
11. **`control`**: 控制表单的状态，通常与 `Controller` 组件一起使用。