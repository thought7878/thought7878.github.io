前端中的 _Back-Forward Cache（BFCache）_，也被称为*往返缓存*，是*一种用于提高网页加载性能和用户体验的技术*

### 定义与原理

- **定义**：BFCache 是*浏览器提供*的一种*缓存机制*，用于在用户使用浏览器的后退和前进按钮时，*快速恢复之前访问过*的页面，而*无需重新加载*整个页面。
- **原理**：当用户浏览网页时，浏览器会*将当前页面的 DOM 树、CSSOM、JavaScript 状态（离开时运行的任务）*等解析后的信息*存储在 BFCache 中*。当用户点击后退或前进按钮时，浏览器可以*直接从 BFCache 中恢复页面，而不是重新发送网络请求获取页面资源*，从而大大提高了页面的加载速度。

### 工作机制

- **页面进入 BFCache**：当用户离开当前页面，例如通过点击链接、前进或后退按钮等操作导航到其他页面时，浏览器会检查当前页面是否符合进入 BFCache 的条件。如果符合，浏览器会将当前页面的相关状态信息保存到 BFCache 中。
- **页面从 BFCache 恢复**：当用户使用后退或前进按钮返回到之前保存在 BFCache 中的页面时，浏览器会从 BFCache 中读取页面的状态信息，并将页面恢复到之前的状态。包括恢复页面的*滚动位置、表单输入值、JavaScript 执行状态*等，让用户感觉就像时间回溯一样，回到了离开时的页面状态。

### 优势

- **提高性能**：_减少了网络请求和页面渲染的时间_，特别是对于包含大量资源的复杂页面，性能提升效果更为明显。这有助于降低用户等待时间，提高用户体验。
- **节省资源**：_由于不需要重新加载页面资源_，减少了网络带宽的占用和服务器的负载，对于移动设备用户或在网络条件较差的环境下浏览网页的用户来说，节省流量和提高加载速度尤为重要。
- **增强用户体验**：快速的后退和前进功能使用户在浏览网页时感觉更加流畅和自然，减少了页面加载时的闪烁和等待，提高了用户对网站的满意度和粘性。

### 局限性和注意事项

- **兼容性问题**：不同浏览器对 BFCache 的支持和实现方式可能存在差异。某些浏览器可能对 BFCache 的支持不完全，或者在特定情况下可能会出现问题，例如页面恢复时的样式错乱或 JavaScript 行为异常等。
- **内存占用**：BFCache 会*占用一定的内存空间*来存储页面状态信息。如果用户在浏览器中*打开了大量页面*并频繁使用后退和前进按钮，_可能会导致内存占用过高_，影响浏览器的性能甚至导致系统崩溃。
- **动态内容更新**：对于一些实时更新的动态页面，如包含实时数据、实时聊天窗口等内容的页面，BFCache 可能无法及时更新页面内容。因为从 BFCache 中恢复的页面是之前保存的状态，可能不是最新的数据。

### 与前端开发的关系

- **开发者需要关注的点**：前端开发者在开发过程中需要考虑 BFCache 的影响，确保页面在 BFCache 中的存储和恢复过程中能够正常工作。例如，需要正确处理页面中的 JavaScript 事件绑定、定时器等，避免在页面恢复时出现异常行为。
- **控制 BFCache 的行为**：开发者可以通过一些技术手段来控制页面是否进入 BFCache，以及在页面恢复时执行特定的操作。例如，使用`pagehide`和`pageshow`事件来监听页面的隐藏和显示，在`pagehide`事件中可以保存页面的一些自定义状态信息，在`pageshow`事件中可以根据需要更新页面内容或执行一些初始化操作。

## How

在前端开发中，使用 Back-Forward Cache（BFCache）主要涉及对相关事件的监听和处理，以下是一些常见的使用方法和示例：

### 监听`pagehide`和`pageshow`事件

- **`pagehide`事件**：当页面即将被缓存或用户即将离开页面时触发，可以在此事件中保存页面的自定义状态信息，以便在页面恢复时使用。
- **`pageshow`事件**：当页面从缓存中恢复或首次加载时触发，可以利用这个事件来检查页面是否从缓存中恢复，并根据需要执行相应的操作，如更新页面内容、重新初始化某些组件等。

以下是一个简单的 JavaScript 代码示例：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>BFCache Example</title>
  </head>

  <body>
    <h1>Back-Forward Cache Example</h1>
    <input type="text" id="inputField" />

    <script>
      // 监听pagehide事件
      window.addEventListener("pagehide", function (event) {
        // 保存输入框的值到localStorage
        localStorage.setItem(
          "inputValue",
          document.getElementById("inputField").value
        );
      });

      // 监听pageshow事件
      window.addEventListener("pageshow", function (event) {
        // 检查是否从缓存中恢复
        if (event.persisted) {
          // 从localStorage中获取保存的值并设置到输入框
          document.getElementById("inputField").value =
            localStorage.getItem("inputValue");
          console.log("页面从缓存中恢复");
        } else {
          console.log("页面首次加载");
        }
      });
    </script>
  </body>
</html>
```

### 使用`beforeunload`事件

- 虽然`beforeunload`事件不完全是针对 BFCache，但在某些情况下可以与 BFCache 配合使用。它在页面即将卸载时触发，可以用于提示用户是否确认离开当前页面，这对于防止用户误操作导致页面状态丢失有一定的作用。

示例代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>BFCache with beforeunload</title>
  </head>

  <body>
    <h1>Back-Forward Cache with beforeunload Example</h1>

    <script>
      // 监听beforeunload事件
      window.addEventListener("beforeunload", function (event) {
        // 可以根据页面状态判断是否需要提示用户
        // 这里简单地总是显示提示
        event.preventDefault();
        event.returnValue = "";
        return "";
      });
    </script>
  </body>
</html>
```

在使用这些事件时，需要注意不同浏览器对 BFCache 和相关事件的支持可能存在差异，可能需要进行一些兼容性处理。同时，合理使用 BFCache 可以提高用户体验，但也要避免过度依赖，确保页面在各种情况下都能正常工作。
