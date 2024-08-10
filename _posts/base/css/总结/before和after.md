在 CSS 中，`::before` 和 `::after` 是伪元素选择器，用于在被选中的元素的内容之前或之后插入内容。这些伪元素可以帮助你无需修改 HTML 结构即可向页面添加装饰性的内容或元素。

### 语法
```css
selector::before {
  content: "some content";
}

selector::after {
  content: "some content";
}
```

### 用途
1. **添加装饰性内容**：
   - 可以用于添加图标、符号或其他装饰性文本。

2. **创建装饰性元素**：
   - 可以用于创建*边框、阴影、背景色*等装饰性元素。

3. **清理浮动**：
   - 可以利用 `::after` 伪元素来清除浮动。

### 示例
假设我们想要在每个 `<p>` 元素前面添加一个图标，并在每个 `<p>` 元素后面添加一条线。

#### HTML
```html
<p>第一段落。</p>
<p>第二段落。</p>
```

#### CSS
```css
p::before {
  content: "➤ ";
  color: blue;
}

p::after {
  content: " ————";
  color: gray;
}
```

### 清理浮动示例
假设我们有一个包含浮动元素的容器，并且需要清除这些元素的浮动。

#### HTML
```html
<div class="container">
  <div class="item">Item 1</div>
  <div class="item">Item 2</div>
</div>
```

#### CSS
```css
.container {
  overflow: auto;
}

.item {
  float: left;
  margin-right: 10px;
  background-color: lightblue;
  padding: 10px;
}

.container::after {
  content: "";
  display: table;
  clear: both;
}
```

### 注意事项
- **伪元素与伪类的区别**：伪元素（如 `::before` 和 `::after`）用于向文档流中添加内容，而伪类（如 `:hover` 和 `:active`）用于选择处于特定状态的元素。
- **双冒号（::）**：在 CSS3 中，推荐使用双冒号 `::` 来表示伪元素，以区别于伪类。不过大多数浏览器也支持单冒号 `:` 的写法。
- **`content` 属性**：对于 `::before` 和 `::after` 伪元素来说，`content` 属性是必需的。如果未设置 `content` 属性，伪元素将不会生成。

### 总结
`::before` 和 `::after` 伪元素选择器是 CSS 中非常有用的工具，可以让你在不改变 HTML 结构的情况下向页面添加装饰性内容或元素。这些伪元素广泛用于各种装饰目的，比如添加图标、创建装饰性边框、清理浮动等。通过合理地使用这些伪元素，你可以增强页面的设计和布局。