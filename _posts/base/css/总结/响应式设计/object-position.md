以下是关于 CSS 属性 `object-position` 的详细总结，涵盖其作用、语法、属性值及实际应用场景：

---

### **一、`object-position` 的作用**  
控制 `<img>`、`<video>` 等替换元素的内容*在容器内的 **对齐位置**，需与 `object-fit` 配合使用*。  
- **核心功能**：精准调整内容焦点区域，避免关键信息被裁剪（如人脸、产品细节）。  
- **类比**：类似 `background-position`，但作用于 HTML 媒体元素。

---

### **二、语法与属性值**  
```css
img {
  object-position: [水平位置] [垂直位置];
}
```

#### **1. 关键词定位**  
| **关键词**      | **说明**                        | **示例**                     |
|-----------------|---------------------------------|------------------------------|
| `left` `right` `center` | 水平方向对齐位置 | `object-position: left top`  |  
| `top` `bottom` `center` | 垂直方向对齐位置 | `object-position: right 80%` |  

#### **2. 数值定位**  
| **单位**       | **说明**                                     | **示例**                      |
|----------------|---------------------------------------------|-------------------------------|
| **百分比**     | 相对于容器尺寸与内容尺寸的差值计算偏移量      | `object-position: 20% 80%`    |  
| **长度单位**   | 固定像素/em 等偏移（可负值）                 | `object-position: 30px -10px` |  

---

### **三、代码示例与效果对比**  
#### 1. 默认居中（`object-position: center`）  
```css
.container img {
  width: 300px;
  height: 200px;
  object-fit: cover;
  object-position: center; /* 默认值 */
}
```  
![居中效果](https://via.placeholder.com/300x200?text=Center)

#### 2. 左上角对齐（`object-position: left top`）  
```css
.container img {
  object-position: left top;
}
```  
![左上角效果](https://via.placeholder.com/300x200?text=Left+Top)

#### 3. 自定义偏移（`object-position: 70% 20%`）  
```css
.container img {
  object-position: 70% 20%; /* 水平向右 70%，垂直向下 20% */
}
```  
![自定义偏移](https://via.placeholder.com/300x200?text=70%+20%)

---

### **四、常见应用场景**  
| **场景**               | **推荐定位值**          | **说明**                              |  
|------------------------|------------------------|---------------------------------------|  
| **人物头像**           | `object-position: 50% 30%` | 聚焦脸部，避免头顶或下巴被裁剪        |  
| **横向长图**          | `object-position: left`    | 优先显示左侧内容（如长横幅广告图）    |  
| **产品细节特写**       | `object-position: 80% 50%` | 突出产品核心区域（如LOGO、按钮）      |  
| **艺术摄影展示**       | `object-position: 20% 60%` | 自定义构图焦点，增强视觉效果          |  

---

### **五、与 `object-fit` 协作示例**  
```html
<div class="gallery">
  <img src="landscape.jpg" alt="风景图">
</div>
```  
```css
.gallery {
  width: 400px;
  height: 300px;
  border: 2px solid #ddd;
}

.gallery img {
  width: 100%;
  height: 100%;
  object-fit: cover;     /* 裁剪填充容器 */
  object-position: 20% 70%; /* 聚焦图片右下区域 */
}
```  
**效果**：图片右下部分 20% 水平位置和 70% 垂直位置成为视觉焦点。

---

### **六、注意事项**  
1. **依赖容器尺寸**：需为容器设置明确的 `width` 和 `height`，否则定位不准确。  
2. **百分比计算规则**：  
   - `object-position: 20% 80%` 的实际偏移量为：  
     **水平**：`(容器宽度 - 图片宽度) * 20%`  
     **垂直**：`(容器高度 - 图片高度) * 80%`  
3. **兼容性**：  
   - 现代浏览器（Chrome、Firefox、Safari、Edge）均支持。  
   - **IE 不支持**，需 Polyfill（如 [object-fit-images](https://github.com/fregante/object-fit-images)）。  

---

### **七、总结**  
| **核心要点**               | **说明**                              |  
|---------------------------|---------------------------------------|  
| **功能定位**              | 调整媒体内容在容器内的显示焦点区域      |  
| **常用值**                | 关键词（`left`/`top`）、百分比、像素   |  
| **最佳搭档**              | 必须与 `object-fit` 配合使用           |  
| **设计价值**              | 提升图片/视频的关键信息展示精准度       |  

通过灵活使用 `object-position`，可有效控制媒体内容的视觉焦点，避免机械居中导致的构图单调或信息丢失。