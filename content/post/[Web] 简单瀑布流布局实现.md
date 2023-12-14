---
title: '[Web] 简单瀑布流布局实现'
date: 2023-02-14 09:45:47
tags:
  - 前端
  - css
  - javascript
categories:
  - Web
  - 笔记
description: '使用少量 JS 和 CSS 实现的瀑布流布局'
---

目前的纯 CSS 布局, 是没办法实现比较完美的瀑布流布局的.


> 参考: [CSS总结:瀑布流布局 - 黑白程序员
](https://blog.csdn.net/qq_53008257/article/details/124638563)


我使用 JS + CSS, 并且自动布局实现了较为简单, 观赏性好的瀑布流布局.


## 代码

HTML:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <button onclick="add_new()">add</button>
    <button onclick="start_loop()">start loop</button>
    <button onclick="stop_loop()">stop loop</button>
    
    <!--瀑布流容器-->
    <div id="masonry">
        <!--瀑布流列-->
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
        <div class="masonry-column-container">
            <div class="masonry-column"></div>
        </div>
    </div>
    <script>
        // 包装一个简单的随机数
        function random(start, end) {
            return Math.random() * (end - start) + start;
        }
        
        // 在瀑布流中添加一个元素
        function add_new() {
            let masonry = document.getElementById("masonry");
            let columns = masonry.querySelectorAll(".masonry-column");
            let minHeightColumn = columns[0];

            // 拿到高度最低的列
            columns.forEach(ele => {
                if (ele.scrollHeight < minHeightColumn.scrollHeight) {
                    minHeightColumn = ele;
                }
            });

			// 创建一个新元素 (设置高度, 背景颜色)
            let new_item = document.createElement('div');
            new_item.classList.add('item');
            new_item.style.height = `${Math.random() * 200 + 70}px`;
            new_item.style.backgroundColor = `rgb(${random(0, 255)},${random(0, 255)},${random(0, 255)})`

			// 在高度最低的列中添加元素
            minHeightColumn.appendChild(new_item);
        }

        var masonry_loop;
        function start_loop() {
            masonry_loop = setInterval(add_new, 50);
        }

        function stop_loop() {
            clearInterval(masonry_loop);
        }
    </script>
</body>
</html>
```


CSS:

```css
/* 瀑布流容器 */
#masonry {
    margin: 0 auto;
    width: 80vw;       /* 居中 */
    display: grid;     /* 网格布局 */
    grid-template-columns: repeat(7, 1fr);   /* 总共 7 列 */
    gap: 10px;                               /* 间距 10px */
}

/* 指定列是相对位置 (其中的元素可以相对列来定位) */
#masonry .masonry-column {
    position: relative;
}

/* 限制内容的宽度占满列, 加上边距, 圆角 */
#masonry .item {
    width: 100%;
    margin-top: 10px;
    border-radius: 1em;
}
```


---

在 CodePen 上查看: [Simple Masonry](https://codepen.io/slimenull-the-bashful/pen/YzOzKmV)
