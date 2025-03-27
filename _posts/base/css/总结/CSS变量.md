CSS 变量是现代 CSS 开发中的重要工具，能够显著*提升代码的灵活性和可维护性*，同时*简化复杂样式的管理*。

## 是什么？

CSS 变量（也称为*自定义属性*或 Custom Properties）是一种允许开发者*在 CSS 中定义、使用变量（可复用的值）* 的机制。它们*以 `--` 开头命名*，类似于*变量*的概念，可以*在整个样式表中引用*和修改。

**核心概念：**

- **定义变量**：通过 `--变量名: 值;` 定义。
- **使用变量**：通过 `var(--变量名)` 引用。
- **作用域**：CSS 变量具有继承性，通常在某个选择器内定义时*仅对该选择器及其子元素生效*。

---

## 为什么使用 CSS 变量？

以下是使用 CSS 变量的主要原因和优势：

### 提高代码可维护性、灵活性

通过将常用值（如颜色、字体大小、间距等）提取为变量，可以*减少重复代码*。_当需要修改全局样式时，只需更改变量值即可，而无需逐一修改每个样式规则_。

**示例：统一管理颜色**

```css
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
}

.button {
  background-color: var(--primary-color);
}

.alert {
  color: var(--secondary-color);
}
```

如果需要更改主题颜色，只需修改 `--primary-color` 和 `--secondary-color` 的值。

---

### 动态更新样式

CSS 变量可以通过 JavaScript 动态修改，从而实现动态样式切换。例如，切换主题、调整布局等。

**示例：动态切换主题**

```javascript
document.documentElement.style.setProperty("--primary-color", "#e74c3c");
```

---

### 支持响应式设计

CSS 变量可以结合媒体查询使用，根据屏幕尺寸动态调整变量值，从而实现响应式设计。

**示例：响应式字体大小**

```css
:root {
  --font-size: 16px;
}

@media (min-width: 768px) {
  :root {
    --font-size: 18px;
  }
}

body {
  font-size: var(--font-size);
}
```

---

### 减少冗余代码

通过复用变量，可以避免在多个地方重复写相同的值，从而减少代码量并降低出错的可能性。

---

## 怎么用？

### 定义变量

_局部变量优先于全局变量_。

#### 在全局范围内定义变量

可以在 `:root` 伪类中定义*全局变量*，这些变量**在整个文档中都可用**。

```css
/* 全局变量 */
:root {
  --main-color: #ff0000;
  --font-size: 16px;
  --border-radius: 8px;
}
```

#### 在局部范围内定义变量

你也可以在特定的*选择器或元素*内定义变量，这样变量的**作用范围仅限于该选择器或元素及其后代**。

```css
/* 局部变量 */
.custom-element {
  --element-color: #00ff00;
  background-color: var(--element-color);
}
```

---

### 使用变量

*通过 `var()` 函数*引用变量。如果变量未定义，可以提供一个**备用值**。

```css
.button {
  background-color: var(
    --primary-color,
    #ccc
  ); /* 如果 --primary-color 未定义，则使用 #ccc */
  padding: var(--spacing);
}
```

#### 变量的回退值

`var()` 函数还可以接受*第二个参数作为回退值，当变量未定义或无效时，会使用这个回退值*。

```css
.button {
  color: var(--text-color, black); /* 如果 --text-color 未定义，则使用黑色 */
}
```

#### 变量的作用域

CSS 变量具有作用域的概念。_在一个更具体的范围内定义的变量会覆盖同名的全局变量_。

```css
:root {
  --color: blue;
}

.custom-element {
  --color: green;
  background-color: var(--color); /* 使用绿色，因为它是局部定义的 */
}
```

#### 变量的继承

变量可以被继承，这意味着*后代元素可以使用祖先元素定义的变量*。

```css
.parent {
  --color: blue;
}

.child {
  color: var(--color); /* 使用蓝色 */
}
```

#### 变量的动态设置

CSS 变量*不仅可以静态定义*，**还可以通过 JavaScript 动态设置，这对于响应式设计和动态主题切换非常有用**。

```javascript
document.documentElement.style.setProperty("--main-color", "#0000ff");
```

#### 变量的计算

CSS 变量*可以与其他计算值结合使用*，比如单位运算、百分比等。

```css
:root {
  --base-font-size: 16px;
}

.text {
  font-size: calc(var(--base-font-size) * 1.5); /* 24px */
}
```

---

### 示例代码

#### 示例 1：全局变量管理颜色

```html
<div class="box"></div>
<button class="button">Click Me</button>
```

```css
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --spacing: 15px;
}

.box {
  width: 100px;
  height: 100px;
  background-color: var(--primary-color);
  margin: var(--spacing);
}

.button {
  background-color: var(--secondary-color);
  padding: var(--spacing);
  color: white;
}
```

**解释：**

- 定义了全局变量 `--primary-color`, `--secondary-color`, 和 `--spacing`。
- `.box` 和 `.button` 使用这些变量来设置背景颜色、边距和内边距。

---

#### 示例 2：动态切换主题

```html
<button id="theme-switcher">Switch Theme</button>
<div class="box"></div>
```

```css
:root {
  --background-color: #fff;
  --text-color: #000;
}

body {
  background-color: var(--background-color);
  color: var(--text-color);
}

.dark-theme {
  --background-color: #333;
  --text-color: #fff;
}
```

```javascript
const themeSwitcher = document.getElementById("theme-switcher");
themeSwitcher.addEventListener("click", () => {
  document.body.classList.toggle("dark-theme");
});
```

**解释：**

- 定义了两个变量 `--background-color` 和 `--text-color`。
- 点击按钮时，通过切换 `.dark-theme` 类动态改变变量值，从而切换主题。

---

#### 示例 3：响应式设计

```css
:root {
  --font-size: 16px;
}

@media (min-width: 768px) {
  :root {
    --font-size: 20px;
  }
}

body {
  font-size: var(--font-size);
}
```

**解释：**

- 在小屏幕设备上，字体大小为 16px；在大屏幕设备上，字体大小调整为 20px。

---

### 注意事项

1. **作用域**：变量的作用范围由定义它的选择器决定。_局部变量优先于全局变量_。

   ```css
   :root {
     --color: blue;
   }

   .box {
     --color: red;
     background-color: var(--color); /* 使用局部变量 red */
   }
   ```

2. **默认值**：如果变量未定义，可以通过 `var(--变量名, 默认值)` 提供备用值。
3. **浏览器兼容性**：CSS 变量在现代浏览器中得到了广泛支持，但在较旧的浏览器（如 IE11）中可能无法正常工作。

---

## 总结

- **是什么？**  
  CSS 变量是一种允许开发者定义可复用值的机制，以 `--` 开头命名，并通过 `var()` 引用。

- **为什么？**

  - 提高代码可维护性。
  - 支持动态更新样式。
  - 适用于响应式设计。
  - 减少冗余代码。

- **怎么用？**
  - 使用 `--变量名: 值;` 定义变量。
  - 使用 `var(--变量名)` 引用变量。
  - 结合媒体查询和 JavaScript 实现动态样式。
