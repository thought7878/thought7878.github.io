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
6. **`criteriaMode`**: (`'firstError'` | `'all'`) 指定如何处理验证规则。默认值为 `'firstError'`。
   - `'firstError'`: 验证停止在第一个失败的规则。
   - `'all'`: 所有规则都会被验证，即使有一些失败。

7. **`values`**: (`TFormValues`) 表单字段的初始值。与 `defaultValues` 类似，但优先级更高。
8. **`delayError`**: (`boolean`) 是否延迟显示验证错误直到用户完成输入。默认值为 `false`。

9. **`context`**: (`any`) 可以用于传递额外的数据到 `resolver` 函数。
10. **`resolver`**: (`Resolver<TFormValues, any>`) 一个可选的自定义验证函数，用于处理表单验证逻辑。

#### 示例

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


### Output

`useForm` 返回*一个对象*，该对象包含了一系列的方法和状态，用于处理表单的各种方面。一些关键属性和方法：

1. **`register`**: (`Register<TFormValues>`) 用于注册表单中的输入字段，并应用验证规则。
2. **`unregister`**: (`Unregister<TFormValues>`) 用于注销表单中的输入字段。
3. **`handleSubmit`**: (`SubmitHandler<TFormValues>`) 用于处理表单提交，当表单被提交时，它会调用提供的回调函数，并将表单数据作为参数传递。
4. **`formState`**: (`FormState<TFormValues>`) 包含表单的状态信息，如 `errors`（验证错误），`isSubmitting`（提交中），`isSubmitted`（已提交）等。
5. **`reset`**: (`Reset<TFormValues>`) 重置表单状态。
6. **`resetField`**: (`ResetField<TFormValues>`) 重置特定字段的状态。
7. **`watch`**: (`Watch<TFormValues>`) 监听表单字段的值。
8. **`setValue`**: (`SetValue<TFormValues>`) 设置表单字段的值。
9. **`getValues`**: (`GetValues<TFormValues>`) 获取表单字段的当前值。
10. **`setError`**: (`SetError<TFormValues>`) 设置验证错误。
11. **`clearErrors`**: (`ClearErrors<TFormValues>`) 清除验证错误。
12. **`trigger`**: (`Trigger<TFormValues>`) 触发表单验证。
13. **`control`**: (`Control<TFormValues, any>`) 控制表单的状态，通常与 `Controller` 组件一起使用。

#### 示例：

```jsx
import React from 'react';
import { useForm } from 'react-hook-form';

function ExampleForm() {
  const { register, handleSubmit, formState: { errors }, control } = useForm();

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username', { required: true })} />
      {errors.username && <span>This field is required</span>}

      <input {...register('email', { required: true, pattern: /^\S+@\S+$/i })} />
      {errors.email && <span>This field is required</span>}

      <button type="submit">Submit</button>
    </form>
  );
}
```

在这个示例中，我们使用了 `useForm` Hook 初始化了一个表单实例，并获取了 `register`, `handleSubmit`, `formState`, 和 `control`。这些方法和状态可以用来控制表单的各个方面，例如注册表单字段、处理表单提交、获取表单状态和控制表单字段的状态。

## Controller

### Why

React Hook Form包含不受控制的组件和原生input，但很难避免使用外部组件，如React-Select，AntD 和 MUI。*这个组件将使您更容易使用外部组件*。通过使用 `Controller`，**你可以更轻松地创建受控组件，并将表单的状态和验证逻辑委托给 `react-hook-form`**。这种方法可以使你的表单逻辑更加清晰和可维护。

在 `react-hook-form` 中，`Controller` 是一个高阶组件（Higher Order Component, HOC），**用于处理受控组件的状态**。它通常与 `useForm` Hook 返回的 `control` 对象一起使用，以便更好地管理表单的状态和行为。

### `Controller` 的作用

- **状态管理**：`Controller` 用于管理受控组件的状态，包括值、验证状态等。
- **同步状态**：它负责将表单的状态同步到对应的表单字段。
- **验证逻辑**：`Controller` 可以应用验证逻辑，并将结果反馈给 `react-hook-form`。

### 使用 `Controller` 的步骤

1. **导入 `Controller`**:
   ```jsx
   import { Controller } from 'react-hook-form';
   ```

2. **获取 `control` 对象**:
   ```jsx
   const { control } = useForm();
   ```

3. **使用 `Controller`**:
   ```jsx
   <Controller
     name="fieldName"
     control={control}
     rules={{ required: true }}
     render={({ field }) => (
       <input {...field} placeholder="Enter your value" />
     )}
   />
   ```

### `Controller` 的属性

- **`name`**: (`string`) 字段名称，用于标识表单中的字段。
- **`control`**: (`Control<TFormValues, any>`) 由 `useForm` Hook 返回的 `control` 对象，用于控制表单的状态。
- **`rules`**: (`object`) 验证规则，可以是 `required`, `maxLength`, `minLength`, `pattern`, `validate`, 等。
- **`defaultValue`**: (`any`) 字段的默认值，如果没有通过 `useForm` 的 `defaultValues` 提供。
- **`render`**: (`function`) 一个函数，返回 JSX，通常包含一个受控组件。这个函数接收一个 `field` 对象，包含 `onChange`, `onBlur`, `value`, `ref`, 等方法，*用于控制输入字段的行为*。

### 示例

```jsx
import React from 'react';
import { useForm, Controller } from 'react-hook-form';

function ExampleForm() {
  const { control, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="username"
        control={control}
        rules={{ required: true }}
        render={({ field }) => (
          <>
            <label htmlFor="username">Username</label>
            <input
              id="username"
              type="text"
              {...field} // 将 field 对象的属性应用到输入元素
            />
            {errors.username && <span>This field is required</span>}
          </>
        )}
      />

      <Controller
        name="email"
        control={control}
        rules={{ required: true, pattern: /^\S+@\S+$/i }}
        render={({ field }) => (
          <>
            <label htmlFor="email">Email</label>
            <input
              id="email"
              type="email"
              {...field} // 将 field 对象的属性应用到输入元素
            />
            {errors.email && <span>This field is required</span>}
          </>
        )}
      />

      <button type="submit">Submit</button>
    </form>
  );
}
```

在这个示例中，我们使用了 `Controller` 组件来处理表单字段的状态。每个 `Controller` 组件都有一个 `name` 属性，用于标识字段，并且接收一个 `control` 对象，该对象来自于 `useForm` Hook。`render` 函数接收一个 `field` 对象，其中包含了用于控制输入字段的方法。

