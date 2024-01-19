---
title: "Morris.js动态更新/ajax重绘"
date: 2020-08-30 12:58:00
lastmod: 2022-01-25 20:33:29
tags: ["javascript"]
---

近期在写一个项目练手的时候发现Morris的donut图表蛮好看的，但停更多年，最近一次更新是在2014年，开始折腾！

需求是Ajax动态刷新图表，网上搜索发现没有任何相关资料，官方GitHub有人问过，但没有解释。

下面是我总结的方法
### 方案一
换用其他维护完善的现代化图表

### 方案二
用Morris的donut图表举例，绘图命令是Morris.donut()，然后用指定id的div渲染，那么利用Ajax加载后，清空div，并重新绘制即可。
```javascript
function getSystemInformation() {
    let memory = $("#memory");
    $.ajax({
        url: "/",
        type: "POST",
        success: function(data) {
            memory.html("");
            Morris.Donut({
                element: memory,
                data: [{
                    value: data.Memory.percent,
                    label: "内存利用率"
                },
                {
                    value: (100 - data.Memory.percent),
                    label: "空闲内存"
                },
                ],
                labelColor: "#2e2f39",
                gridTextSize: "14px",
                colors: ["#ff407b", "#5969ff"],
                formatter: function(x) {
                    return x + "%"
                },
                resize: true
            });
        }
    });
```

缺点是没有加载动画，切换比较生硬，如果有其他方法再更新。

### 方案三（未实验）
将数据加载在一个div里，将画图指令里的resize设为true，让Morris自动读取重绘。
