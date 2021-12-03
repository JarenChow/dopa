# dopa

HTML5 canvas 2d library

# 简介

**dopa** 的出现不是为了在现已存在的众多 *canvas 2d* 开发框架中独树一帜或特立独行，它并不为了增加心智负担而生。

**dopa** 相信每种以不同方式构思的框架或库的出现，都能够很好的解决其所面对的各种问题，而 **dopa** 仅以 **让 canvas 2d 开发更简单** 为出发点，单纯又小巧得约为 *60KB*。

**dopa** 以这样一种角度为追求：

1. 最佳的性能表现和最少的内存占用，这使得它的渲染速度与原生代码几乎一致；
2. 无损的灵活性，它所具备的能力与原生 [canvas](https://simon.html5.org/dump/html5-canvas-cheat-sheet.html) 基本对应并有关键的简化与增强；
3. 直观的代码风格，通过阅读代码就知道发生了什么。

# 安装

1. `<script src="https://cdn.jsdelivr.net/npm/dopa"></script>`
2. `npm install dopa --save`

# 初体验

**dopa** 的灵活性在初始化时的体现：

```javascript
let layer = new dopa.Canvas({
  container: '#app', // 通常是 div 容器的 '#id' 或引用
  width: 300,        // 手动指定宽高
  height: 200,       // 如果不指定则适配容器宽高
  pixelRatio: 1,     // 如果不指定则适配高清屏
});
```

如果到这儿了觉得已经够了，那么：

```javascript
let ctx = layer.context;       // 直接接管绘图上下文
ctx.fillRect(50, 50, 100, 50); // 回到原生开发模式
```

![](https://cdn.jsdelivr.net/gh/JarenChow/ImageHosting@master/image/dopa/one.png)

如果觉得这还不够，还有兴趣探索的话，那么有一种更好的方式：

```javascript
let rect = layer.create('rect', { // 使用 layer 创建矩形
  x: 150,
  y: 100,                         // 设置矩形的坐标
  width: 100,
  height: 50                      // 设置矩形的宽高
});
rect.fill();                      // 矩形自身执行绘制
```

![](https://cdn.jsdelivr.net/gh/JarenChow/ImageHosting@master/image/dopa/two.png)

虽然似乎写了很多额外代码，但是好处立马体现：

```javascript
rect.fillStyle = 'orange';   // 设置矩形的填充样式
rect.rotation = Math.PI / 4; // 设置矩形的旋转角度
rect.fill();
```

![](https://cdn.jsdelivr.net/gh/JarenChow/ImageHosting@master/image/dopa/three.png)

# 示例

1. [dopa-corner](https://jarenchow.github.io/dopa-corner)
2. [dopa-stats](https://jarenchow.github.io/dopa-stats)

# 源代码

有需要的话会开源的，其实 **dopa** 仅作为 *canvas* 浅层的面向对象封装框架，和使用原生 *canvas api* 开发并没有本质的区别，对其有兴趣的童鞋欢迎讨论。
