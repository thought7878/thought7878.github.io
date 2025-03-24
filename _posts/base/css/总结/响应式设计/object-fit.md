以下是关于 CSS 属性 `object-fit` 的详细总结，包含其作用、语法、属性值及实际应用场景：

---

### 一、`object-fit` 的作用  
控制 `<img>`、`<video>` 等替换元素**在容器中的 填充方式**，解决图片/视频**被拉伸或压缩变形的问题**。  
- **核心功能**：类似 `background-size: cover/contain`，但直接作用于 HTML 媒体元素。  
- **适用场景**：固定尺寸容器内显示图片/视频，需保持内容比例且不拉伸。

---

### 二、语法与属性值  
```css
img {
  object-fit: fill | contain | cover | none | scale-down;
}
```

| **属性值**          | **效果描述**                                       | **示意图**                                                           |
| ---------------- | ---------------------------------------------- | ----------------------------------------------------------------- |
| **`fill`**       | **默认值**。*拉伸内容以填满容器*，**不保持比例**，可能导致变形。          | ![fill](https://via.placeholder.com/100x50?text=Fill)             |
| **`contain`**    | *保持内容比例*，*完整显示*在容器内，*可能留有空白区域*（短边适配）。          | ![contain](https://via.placeholder.com/100x50?text=Contain)       |
| **`cover`**      | *保持内容比例*，*覆盖整个容器*，*内容可能被裁剪*（长边适配）。             | ![cover](https://via.placeholder.com/100x50?text=Cover)           |
| **`none`**       | 保持内容原始尺寸和比例，不缩放，可能溢出容器或被裁剪。                    | ![none](https://via.placeholder.com/100x50?text=None)             |
| **`scale-down`** | 在 `none` 和 `contain` 中选择更小的尺寸（表现类似 `contain`）。 | ![scale-down](https://via.placeholder.com/100x50?text=Scale-Down) |

---

### 三、配合 `object-position` 使用  
**作用**：调整内容在容器内的 **对齐位置**（类似 `background-position`）。  
**语法**：  
```css
img {
  object-fit: cover;
  object-position: 20% 80%; /* 水平位置 垂直位置 */
}
```  
**示例**：  
- `object-position: center`（默认值，居中）  
- `object-position: top left`（左上角）  
- `object-position: 100px 50px`（像素偏移）  

---

### 四、代码示例  
#### 1. 固定容器内图片完美填充  
```html
<div class="image-container">
  <img src="photo.jpg" alt="示例图片">
</div>
```  
```css
.image-container {
  width: 300px;
  height: 200px;
  border: 1px solid #ddd;
}

.image-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;  /* 裁剪填充，保持比例 */
  object-position: center; /* 焦点居中 */
}
```  
**效果**：图片自动裁剪为容器尺寸，焦点居中显示，无拉伸变形。

---

#### 2. 产品列表图片统一尺寸  
```css
.product-image {
  width: 200px;
  height: 200px;
  object-fit: contain; /* 完整显示图片，留白 */
}
```  
**效果**：不同比例的图片统一显示为 200x200 方块，保持原比例，空白区域自动填充背景色。

---

### 五、常见应用场景  
| **场景**               | **推荐属性值**     | **说明**                              |
|-----------------------|-------------------|---------------------------------------|
| 头像显示              | `cover` + `center` | 裁剪为圆形/方形，焦点居中              |
| 产品图列表            | `contain`         | 完整显示不同比例商品图，避免变形        |
| 全屏背景图            | `cover`           | 图片覆盖整个屏幕，无空白或滚动条        |
| 缩略图展示            | `scale-down`      | 自动选择不拉伸的最小尺寸                |

---

### 六、注意事项  
1. **容器需固定尺寸**：父元素需设置 `width` 和 `height`，否则 `object-fit` 不生效。  
2. **兼容性**：  
   - 现代浏览器（Chrome、Firefox、Safari、Edge）均支持。  
   - **IE 不支持**，需 Polyfill（如 [object-fit-images](https://github.com/fregante/object-fit-images)）。  
3. **与 `background-size` 的区别**：  
   - `background-size` 用于背景图，`object-fit` 直接作用于 HTML 媒体元素。  

---

### 七、Polyfill 解决方案（针对 IE）  
```html
<!-- 引入 object-fit-images -->
<script src="https://cdn.jsdelivr.net/npm/object-fit-images@3.2.4/dist/ofi.min.js"></script>
```  
```css
img {
  object-fit: cover;
  font-family: 'object-fit: cover;'; /* 兼容 Hack */
}
```  
```javascript
// 初始化
objectFitImages();
```

---

### 总结  
`object-fit` 是控制图片/视频自适应容器的核心属性，尤其适合解决 **固定尺寸容器内的媒体内容适配问题**。优先使用 `cover`（裁剪填充）和 `contain`（完整显示），结合 `object-position` 精准控制焦点位置，可显著提升 UI 视觉效果。