# <Form {...form}>
```ts
import {
	FormProvider,
} from "react-hook-form"

const Form = FormProvider
```

## FormProvider

内部封装的是Context Provider。
使用useFormContext()获取Provider数据

```ts
import React from "react"
import { useForm, FormProvider, useFormContext } from "react-hook-form"

export default function App() {
  const methods = useForm()
  const onSubmit = (data) => console.log(data)

  return (
    <FormProvider {...methods}>
      // pass all methods into the context
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <NestedInput />
        <input type="submit" />
      </form>
    </FormProvider>
  )
}


function NestedInput() {
  const { register } = useFormContext() // retrieve all hook methods

  return <input {...register("test")} />
}
```

# FormField

## [Controller](https://react-hook-form.com/docs/usecontroller/controller)

React Hook Form包含不受控制的组件和原生input，但很难避免使用外部组件，如React-Select，AntD 和 MUI。*这个组件将使您更容易使用它们*。

# FormItem


# FormLabel


# FormControl


# FormDescription

# FormMessage