## display

Defines the element as a grid container and establishes a new grid formatting context for its contents.  
将元素定义为网格容器并为其内容建立新的网格格式化上下文。

Values: 

- **`grid`** – 生成块级网格 
- **`inline-grid`** – 生成内联网格 

```css
.container {
  display: grid | inline-grid;
}
```

## grid-template-columns  grid-template-rows

使用空格分隔的值列表，定义网格的列和行。这些值代表轨道大小，它们之间的空格代表网格线。

Values: 

- **`<track-size>`** – 长度、百分比或分数， [fr](https://css-tricks.com/introduction-fr-css-unit/) 单位
- **`<line-name>`** – 您选择的任意名称

```css
.container {
  grid-template-columns: ...  ...;
  /* e.g. 
      1fr 1fr
      minmax(10px, 1fr) 3fr
      repeat(5, 1fr)
      50px auto 100px 1fr
  */
  grid-template-rows: ... ...;
  /* e.g. 
      min-content 1fr min-content
      100px 1fr max-content
  */
}
```

- 网格线会根据这些分配自动分配正数（-1 是最后一行的替代值）。

![](assets/排版-Grid-API/2023-09-19-13-57-06-image.png)

### 命名grid line

- 明确命名这些行。请注意名称的括号语法：

```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

![](assets/排版-Grid-API/2023-09-19-14-06-03-image.png)![Grid with user named lines](https://css-tricks.com/wp-content/uploads/2018/11/template-column-rows-02.svg)

- 请注意，一行可以有多个名称。例如，这里第二行将有两个名称：row1-end 和 row2-start：

```css
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```

### repeat()

- 如果您的定义包含重复部分，您可以使用 `repeat()` 表示法来简化内容：

```css
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}

/*这相当于：*/
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```

如果多条线共享相同的名称，则可以通过其线名和计数来引用它们。

```css
.item {
  grid-column-start: col-start 2;
}
```

### `fr` 单位

- `fr` 单位允许您将轨道的大小，设置为网格容器可用空间的一部分。

例如，这会将每个项目设置为网格容器宽度的三分之一：

```css
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
```

- 可用空间是在所有非灵活项目之后计算的。

在此示例中， `fr` 单位可用的可用空间总量不包括 50px：

```css
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```



## grid-template-areas

使用 [grid-area](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-area)  属性指定的网格区域的名称来定义网格模板。重复网格区域的名称会导致内容跨越这些单元格。点表示空单元格。语法本身提供了网格结构的可视化。

Values: 

- **`<grid-area-name>`** – 用 `grid-area` 指定的网格区域的名称
- **`.`** – 点表示空单元
- **`none`** – 未定义网格区域

```css
.container {
  grid-template-areas: 
    "<grid-area-name> | . | none | ..."
    "...";
}
```

Example: 

```css
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

这将创建一个四列宽、三行高的网格。整个顶行将由标题区域组成。中间行将由两个主要区域、一个空单元格和一个侧边栏区域组成。最后一行全是页脚。

![](assets/排版-Grid-API/2023-09-19-14-47-21-image.png)

声明中的每一行都需要具有相同数量的单元格。您可以使用任意数量的相邻句点来声明单个空单元格。只要句点之间没有空格，它们就代表一个单元格。

请注意，您不是使用此语法命名行，而是使用区域。当您使用此语法时，区域两端的行实际上会自动命名。如果网格区域的名称为 foo，则该区域的起始行线和起始列线的名称将为 foo-start，其最后一个行线和最后一个列线的名称将为 foo-end。<u>这意味着某些行可能有多个名称，例如上例中最左边的行，它将有三个名称：header-start、main-start 和 footer-start</u>。



## grid-template

在单个声明中设置 [grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows) 、 [grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows) 和 [grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas)的简写。

Values: 价值观：

- **`none`** – 将所有三个属性设置为其初始值
- **`<grid-template-rows>` / `<grid-template-columns`>** – `<grid-template-rows>` / `<grid-template-columns` > – 分别将 [grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows) 和 [grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)` 设置为指定值，并将 `[grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas) 设置为 `none`

```css
.container {
  grid-template: none | <grid-template-rows> / <grid-template-columns>;
}
```

它还接受更复杂但相当方便的语法来指定所有这三个。这是一个例子：

```css
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
}
```

这相当于：

```css
.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas: 
    "header header header" 
    "footer footer footer";
}
```

由于 `grid-template` 不会重置隐式网格属性（ [grid-auto-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows) 、 [grid-auto-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows) 和 [grid-auto-flow](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-flow) ），这可能就是您想要的在大多数情况下，建议使用 [grid](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid) 属性而不是 `grid-template` 。


