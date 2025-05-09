CSS（层叠样式表）中的规则是定义网页*外观*和*布局*的核心部分。每条 CSS 规则由 **选择器** 和 **声明块** 组成，用于指定哪些 HTML 元素应用哪些样式。以下是关于 CSS 规则的详细解析：

---

## **1. CSS 规则的基本结构**

一条 CSS 规则由以下两部分组成：
- **选择器（Selector）**：指定要应用样式的 HTML 元素。
- **声明块（Declaration Block）**：包含一组样式声明，定义元素的外观。

### **语法**
```css
selector {
  property: value;
}
```

#### 示例：
```css
p {
  color: blue;
  font-size: 16px;
}
```
在这个例子中：
- `p` 是选择器，表示所有 `<p>` 元素。
- `color: blue;` 和 `font-size: 16px;` 是声明，分别设置文字颜色和字体大小。

---

## **2. CSS 规则的组成部分**

### **(1) 选择器**
选择器用于定位 HTML 元素，可以分为以下几类：

#### **基本选择器**
- **元素选择器**：匹配特定的 HTML 元素。
  ```css
  p { color: red; } /* 匹配所有 <p> 元素 */
  ```
- **类选择器**：匹配具有特定类名的元素。
  ```css
  .highlight { background-color: yellow; }
  ```
- **ID 选择器**：匹配具有特定 ID 的元素。
  ```css
  #header { font-size: 24px; }
  ```

#### **组合选择器**
- **后代选择器**：匹配某个元素的后代元素。
  ```css
  div p { color: green; } /* 匹配所有 <div> 内部的 <p> 元素 */
  ```
- **子选择器**：匹配某个元素的直接子元素。
  ```css
  ul > li { list-style: none; } /* 匹配所有 <ul> 的直接子元素 <li> */
  ```
- **相邻兄弟选择器**：匹配紧接在另一个元素后的元素。
  ```css
  h1 + p { margin-top: 0; } /* 匹配紧跟在 <h1> 后的 <p> 元素 */
  ```
- **通用兄弟选择器**：匹配某个元素之后的所有兄弟元素。
  ```css
  h1 ~ p { color: gray; } /* 匹配所有在 <h1> 之后的 <p> 元素 */
  ```

#### **伪类和伪元素**
- **伪类**：用于选择元素的特定状态。
  ```css
  a:hover { color: red; } /* 匹配鼠标悬停时的链接 */
  ```
- **伪元素**：用于选择元素的特定部分。
  ```css
  p::first-line { font-weight: bold; } /* 匹配段落的第一行 */
  ```

---

### **(2) 声明块**
声明块包含一组样式声明，每条声明由 **属性（Property）** 和 **值（Value）** 组成。

#### **语法**
```css
property: value;
```

#### 示例：
```css
h1 {
  color: blue; /* 属性为 color，值为 blue */
  text-align: center; /* 属性为 text-align，值为 center */
}
```

---

## **3. CSS 规则的优先级**

当多个 CSS 规则同时作用于同一个元素时，浏览器会根据 **优先级规则** 决定最终应用的样式。

### **优先级顺序**
1. **内联样式**：直接写在 HTML 元素的 `style` 属性中，优先级最高。
   ```html
   <p style="color: red;">Inline style</p>
   ```
2. **ID 选择器**：优先级高于类选择器和元素选择器。
   ```css
   #header { color: blue; }
   ```
3. **类选择器、属性选择器和伪类**：优先级高于元素选择器。
   ```css
   .highlight { color: yellow; }
   ```
4. **元素选择器和伪元素**：优先级最低。
   ```css
   p { color: black; }
   ```

### **!important**
- 使用 `!important` 可以覆盖其他规则的优先级。
- **注意**：尽量避免滥用 `!important`，因为它会破坏优先级规则，增加维护难度。
  ```css
  p {
    color: red !important;
  }
  ```

---

## **4. CSS 规则的继承**

某些 CSS 属性会自动从父元素继承到子元素，这种机制称为 **继承**。

### **可继承的属性**
- 字体相关：`font-family`、`font-size`、`color` 等。
- 文本相关：`text-align`、`line-height` 等。

#### 示例：
```css
body {
  color: blue;
}

p {
  /* <p> 元素会继承 body 的 color 属性 */
}
```

### **不可继承的属性**
- 边框、外边距、背景等属性不会自动继承。
  ```css
  body {
    border: 1px solid black;
  }

  p {
    /* <p> 元素不会继承 body 的 border 属性 */
  }
  ```

---

## **5. CSS 规则的层叠**

当多个规则应用于同一个元素时，浏览器会根据 **层叠规则** 决定最终样式。

### **层叠规则**
1. **来源**：用户代理样式 < 用户样式 < 作者样式。
2. **重要性**：普通声明 < `!important` 声明。
3. **优先级**：根据选择器的优先级决定。
4. **顺序**：后定义的规则覆盖先定义的规则。

#### 示例：
```css
/* 规则 1 */
p {
  color: red;
}

/* 规则 2 */
p {
  color: blue;
}
```
在这个例子中，`color: blue;` 会覆盖 `color: red;`，因为它是后定义的规则。

---

## **6. 总结**

CSS 规则是定义网页样式的基石，以下是关键点总结：

1. **基本结构**：
   - 每条规则由选择器和声明块组成。
2. **选择器类型**：
   - 元素选择器、类选择器、ID 选择器、组合选择器、伪类和伪元素。
3. **优先级**：
   - 内联样式 > ID 选择器 > 类选择器 > 元素选择器。
   - `!important` 可以覆盖其他规则。
4. **继承**：
   - 某些属性会从父元素继承到子元素。
5. **层叠**：
   - 根据来源、重要性、优先级和顺序决定最终样式。

通过深入理解 CSS 规则的结构和机制，开发者可以更高效地编写样式代码，并解决复杂的样式冲突问题。