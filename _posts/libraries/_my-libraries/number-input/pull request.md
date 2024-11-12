Feat: Create a new component NumberField
# Anatomy of number-field

The [NumberField](https://github.com/thought7878/andi-ui?tab=readme-ov-file) component is a flexible and accessible input field for numerical values, built with React and integrated with shadcn/ui. It offers customizable increment and decrement buttons, supports various layouts, and includes built-in error handling and validation. This component is designed to be highly adaptable, making it suitable for a wide range of applications where numerical input is required.

This component set includes the following sub-components:

```tsx
<NumberField name="..." value="..." onChange={...} className="...">
  <NumberFieldLabel>{/* label */}</NumberFieldLabel>
  <NumberFieldGroup>
    <NumberFieldIncrement>{/* increment icon */}</NumberFieldIncrement>
    <NumberFieldInput className="..." />
    <NumberFieldDecrement>{/* decrement icon */}</NumberFieldDecrement>
  </NumberFieldGroup>
  <NumberFieldError />
</NumberField>
```

<img width="539" alt="introduction" src="https://github.com/user-attachments/assets/2d59b16a-031a-4d4f-bf20-57f265c95fbf" />

## Features

Key features include:

1. Customizable button placement (inside or outside the input)
2. Flexible label positioning
3. Internationalization support
4. Accessibility features using React Aria
5. Integration with shadcn/ui components and styling
6. Compound component structure for easy customization

# Examples

## Default

```tsx
import {
  NumberField,
  NumberFieldDecrement,
  NumberFieldGroup,
  NumberFieldIncrement,
  NumberFieldInput,
  NumberFieldLabel,
} from "@/components/ui/number-field"

export function NumberFieldDemo() {
  return (
    <NumberField>
      <NumberFieldLabel>Amount</NumberFieldLabel>
      <NumberFieldGroup>
        <NumberFieldIncrement />
        <NumberFieldInput />
        <NumberFieldDecrement />
      </NumberFieldGroup>
    </NumberField>
  )
}

```

<img width="743" alt="image" src="https://github.com/user-attachments/assets/30363d29-67ee-476a-8bc9-36555d979637">

## Form

```tsx
"use client"

import { zodResolver } from "@hookform/resolvers/zod"
import { useForm } from "react-hook-form"
import { z } from "zod"

import { toast } from "@/components/hooks/use-toast"
import { Button } from "@/components/ui/button"
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form"
import {
  NumberField,
  NumberFieldDecrement,
  NumberFieldGroup,
  NumberFieldIncrement,
  NumberFieldInput,
} from "@/components/ui/number-field"

const formSchema = z.object({
  amount: z
    .number({
      required_error: "Amount is required.",
    })
    .min(0, { message: "Amount must be greater than 0." })
    .max(10, { message: "Amount must be less than 11." }),
})

export function NumberFieldForm() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
  })

  function onSubmit(data: z.infer<typeof formSchema>) {
    toast({
      title: "You submitted the following values:",
      description: (
        <pre className="mt-2 w-[340px] rounded-md bg-slate-950 p-4">
          <code className="text-white">{JSON.stringify(data, null, 2)}</code>
        </pre>
      ),
    })
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="w-2/3 space-y-6">
        <FormField
          control={form.control}
          name="amount"
          render={({ field: { onChange, value } }) => (
            <FormItem>
              <FormLabel>Amount</FormLabel>
              <FormControl>
                <NumberField onChange={onChange} value={value}>
                  <NumberFieldGroup>
                    <NumberFieldDecrement />
                    <NumberFieldInput />
                    <NumberFieldIncrement />
                  </NumberFieldGroup>
                </NumberField>
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        <Button type="submit">Submit</Button>
      </form>
    </Form>
  )
}

```

<img width="747" alt="image" src="https://github.com/user-attachments/assets/4cf0f230-49a5-4d2a-b918-a09d1fef8a69">

## Disabled

```tsx
import {
  NumberField,
  NumberFieldDecrement,
  NumberFieldGroup,
  NumberFieldIncrement,
  NumberFieldInput,
  NumberFieldLabel,
} from "@/components/ui/number-field"

export function NumberFieldDisabled() {
  return (
    <NumberField isDisabled>
      <NumberFieldLabel>Amount</NumberFieldLabel>
      <NumberFieldGroup>
        <NumberFieldIncrement />
        <NumberFieldInput />
        <NumberFieldDecrement />
      </NumberFieldGroup>
    </NumberField>
  )
}

```

<img width="736" alt="image" src="https://github.com/user-attachments/assets/dee19ee5-a892-4d41-8f8d-6f3514ed506e">

## Button position
The button can be located inside or outside, the default is inside. You can also customize it as needed
```tsx
import {
  NumberField,
  NumberFieldDecrement,
  NumberFieldGroup,
  NumberFieldIncrement,
  NumberFieldInput,
  NumberFieldLabel,
} from "@/components/ui/number-field"

export function NumberFieldBtnPosition() {
  return (
    <NumberField btnPosition="outside">
      <NumberFieldLabel>Amount</NumberFieldLabel>
      <NumberFieldGroup>
        <NumberFieldIncrement />
        <NumberFieldInput />
        <NumberFieldDecrement />
      </NumberFieldGroup>
    </NumberField>
  )
}

```
<img width="747" alt="image" src="https://github.com/user-attachments/assets/3fcbaa9e-52a3-489d-b415-18a2596cde48">

## Label position
The label can be located at the top or left, the default is the top

```tsx
import {
  NumberField,
  NumberFieldDecrement,
  NumberFieldGroup,
  NumberFieldIncrement,
  NumberFieldInput,
  NumberFieldLabel,
} from "@/components/ui/number-field"

export function NumberFieldLabelPosition() {
  return (
    <NumberField labelPosition="left">
      <NumberFieldLabel>Amount</NumberFieldLabel>
      <NumberFieldGroup>
        <NumberFieldIncrement />
        <NumberFieldInput />
        <NumberFieldDecrement />
      </NumberFieldGroup>
    </NumberField>
  )
}

```
<img width="735" alt="image" src="https://github.com/user-attachments/assets/a250cfe1-762a-4e0f-a774-3f53c3eaf77d">

## Number formatting
Supported formats include Decimals, Percentages, Currency values, Units
```tsx
import {
  NumberField,
  NumberFieldDecrement,
  NumberFieldGroup,
  NumberFieldIncrement,
  NumberFieldInput,
  NumberFieldLabel,
} from "@/components/ui/number-field"

export function NumberFieldNumberFormatting() {
  return (
    <NumberField
      label="Currency"
      defaultValue={1888}
      formatOptions={{
        style: "currency",
        currency: "EUR",
        currencyDisplay: "code",
        currencySign: "accounting",
      }}
    >
      <NumberFieldLabel>Amount</NumberFieldLabel>
      <NumberFieldGroup>
        <NumberFieldIncrement />
        <NumberFieldInput />
        <NumberFieldDecrement />
      </NumberFieldGroup>
    </NumberField>
  )
}

```
<img width="750" alt="image" src="https://github.com/user-attachments/assets/fc109024-31e9-42a8-ad37-c0c53c234c4a">

