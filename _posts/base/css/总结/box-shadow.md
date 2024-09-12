`box-shadow` 是 CSS 中的一个属性，用于向元素添加阴影效果。它可以让元素*看起来像是从页面上凸出来或者凹进去*，从而增加视觉层次感和深度。`box-shadow` 属性*可以应用于任何元素*，包括文本、图像、块级元素等。

### 语法
```css
box-shadow: [offset-x] [offset-y] [blur-radius] [spread-radius] [color] [inset];
```

### 参数解释
- **`offset-x`**：水平偏移量，正值表示阴影向右移动，负值表示阴影向左移动。
- **`offset-y`**：垂直偏移量，正值表示阴影向下移动，负值表示阴影向上移动。
- **`blur-radius`**：阴影模糊半径。值越大，阴影越模糊。默认值为 0，表示没有模糊。
- **`spread-radius`**：阴影扩展半径。正值会使阴影变大，负值会使阴影变小。
- **`color`**：阴影的颜色。
- **`inset`**：可选关键字，如果存在，则表示内阴影（阴影位于元素内部）；如果不存在，则表示外阴影（阴影位于元素外部）。

### 示例
假设我们想要给一个按钮添加一个简单的阴影效果。

#### HTML
```html
<button class="shadowed-button">点击我</button>
```

#### CSS
```css
.shadowed-button {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  box-shadow: 10px 10px 5px 0px rgba(0,0,0,0.75);
}
```

### 示例解释
在这个例子中，我们给按钮添加了一个向外的阴影：
- `10px` 表示水平阴影的位置（阴影在元素右侧 10px 处）。
- `10px` 表示垂直阴影的位置（阴影在元素下方 10px 处）。
- `5px` 表示阴影的模糊半径（阴影模糊程度）。
- `0px` 表示阴影的扩展半径（阴影大小不变）。
- `rgba(0,0,0,0.75)` 表示阴影的颜色（黑色，透明度为 75%）。

### 多重阴影
你还可以为一个元素添加多重阴影效果，只需要将多个 `box-shadow` 值用逗号分隔即可。

#### 示例
```css
.shadowed-button {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  box-shadow: 
    2px 2px 4px rgba(0, 0, 0, 0.5),
    -2px -2px 4px rgba(255, 255, 255, 0.3);
}
```

### 内阴影

如果希望阴影在元素内部，可以使用 `inset` 关键字。

```css
.box {
  box-shadow: inset 10px 10px 5px 0px rgba(0,0,0,0.75);
}
```

### 兼容性
`box-shadow` 在现代浏览器中得到了很好的支持，但在较旧的浏览器中可能需要前缀 `-webkit-`、`-moz-` 或 `-ms-` 来支持 WebKit、Mozilla 和 Microsoft 的浏览器引擎。

### 总结
`box-shadow` 是一个非常有用的 CSS 属性，它可以用来为元素添加阴影效果，从而使页面更具吸引力和立体感。通过调整不同的参数，你可以创造出多种多样的阴影效果。在设计网页时，合理使用 `box-shadow` 可以大大提升用户体验和视觉美感。