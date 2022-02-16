# [dopa](https://github.com/jarenChow/dopa)

HTML5 canvas 2d library

# 目录

1. [简介](#简介)
2. [安装](#安装)
3. [初体验](#初体验)
4. [示例](#示例)
5. [结构](#结构)
6. [文档](#文档)

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
let layer = new dopa.Canvas({ // 新建画布 layer
  container: '#app',          // 通常是 div 容器的 '#id' 或引用
  width: 300,                 // 手动指定画布的宽高
  height: 200,                // 如果不指定则适配容器宽高
  pixelRatio: 1,              // 如果不指定则适配高清屏
});
```

如果到这儿了觉得已经够了，那么：

```javascript
let ctx = layer.context;       // 直接接管绘图上下文
ctx.fillRect(50, 50, 100, 50); // 回到原生开发模式
```

![](https://cdn.jsdelivr.net/gh/JarenChow/ImageHosting@master/image/dopa/one.png)

> 原生 canvas 面向过程的开发：在 50, 50 坐标处，绘制一个宽 100，高 50 的矩形。

如果觉得这还不够，还有兴趣探索的话，那么有一种更好的方式：

```javascript
let rect = layer.create('rect', { // 使用画布 layer 创建矩形
  x: 150,
  y: 100,                         // 设置矩形的坐标
  width: 100,
  height: 50                      // 设置矩形的宽高
});
rect.fill();                      // 矩形自身执行绘制
```

![](https://cdn.jsdelivr.net/gh/JarenChow/ImageHosting@master/image/dopa/two.png)

> dopa 以图形为对象的开发：创建一个矩形，坐标 150, 100，宽 100，高 50，矩形执行绘制。

对比过程化地绘制一个矩形，虽然似乎写了一些额外代码，但是好处立马体现：

```javascript
rect.fillStyle = 'orange';   // 单独设置矩形的填充样式
rect.rotation = Math.PI / 4; // 单独设置矩形的旋转角度
rect.fill();                 // 矩形再次执行绘制
```

![](https://cdn.jsdelivr.net/gh/JarenChow/ImageHosting@master/image/dopa/three.png)

> 令人激动的是，创建的形状之间互不影响，持有各自独立的属性和公共的方法！

# 示例

1. [dopa-corner](https://JarenChow.github.io/dopa-corner)：[tholman/github-corners](https://github.com/tholman/github-corners) 的 dopa 实现
2. [dopa-stats](https://JarenChow.github.io/dopa-stats)：[mrdoob/stats.js](https://github.com/mrdoob/stats.js) 的 dopa 实现
3. [dopa-benchmark](https://JarenChow.github.io/dopa/test/benchmark.html)：原生 API、ZRender 以及 Dopa 的渲染性能测试
4. [loading](https://JarenChow.github.io/dopa/test/loading.html)：Windows 系统更新动画

# 结构

> \! 为常用内容，千里之行，始于此处；
> \# 为不可见内容，表示私有、抽象，一般用不上；
> 其他为拓展内容，可用可忽视，层级结构表示继承。

- [util](#util)，工具对象
- [\!ease](#ease)，缓动函数对象
- [\#Alpha](#alpha)
  - [\!Rgb](#rgb)，三原色
  - [Hsl](#hsl)
  - [Lab](#lab)
- [\#Animate](#animate)
  - [Animation](#animation)
    - [\!Canvas](#canvas)，画布
  - [\!Animator](#animator)，子动画
- [Point](#point)
  - [ImageData](#imageData)
  - [\!Group](#group)，组
    - [Shape](#shape)
      - [\!Rect](#rect)，矩形
        - [Grid](#grid)
        - [RoundRect](#roundrect)
        - [\!Image](#image)，图片
        - [Wave](#wave)
      - [\!Text](#text)，文字
      - [\!Arc](#arc)，圆弧
        - [Sector](#sector)
        - [Ring](#ring)
        - [\!Ellipse](#ellipse)，椭圆
      - [Isogon](#isogon)
        - [Star](#star)
        - [Koch](#koch)
      - [\!Line](#line)，线段
        - [Link](#link)
          - [\!Curve](#curve)，贝塞尔曲线
      - [Triangle](#triangle)
        - [Droplet](#droplet)
        - [Heart](#heart)
        - [Pin](#pin)
      - [\!Path](#path)，路径
      - [PolyShape](#polyshape)
        - [\#PolylineLike](#polylinelike)
          - [Polyline](#polyline)
          - [Bezier](#bezier)
          - [\#PolygonLike](#polygonlike)
            - [Polygon](#polygon)
            - [Rectangle](#rectangle)
            - [Circle](#circle)
            - [SuperEllipse](#superellipse)
        - [SmoothLine](#smoothline)
- [Event](#event)

# 文档

## util

工具对象，包含常用函数。

#### noop(): this;

空函数，其值为 `function () { return this; }`

#### extends(SubType: Function, SuperType: Function, ...Interface?: PropertyDescriptorMap): void;

继承函数，使得 `SubType` 继承于 `SuperType` 且实现 `...Interface`。

继承基于原型链，将创建 `SuperType.prototype` 副本并赋值给 `SubType.prototype`。

其中 `...Interface` 类型为 `PropertyDescriptorMap` 对象，相当于 `Object.defineProperties(SubType, PropertyDescriptorMap)`。

##### 参数

- SubType，子类构造函数
- SuperType，父类构造函数
- ...Interface，属性描述符对象

##### 示例

```javascript
function SuperClass(name) {
  this.name = name;
}

SuperClass.prototype.getName = function () {
  return this.name;
};

function SubClass(name, age) {
  SuperClass.call(this, name);
  this.age = age;
}

dopa.util.extends(SubClass, SuperClass, {
  getAge: {
    value: function () {
      return this.age;
    }
  }
});

let sub = new SubClass('dopa', 4);
console.log(sub.getName()); // output: dopa
console.log(sub.getAge());  // output: 4
```

#### measureText(text: string, font: string, align: string, baseline: string, direction: string): TextMetrics;

测量文本 `text` 处于 `font`/`align`/`baseline`/`direction` 样式下的 `TextMetrics` 对象。

##### 参数

- text，待测量文本，如 'Hello Dopa'
- font，字体，如 '12px sans-serif'
- align，对齐方式，如 'center'
- baseline，基线，如 'middle'
- direction，方向，如 'ltr'

##### 示例

```javascript
let metrics = dopa.util.measureText(
  'Hello Dopa',
  '12px sans-serif',
  'center',
  'middle',
  'ltr'
);
console.log(metrics); // output: { ... }
```

#### measureTextWidth(text: string, font: string): number;

测量文本 `text` 处于 `font` 字体下的宽度。

##### 参数

- text，待测量文本，如 'Hello Dopa'
- font，字体，如 '12px sans-serif'

##### 示例

```javascript
let width = dopa.util.measureTextWidth('Hello Dopa', '12px san-serif');
console.log(width); // output: 64.552734375
```

#### loadImage(src: string, callback: (image: HTMLImageElement, success: boolean) => void, thisArg?: any): void;

从 `src` 地址加载一张图片，加载后会触发 `callback` 回调。

##### 参数

- src，图片路径，如 'https://mdn.mozillademos.org/files/1429/Canvas_earth.png'
- callback，加载成功或失败后触发的回调函数，回调图片对象以及是否成功加载
- thisArg，执行回调函数时的上下文对象

##### 示例

```javascript
let src = 'https://mdn.mozillademos.org/files/1429/Canvas_earth.png';
dopa.util.loadImage(src, (image, success) => {
  console.log(image, success);
});
```

#### createLinearGradient(x1: number, y1: number, x2: number, y2: number): CanvasGradient;

创建线性渐变，与使用绘图上下文 `context.createLinearGradient(x1, y1, x2, y2);` 等效，此代码组织方式只是解耦了具体的绘图上下文 `context` 与渐变对象，以下几个函数同理。

##### 参数

- x1，线性渐变起始点 x
- y1，线性渐变起始点 y
- x2，线性渐变结束点 x
- y2，线性渐变结束点 y

#### createRadialGradient(x1: number, y1: number, r1: number, x2: number, y2: number, r2: number): CanvasGradient;

创建径向渐变。

##### 参数

- x1，径向渐变起始点 x
- y1，径向渐变起始点 y
- r1，径向渐变起始半径 radius
- x2，径向渐变结束点 x
- y2，径向渐变结束点 y
- r2，径向渐变结束半径 radius

#### createConicGradient(startAngle: number, x: number, y: number): CanvasGradient;

创建锥形（角向）渐变，目前多数浏览器尚未实现，如需在 `chrome` 中使用，需开启 `chrome://flags/#new-canvas-2d-api`。

##### 参数

- startAngle，锥形渐变起始角度，规范中定义 -PI / 2 为起始角度，chrome 中以 0 为起始角度
- x，锥形渐变中心点 x
- y，锥形渐变中心点 y

#### createPattern(image: CanvasImageSource, repetition?: string): CanvasPattern;

使用指定 `image` 以 `repetition` 方式，创建填充模式。

##### 参数

- image，作为重复图像的源对象
- repetition，指定如何重复图像，默认值 'repeat'

#### normalizeNumber(number: number, range: number): number;

将一个数值 `number` 标准化到范围 `range` 中。

##### 参数

- number，将要标准化的数值
- range，目标值范围

##### 示例

```javascript
let normalize = dopa.util.normalizeNumber, PI = Math.PI;
console.log(normalize(-PI, PI * 2));     // output: PI
console.log(normalize(-PI / 2, PI * 2)); // output: PI * 3 / 2
console.log(normalize(PI / 2, PI * 2));  // output: PI / 2
console.log(normalize(PI * 3, PI * 2));  // output: PI
```

#### limitNumber(number: number, min: number, max: number): number;

将一个数值 `number` 限制到范围 `min` ~ `max` 中。

##### 参数

- number，将要限制的数值
- min，目标数值的最小值
- max，目标数值的最大值

##### 示例

```javascript
let limit = dopa.util.limitNumber;
console.log(limit(5, 0, 10));  // output: 5
console.log(limit(-5, 0, 10)); // output: 0
console.log(limit(15, 0, 10)); // output: 10
```

#### isNotAroundZero(value: number): boolean;

判断数值 `value` 是否接近 `0`，默认阈值取值为 `Math.pow(2, -14)`。

##### 参数

- value，将要判断的数值

##### 示例

```javascript
let isNotZero = dopa.util.isNotAroundZero;
console.log(isNotZero(0));      // output: false
console.log(isNotZero(1e-5));   // output: false
console.log(isNotZero(1e-4));   // output: true
console.log(isNotZero(1));      // output: true
```

#### saveURL(url: string, filename: string): void;

将 `url` 保存为本地文件。

##### 参数

- url，将要保存的 url，通常是 base64 字符串，如 canvas.toDataURL()
- filename，保存为的文件名

#### randInt(start: number, end?: number, containsEnd?: boolean): number;

随机一个整数。

##### 参数

- start，整数，起点，若不存在 end，则起点为 0，终点为 start
- end，整数，终点
- containsEnd，结果值是否要包含终点

##### 示例

```javascript
let rand = dopa.util.randInt;
console.log(rand(5));           // output: 0/1/2/3/4
console.log(rand(3, 5));        // output: 3/4
console.log(rand(3, 5, true));  // output: 3/4/5
console.log(rand(-2, 2, true)); // output: -2/-1/0/1/2
```

#### lerp(start: number, end: number, ratio: number): number;

插值函数，相当于 `return start + (end - start) * ratio;`。

##### 参数

- start，插值起点
- end，插值终点
- ratio，插值进度，通常处于 0 ~ 1 之间

## ease

`ease` 为缓动函数对象。

### 方法

`ease` 对象含有 `31` 个常见的动画缓动函数与 `1` 个贝塞尔缓动函数构造器，其中参数 `t` 为动画进度，值范围 \[0, 1\]。

1. linear(t: number): number;
2. sinIn(t: number): number;
3. sinOut(t: number): number;
4. sinInOut(t: number): number;
5. quadIn(t: number): number;
6. quadOut(t: number): number;
7. quadInOut(t: number): number;
8. cubicIn(t: number): number;
9. cubicOut(t: number): number;
10. cubicInOut(t: number): number;
11. quartIn(t: number): number;
12. quartOut(t: number): number;
13. quartInOut(t: number): number;
14. quintIn(t: number): number;
15. quintOut(t: number): number;
16. quintInOut(t: number): number;
17. expIn(t: number): number;
18. expOut(t: number): number;
19. expInOut(t: number): number;
20. circleIn(t: number): number;
21. circleOut(t: number): number;
22. circleInOut(t: number): number;
23. backIn(t: number): number;
24. backOut(t: number): number;
25. backInOut(t: number): number;
26. elasticIn(t: number): number;
27. elasticOut(t: number): number;
28. elasticInOut(t: number): number;
29. bounceIn(t: number): number;
30. bounceOut(t: number): number;
31. bounceInOut(t: number): number;
32. bezier(x1, y1, x2, y2): Function (t: number): number;

```javascript
let quad = dopa.ease.quadOut; // 动画先快后慢
for (let t = 0; t <= 1; t += 0.125) {
  console.log(
    '动画进度', t.toFixed(3),
    '缓动进度', quad(t).toFixed(6)
  );
}
```

## Alpha

透明度

### 构造函数

#### Alpha(alpha);

### 属性

#### alpha: number;

透明度通道，值范围 \[0, 255\]，默认值 `0`

### 方法

#### fromString(alpha: string): this;

解析 `alpha` 字符串为 `Alpha` 对象属性。

##### 参数

- alpha，字符串，如 '0.5'，将被解析为 127.5

#### toString(): string;

将 `Alpha` 对象转换成字符串。

## Rgb

三原色

### 构造函数

#### Rgb(red, green, blue, alpha) extends Alpha;

### 属性

#### red: number;

红色，值范围 \[0, 255\]，默认值 `0`

#### green: number;

绿色，值范围 \[0, 255\]，默认值 `0`

#### blue: number;

蓝色，值范围 \[0, 255\]，默认值 `0`

### 方法

#### fromString(rgb: string): this;

解析 `rgb` 字符串为 `Rgb` 对象属性。

##### 参数

- rgb，字符串，如 'rgb(127, 255, 64)'

##### 示例

```javascript
let rgb = new dopa.Rgb();
rgb.fromString('rgb(127, 255, 64)');
rgb.green = 0;
console.log(rgb.toString()); // output: rgb(127, 0, 64)
```

#### fromHex(hex: string): this;

解析 `hex` 字符串为 `Rgb` 对象属性。

##### 参数

- hex，字符串，如 '#7fff40'

##### 示例

```javascript
let rgb = new dopa.Rgb().fromHex('#7fff40');
```

#### fromHsl(hsl: Hsl): this;

解析 `Hsl` 对象为 `Rgb` 对象属性。

##### 参数

- hsl，Hsl 对象

##### 示例

```javascript
let rgb = new dopa.Rgb();
let hsl = new dopa.Hsl(30, 1, 0.5);
rgb.fromHsl(hsl);
console.log(rgb.toString()); // output: rgb(255, 128, 0)
```

#### fromLab(lab: Lab): this;

解析 `Lab` 对象为 `Rgb` 对象属性。

##### 参数

- lab，Lab 对象

##### 示例

```javascript
let rgb = new dopa.Rgb();
let lab = new dopa.Lab(80, 10, -10);
rgb.fromLab(lab);
console.log(rgb.toString()); // output: rgb(211, 192, 217)
```

#### inverseCompanding(): this;

转换伽马编码 `RGB` 模式下的 `Rgb` 对象为线性光 `sRGB` 模式。

##### 示例

```javascript
let rgb = new dopa.Rgb(127, 255, 64);
rgb.inverseCompanding();
console.log(rgb.red);   // output: 0.21223...
console.log(rgb.green); // output: 1
console.log(rgb.blue);  // output: 0.05126...
```

#### companding(): this;

转换线性光 `sRGB` 模式下的 `Rgb` 对象为伽马编码 `RGB` 模式。

##### 示例

```javascript
let rgb = new dopa.Rgb(0.2, 0.5, 0.8);
rgb.companding();
console.log(rgb.red);   // output: 123.55494...
console.log(rgb.green); // output: 187.51603...
console.log(rgb.blue);  // output: 231.11459...
```

#### toString(alpha: boolean): string;

将 `Rgb` 对象转换成 `rgb` 字符串。

##### 参数

- alpha，布尔值，表示是否包含 alpha 通道

##### 示例

```javascript
let rgb = new dopa.Rgb(127, 255, 64, 127);
rgb.green -= 51;
console.log(rgb.toString());     // output: rgb(127, 204, 64)
console.log(rgb.toString(true)); // output: rgba(127, 204, 64, 0.49803...)
```

#### toHex(alpha: boolean): string;

将 `Rgb` 对象转换成 `hex` 字符串。

##### 参数

- alpha，布尔值，表示是否包含 alpha 通道

##### 示例

```javascript
let rgb = new dopa.Rgb(127, 255, 64, 127);
rgb.green -= 51;
console.log(rgb.toHex());     // output: #7fcc40
console.log(rgb.toHex(true)); // output: #7fcc407f
```

## Hsl

色彩空间

### 构造函数

#### Hsl(hue, saturation, lightness, alpha) extends Alpha;

### 属性

#### hue: number;

色相，值范围 \[0, 360\]，默认值 `0`

#### saturation: number;

饱和度，值范围 \[0, 1\]，默认值 `1`

#### lightness: number;

亮度，值范围 \[0, 1\]，默认值 `0.5`

### 方法

#### fromString(hsl: string): this;

解析 `hsl` 字符串为 `Hsl` 对象属性。

##### 参数

- hsl，字符串，如 'hsl(30, 100%, 50%)'

##### 示例

```javascript
let hsl = new dopa.Hsl();
hsl.fromString('hsl(30, 100%, 50%)');
hsl.lightness -= 0.2;
console.log(hsl.toString()); // output: hsl(30, 100%, 30%)
```

#### fromRgb(rgb: Rgb): this;

解析 `Rgb` 对象为 `Hsl` 对象属性。

##### 参数

- rgb，Rgb 对象

##### 示例

```javascript
let hsl = new dopa.Hsl();
let rgb = new dopa.Rgb(255, 128, 0);
hsl.fromRgb(rgb);
console.log(hsl.toString()); // output: hsl(30.11764..., 100%, 50%)
```

#### toString(alpha: boolean): string;

将 `Hsl` 对象转换成 `hsl` 字符串。

##### 参数

- alpha，布尔值，表示是否包含 alpha 通道

## Lab

色彩空间

### 构造函数

#### Lab(lightness, a, b, alpha) extends Alpha;

### 属性

#### lightness: number;

明度，值范围 \[0, 100\]，默认值 `0`

#### a: number;

位置，值范围 \[-128, 127\]，默认值 `0`

#### b: number;

位置，值范围 \[-128, 127\]，默认值 `0`

### 方法

#### fromString(lab: string): this;

解析 `lab` 字符串为 `Lab` 对象属性。

##### 参数

- lab，字符串，如 'lab(65, 45, 75)'

#### fromRgb(rgb: Rgb): this;

解析 `Rgb` 对象为 `Lab` 对象属性。

##### 参数

- rgb，Rgb 对象

##### 示例

```javascript
let lab = new dopa.Lab();            // 准备提亮一个颜色
let rgb = new dopa.Rgb(255, 128, 0); // 橙色
lab.fromRgb(rgb);                    // 转换成 Lab
lab.lightness += 10;                 // 提亮明度
rgb.fromLab(lab);                    // 转换回 Rgb
console.log(rgb + '');               // output: rgb(255, 155, 43)
```

#### toString(alpha: boolean): string;

将 `Lab` 对象转换成字符串。

## Animate

私有、抽象的动画基类

### 构造函数

#### Animate(duration, interval);

### 属性

#### duration: number;

动画持续时长，不设置则默认为一个极小值，在下一帧直接执行完毕。

#### interval: number;

动画帧间隔时长，不设置则默认为 0，表示以最高帧率运行。

### 方法

#### start(): this;

启动动画。

#### resume(): this;

恢复动画。

#### pause(): this;

暂停动画。

#### stop(toEnd: boolean): this;

停止动画。

##### 参数

- toEnd，布尔值，表示动画停止时是否直接执行到动画终点，仅对子动画 Animator 有效

#### isRunning(): boolean;

判断动画是否正在运行。

#### isStopped(): boolean;

判断动画是否处于停止状态。

#### isPaused(): boolean;

判断动画是否处于暂停状态。

#### onstart(): void;

动画 `start()` 启动时触发此回调。

#### onresume(): void;

动画 `resume()` 恢复时触发此回调。

#### onupdate(ratio, timestamp, interval): void;

动画 `运行` 时触发此回调。

##### 参数

- ratio，动画进度，值范围 (0, 1]
- timestamp，动画时间戳，值范围 (0, duration]
- interval，动画间隔时间，仅对主动画 Animation 有效

#### onpause(): void;

动画 `pause()` 暂停时触发此回调。

#### onstop(): void;

动画 `stop()` 停止时触发此回调。

#### onfinish(forward): void;

动画 `结束` 时触发此回调。

##### 参数

- forward，布尔值，动画方向

## Animation

主动画循环

### 构造函数

#### Animation(duration, interval) extends Animate;

### 方法

#### add(...animators: Animator): this;

添加子动画对象。

##### 参数

- animators，多个子动画对象

#### remove(...animators: Animator): this;

移除子动画对象。

##### 参数

- animators，多个子动画对象

## Canvas

画布（图层）

### 构造函数

#### Canvas(container, width, height, pixelRatio, duration, interval) extends Animation;

`container` 可以是容器 `'#id'` 或 `{container, width, height, pixelRatio, duration, interval}` 配置对象。

### 属性

#### readonly container: Element;

只读属性容器 `container`，在初始化时指定，值为 `wrapper.parentNode`。

#### readonly wrapper: Element;

只读属性包装容器 `wrapper`，一般初始化时自动生成，可实现重叠多张画布。

#### readonly canvas: HTMLCanvasElement;

只读属性画布 `canvas`，呈现内容的载体。

#### readonly context: CanvasRenderingContext2D;

只读属性绘图上下文 `context`，绘制内容的句柄。

#### width: number;

宽度，不设置则自动适配容器 `container` 的宽度。

#### height: number;

高度，不设置则自动适配容器 `container` 的高度。

#### pixelRatio: number;

像素比例，不设置则自动适配设备像素比例 `devicePixelRatio`。

#### readonly visible: boolean;

只读属性 `visible` 表示 `canvas` 是否可见。

#### cursor: string;

鼠标样式，不设置则默认为 `''`。

### 方法

#### resize(width: number, height: number): this;

调整大小的方法，单独调整此 `Canvas` 大小一次，通常用于超过界面大小的截图。此方法也会由 `ResizeObserver` 触发。

##### 参数

- width，宽度，不设置则默认适配容器宽度
- height，高度，不设置则默认适配容器高度

#### render(immediate: boolean): this;

执行渲染的方法，如果具备 `duration`，则会启动动画。

##### 参数

- immediate，布尔值，表示是否立即执行渲染不必等待下一帧

#### clearRect(x: number, y: number, width: number, height: number): this;

清空画布的方法。

##### 参数

- x，清空起点 x，通常设为 0
- y，清空起点 y，通常设为 0
- width，清空区域宽度 width，通常设为画布宽度
- height，清空区域高度 height，通常设为画布高度

#### destroy(): this;

销毁 `Canvas` 的方法。

#### on(type: string, listener: (Event | undefined) => void): this;

添加事件监听器的方法，可添加 `WindowEventMap` 中的多数监听器以及 `内置回调` 的监听器。

##### 参数

- type，类型，如 'render'/'click'
- listener，监听器函数

#### create(Ctor: string | Function, source?: object | any, options?: object | any): Ctor;

使用 `new` 操作符创建一个以 `Ctor` 作为构造函数的对象并添加给 `Canvas`。

##### 参数

- Ctor，表示构造函数的字符串或构造函数，如 'rect' 等价于 dopa.Rect
- source | options: 如果是对象，则会浅拷贝给创建的对象，否则执行构造函数的 apply() 方法

#### oncreate(): void;

`Canvas` 对象创建时触发此回调。

#### onresize(): void;

`Canvas` 对象创建时以及容器 `container` 大小变动后触发此回调。

#### onrender(): void;

`Canvas` 对象创建时以及动画运行时触发此回调。

#### onvisibilitychange(): void;

`Canvas` 对象显示在视窗中、移出视窗时、浏览器显示及隐藏时触发此回调。

#### ondestroy(): void;

`Canvas` 对象执行 `destroy` 销毁时触发此回调。

## Animator

子动画循环

### 构造函数

#### Animator(duration, interval) extends Animate;

### 属性

#### animation: Animation;

主动画句柄，子动画依赖于主动画才能运行，主动画可添加多个子动画同时运行互不干扰。

### 方法

#### reverse(): this;

反转动画。

#### onreverse(): void;

动画 `reverse()` 反转时触发此回调。

## Point

点

### 构造函数

#### Point(x, y);

### 属性

#### x: number;

坐标 `x`，默认值 `0`。

#### y: number;

坐标 `y`，默认值 `0`。

### 方法

#### set(x: number, y: number): this;

同时设置 `x, y` 坐标。

##### 参数

- x，待设置的坐标 x
- y，带设置的坐标 y

#### skew(skewX: number, skewY: number): this;

错切（斜切）坐标。

##### 参数

- skewX，x 轴方向的错切度数，以弧度表示
- skewY，y 轴方向的错切度数，以弧度表示

#### scale(scaleX: number, scaleY: number): this;

缩放坐标。

##### 参数

- scaleX，x 轴方向的缩放倍数
- scaleY，y 轴方向的缩放倍数

#### rotate(angle: number): this;

旋转坐标。

##### 参数

- angle，旋转角度，以弧度表示

#### translate(x: number, y: number): this;

平移坐标。

##### 参数

- x，x 轴方向的平移量
- y，y 轴方向的平移量

#### transform(a: number | DOMMatrix2DInit, b?: number, c?: number, d?: number, e?: number, f?: number): this;

变换坐标。

##### 参数

- a，矩阵数值 m11，或 {a, b, c, d, e, f} 矩阵对象
- b，矩阵数值 m12
- c，矩阵数值 m21
- d，矩阵数值 m22
- e，矩阵数值 m41
- f，矩阵数值 m42

#### unit(): this;

缩放成单位长度 `1`，可视为处理成单位向量。

#### inverse(): this;

反转坐标。

#### vertical(): this;

顺时针旋转 `90` 度。

#### add(point: Point): this;

加，可视为向量相加。通常用来平移对象。

##### 参数

- point，另一个点

#### subtract(point: Point): this;

减，可视为向量相减。

##### 参数

- point，另一个点

## ImageData

`window.ImageData` 的包装器，对 `devicePixelRatio` 敏感。

### 构造函数

#### ImageData(x, y, width_or_imageData, height_or_width, dirtyX, dirtyY, dirtyWidth, dirtyHeight) extends Point;

### 属性

#### dirtyX: number | null;

脏矩形起始点 x，默认值 `undefined`。

#### dirtyY: number | null;

脏矩形起始点 y，默认值 `undefined`。

#### dirtyWidth: number | null;

脏矩形宽度 width，默认值 `undefined`。

#### dirtyHeight: number | null;

脏矩形高度 height，默认值 `undefined`。

#### imageData: window.ImageData;

内部数据源 `imageData`。

#### readonly width: number;

只读属性宽度。

#### readonly height: number;

只读属性高度。

#### readonly left: number;

只读属性左侧边界。

#### readonly top: number;

只读属性上侧边界。

#### readonly right: number;

只读属性右侧边界。

#### readonly bottom: number;

只读属性下侧边界。

### 方法

#### getImageData(x?: number, y?: number, width_or_imageData: number | ImageData, height_or_width?: number): this;

获取 `window.ImageData` 数据，方法因参数不同，有以下 `4` 种执行方式：

1. (x, y, width, height)，获取区域中的 window.ImageData;
2. (null, null, width, height)，创建具备宽高的 window.ImageData;
3. (x?, y?, imageData, null)，将 imageData 赋值给对象;
4. (x?, y?, imageData, width)，将 imageData 以宽度复制，并赋值给对象;

##### 参数

- x，获取数据的起点坐标 x
- y，获取数据的起点坐标 y
- width_or_imageData，获取数据的宽度或 ImageData
- height_or_width，获取数据的高度或宽度，后者用于复制

#### toDataURL(type?: string, quality?: number): string;

将 `ImageData` 对象转换成 `DataURL` 字符串。

##### 参数

- type，类型，默认为 PNG 格式，如 'image/png'
- quality，品质，默认为 0.92

#### toBlob(callback: (blob: Blob | null) => void, type?: string, quality?: number): this;

将 `ImageData` 对象转换成 `Blob` 对象，完成后以 `callback` 回调。

##### 参数

- callback，回调函数，通常回调 Blob 对象
- type，类型，默认为 PNG 格式，如 'image/png'
- quality，品质，默认为 0.92

#### fill(): this;

填充绘制。

#### isPointInPath(x: number, y: number): boolean;

判断点 `x, y` 是否处于 `ImageData` 内部。

##### 参数

- x，点 x 坐标
- y，点 y 坐标

#### getPixelIndex(x: number, y: number): number;

获取点 `x, y` 位于 `ImageData.data` 里的 `index` 值。

##### 参数

- x，点 x 坐标
- y，点 y 坐标

#### forEach(callback: (data, index, x, y) => void, thisArg?: any, x?: number, y?: number, width?: number, height?: number): this;

遍历 `ImageData.data`。

##### 参数

- callback，回调函数，回调供处理的 data、当前 index 及像素处于界面中的 x, y 坐标
- thisArg，执行回调函数时的上下文对象
- x，遍历起始坐标 x
- y，遍历起始坐标 y
- width，遍历宽度
- height，遍历高度

#### mosaic(size?: number, x?: number, y?: number, width?: number, height?: number): this;

马赛克处理 `ImageData`。

##### 参数

- size，马赛克的矩形大小，正整数，默认值 1，表示无效果
- x，遍历起始坐标 x
- y，遍历起始坐标 y
- width，遍历宽度
- height，遍历高度

#### transparencyDisposal(threshold?: number, x?: number, y?: number, width?: number, height?: number): this;

透明度处理，将阈值 `threshold` 之上像素点的 `alpha` 通道设置为 `0`。

##### 参数

- ratio，阈值，值范围 \[0, 1\]，0 表示仅保留黑色，1 表示仅处理白色，默认值 1
- x，遍历起始坐标 x
- y，遍历起始坐标 y
- width，遍历宽度
- height，遍历高度

#### reverseColor(x?: number, y?: number, width?: number, height?: number): this;

反转颜色。

##### 参数

- x，遍历起始坐标 x
- y，遍历起始坐标 y
- width，遍历宽度
- height，遍历高度

#### greyProcessing(x?: number, y?: number, width?: number, height?: number): this;

灰度处理。

##### 参数

- x，遍历起始坐标 x
- y，遍历起始坐标 y
- width，遍历宽度
- height，遍历高度

#### blackWhite(threshold?: number, x?: number, y?: number, width?: number, height?: number): this;

黑白二值化。

##### 参数

- threshold，阈值，值范围 \[0, 1\]，默认值 127 / 255
- x，遍历起始坐标 x
- y，遍历起始坐标 y
- width，遍历宽度
- height，遍历高度

#### relief(): this;

浮雕滤镜。

#### sunGlass(): this;

墨镜滤镜。

#### frostedGlass(size?: number): this;

毛玻璃滤镜。

##### 参数

- size，毛玻璃随机像素的矩形大小，正整数，默认值 1，表示无效果

## Group

组，可作为其他组或形状的父级。

需要注意的是，区别于点的变形方法是改变点的坐标 `x, y`，组的变形是依据 `originX, originY` 而改变的组本身的缩放、旋转及错切。

### 构造函数

#### Group(originX, originY) extends Point;

### 属性

#### originX: number;

变形依据的原点 x 坐标，默认值 `0`。

#### originY: number;

变形依据的原点 y 坐标，默认值 `0`。

#### group: Group | null;

设置组的父级 `group`，父对象的变形和样式均会影响子对象。

#### scaleX: number;

x 轴方向的缩放倍数。

#### scaleY: number;

y 轴方向的缩放倍数。

#### skewX: number;

x 轴方向的错切度数，以弧度表示。

#### skewY: number;

y 轴方向的错切度数，以弧度表示。

#### rotation: number;

旋转角度，以弧度表示。

#### readonly a: number;

矩阵数值 m11。

#### readonly b: number;

矩阵数值 m12。

#### readonly c: number;

矩阵数值 m21。

#### readonly d: number;

矩阵数值 m22。

#### readonly e: number;

矩阵数值 m41。

#### readonly f: number;

矩阵数值 m42。

#### alpha: number;

透明度，值范围 \[0, 1\]，`0` 表示透明，`1` 表示不透明，默认值 `1`。

#### compositeOperation: string;

图形合成运算方式，默认值 `'source-over'`。

#### filter: string;

CSS 滤镜，默认值 `'none'`。

#### shadowBlur: number;

阴影模糊，默认值 `0`。

#### shadowColor: string;

阴影颜色，默认值 `'rgba(0, 0, 0, 0)'`。

#### shadowOffsetX: number;

阴影 x 轴偏移量，默认值 `0`。

#### shadowOffsetY: number;

阴影 y 轴偏移量，默认值 `0`。

#### fillStyle: string;

填充样式，默认值 `'#000000'`。

#### strokeStyle: string;

描边样式，默认值 `'#000000'`。

#### lineDash: Array;

虚线样式，默认值 `[]`。

#### lineDashOffset: number;

虚线偏移量，默认值 `0`。

#### lineWidth: number;

线宽，默认值 `1`。

#### lineCap: string;

线段两端样式，默认值 `'butt'`。

#### lineJoin: string;

线段连接处样式，默认值 `'miter'`。

#### miterLimit: number;

线段连接处长度限制值，默认值 `10`。

#### smoothingEnabled: boolean;

是否开启图像平滑，默认值 `true`。

#### smoothingQuality: string;

图像平滑质量，默认值 `'low'`。

#### font: string;

文字字体，默认值 `'10px sans-serif'`。

#### align: string;

文字对齐方式，默认值 `'start'`。

#### baseline: string;

文字基线类型，默认值 `'alphabetic'`。

#### direction: string;

文字方向，默认值 `'ltr'`。

### 方法

#### drag(movementX: number, movementY: number): this;

拖曳组的方法。

##### 参数

- movementX，x 轴方向拖曳距离
- movementY，y 轴方向拖曳距离

#### zoom(zooming: number, x: number, y: number): this;

缩放组的方法。

##### 参数

- zooming，缩放倍数
- x，缩放依据的 x 坐标
- y，缩放依据的 y 坐标

#### updateTransform(): this;

更新组的变形，绘制时自动调用，手动调用以获取矩阵数值。

#### updateStyle(): this;

更新组的通用样式，绘制时自动调用，手动调用以获取通用样式。

#### updateFillStyle(): this;

更新组的填充样式，填充绘制时自动调用，手动调用以获取填充样式。

#### updateStrokeStyle(): this;

更新组的描边样式，描边绘制时自动调用，手动调用以获取描边样式。

#### updateImageStyle(): this;

更新组的图像样式，绘制图片时自动调用，手动调用以获取图像样式。

#### updateTextStyle(): this;

更新组的文字样式，绘制文字时自动调用，手动调用以获取文字样式。

## Shape

图形（形状），具备绘制能力，也是所有图形的父类。

### 构造函数

#### Shape(startX, startY, originX, originY) extends Group;

### 属性

#### context: CanvasRenderingContext2D;

绘图上下文句柄。

#### startX: number;

图形起始绘制点 x 坐标，默认值 `0`。

#### startY: number;

图形起始绘制点 y 坐标，默认值 `0`。

### 方法

#### fill(fillRule?: CanvasFillRule): this;

填充绘制。

##### 参数

- fillRule，填充规则，默认值 'nonzero'，可选 'evenodd'

#### stroke(): this;

描边绘制。

#### clip(fillRule?: CanvasFillRule): this;

裁剪绘制的范围，此时会向栈中推入一次状态快照。

##### 参数

- fillRule，填充规则，默认值 'nonzero'，可选 'evenodd'

#### restore(count?: number): this;

恢复裁剪操作，此时栈会推出状态快照并恢复。

##### 参数

- count，恢复状态的次数。

#### clearRect(x: number, y: number, width: number, height: number, fillRule?: CanvasFillRule): this;

图形执行裁剪方法，接着执行清空矩形区域的方法，最后执行恢复裁剪方法。

##### 参数

- x，清空起点 x，以图形起始绘制点 startX 为基准
- y，清空起点 y，以图形起始绘制点 startY 为基准
- width，清空区域宽度 width
- height，清空区域高度 height
- fillRule，填充规则，默认值 'nonzero'，可选 'evenodd'

#### rebuildPath(): void;

图形统一组建参数，重建路径的方法。通常由子类覆写，自动执行，使得 `buildPath()` 与对象解耦。

#### buildPath(context: CanvasRenderingContext2D, startX: number, startY: number): void;

图形具体进行绘制的方法。通常由子类覆写，由 `rebuildPath()` 触发调用。

##### 参数

- context，绘图上下文句柄
- startX，起始绘制点 x
- startY，起始绘制点 y
- ...，由 rebuildPath() 组建而来

#### isPointInPath(x: number, y: number): boolean;

判断点 `x, y` 是否处于图形内部。

#### isPointInStroke(x: number, y: number): boolean;

判断点 `x, y` 是否处于图形描边处。

#### isQuickInPath(x: number, y: number): boolean;

快速判断点 `x, y` 是否处于图形内部。此方法通常为粗略的判断，未实现的图形则回退为 `isPointInPath`。

#### isQuickInPath(x: number, y: number): boolean;

快速判断点 `x, y` 是否处于图形描边处。此方法通常为粗略的判断，未实现的图形则回退为 `isPointInStroke`。

#### createLinearGradient(x1: number, y1: number, x2: number, y2: number): CanvasGradient;

创建线性渐变，以图形起始绘制点为基准。

##### 参数

- x1，线性渐变起始点 x，以图形起始绘制点 startX 为基准
- y1，线性渐变起始点 y，以图形起始绘制点 startY 为基准
- x2，线性渐变结束点 x，以图形起始绘制点 startX 为基准
- y2，线性渐变结束点 y，以图形起始绘制点 startY 为基准

#### createRadialGradient(x1: number, y1: number, r1: number, x2: number, y2: number, r2: number): CanvasGradient;

创建径向渐变，以图形起始绘制点为基准。

##### 参数

- x1，径向渐变起始点 x，以图形起始绘制点 startX 为基准
- y1，径向渐变起始点 y，以图形起始绘制点 startY 为基准
- r1，径向渐变起始半径 radius
- x2，径向渐变结束点 x，以图形起始绘制点 startX 为基准
- y2，径向渐变结束点 y，以图形起始绘制点 startY 为基准
- r2，径向渐变结束半径 radius

#### createConicGradient(startAngle: number, x: number, y: number): CanvasGradient;

创建锥形（角向）渐变，以图形起始绘制点为基准，目前多数浏览器尚未实现，如需在 `chrome` 中使用，需开启 `chrome://flags/#new-canvas-2d-api`。。

##### 参数

- startAngle，锥形渐变起始角度，规范中定义 -PI / 2 为起始角度，chrome 中以 0 为起始角度
- x，锥形渐变中心点 x，以图形起始绘制点 startX 为基准
- y，锥形渐变中心点 y，以图形起始绘制点 startY 为基准

## Rect

矩形

### 构造函数

#### Rect(startX, startY, width, height, originX, originY) extends Shape;

### 属性

#### width: number;

矩形宽度，默认值 `undefined`。

#### height: number;

矩形高度，默认值 `undefined`。

#### readonly left: number;

只读属性左侧边界。

#### readonly top: number;

只读属性上侧边界。

#### readonly right: number;

只读属性右侧边界。

#### readonly bottom: number;

只读属性下侧边界。

## Grid

网格

### 构造函数

#### Grid(startX, startY, width, height, spacing, originX, originY) extends Rect;

### 属性

#### spacing: number;

网格间距，正整数，默认值 `1`。

## RoundRect

圆角矩形

### 构造函数

#### RoundRect(startX, startY, width, height, radius, originX, originY) extends Rect;

### 属性

#### radius: number;

圆角半径，非负数，默认值 `0`。

## Image

图像，`window.Image` 的包装器

### 构造函数

#### Image(startX, startY, src_or_image, originX, originY, width, height, cropX, cropY, cropWidth, cropHeight) extends Rect;

### 属性

#### cropX: number | null;

裁切起始点 x，默认值 `undefined`。

#### cropY: number | null;

裁切起始点 y，默认值 `undefined`。

#### cropWidth: number | null;

裁切宽度，默认值 `undefined`。

#### cropHeight: number | null;

裁切高度，默认值 `undefined`。

#### src: string;

图片来源字符串，如 `url` 地址或 `base64` 字符串。

#### image: CanvasImageSource;

内部图像源 `image`，属于 `CanvasImageSource` 的对象均可。

#### readonly complete: boolean;

只读属性 `complete`，表示 `Image` 是否加载完成。

### 方法

#### onload(success: boolean): void;

图片加载成功或失败后触发的回调。

##### 参数

- success，表示图片是否成功加载

#### toImageData(): window.ImageData;

转换图片为 `window.ImageData`。

#### createPattern(repetition?: string): CanvasPattern;

使用当前内部图像源 `image` 以 `repetition` 方式，创建填充模式。

##### 参数

- repetition，指定如何重复图像，默认值 'repeat'

## Wave

波浪形

### 构造函数

#### Wave(startX, startY, width, height, amplitude, method, period, phase, step, originX, originY) extends Rect;

### 属性

#### amplitude: number;

振幅，默认值 `10`。

#### method: Function;

周期函数，默认值 `Math.sin`。

#### period: number;

周期，默认值 `Math.PI * 2`。

#### phase: number;

初始相位，默认值 `0`。

#### step: number;

步进，正整数，默认值 `1`。

## Text

文字

### 构造函数

#### Text(startX, startY, text, maxWidth, originX, originY) extends Shape;

### 属性

#### text: string;

文本字符串，默认值 `''`。

#### maxWidth: number;

文本最大宽度，默认值 `undefined`。

##### 示例

```javascript
let text = layer.create('text', {
  x: 100,
  y: 100,
  text: 'Hello Dopa',
  font: '12px sans-serif'
});
text.fill();
console.log(text.width); // output: 64.55273...
console.log(text.font);  // output: 12px sans-serif
text.width = 100;
text.y += 50;
text.fill();
console.log(text.width); // output: 96.82910...
console.log(text.font)   // output: 18px sans-serif
```

#### readonly width: number;

只读属性文本宽度。

#### readonly metrics: TextMetrics;

只读属性 `metrics`，获取文本处于当前文字样式下的 `TextMetrics` 对象。

#### readonly actualBoundingBoxWidth: number;

只读属性文本包围盒宽度。

#### readonly actualBoundingBoxHeight: number;

只读属性文本包围盒高度。

#### readonly left: number;

只读属性左侧边界。

#### readonly top: number;

只读属性上侧边界。

#### readonly right: number;

只读属性右侧边界。

#### readonly bottom: number;

只读属性下侧边界。

## Arc

圆弧

### 构造函数

#### Arc(startX, startY, radius, startAngle, endAngle, anticlockwise, originX, originY) extends Shape;

### 属性

#### radius: number;

圆弧半径，默认值 `0`。

#### startAngle: number;

起始角度，默认值 `0`。

#### endAngle: number;

结束角度，默认值 `Math.PI * 2`。

#### anticlockwise?: boolean;

是否逆时针绘制圆弧，默认值 `false`。

#### readonly centerX: number;

只读属性圆心坐标 x。

#### readonly centerY: number;

只读属性圆心坐标 y。

## Sector

扇形

### 构造函数

#### Sector(startX, startY, radius, startAngle, endAngle, anticlockwise, originX, originY) extends Arc;

## Ring

圆环

### 构造函数

#### Ring(startX, startY, outerRadius, innerRadius, startAngle, endAngle, anticlockwise, originX, originY) extends Arc;

### 属性

#### outerRadius: number;

外圆半径，等价于属性 `radius`。

#### innerRadius: number;

内圆半径，默认值 `0`。

## Ellipse

椭圆

### 构造函数

#### Ellipse(startX, startY, radiusX, radiusY, startAngle, endAngle, anticlockwise, originX, originY) extends Arc;

### 属性

#### radiusX: number;

椭圆长轴半径，等价于属性 `radius`。

#### radiusY: number;

椭圆短轴半径，默认值 `0`。

## Isogon

正多边形

### 构造函数

#### Isogon(startX, startY, radius, sides, originX, originY) extends Shape;

### 属性

#### radius: number;

外接圆半径，默认值 `0`。

#### sides: number;

边数，默认值 `3`。

#### readonly centerX: number;

只读属性圆心坐标 x。

#### readonly centerY: number;

只读属性圆心坐标 y。

## Star

正多角形

### 构造函数

#### Star(startX, startY, radius, sides, originX, originY) extends Isogon;

## Koch

科赫雪花分形

### 构造函数

#### Koch(startX, startY, radius, sides, originX, originY) extends Isogon;

## Line

线段

### 构造函数

#### Line(startX, startY, endX, endY, originX, originY) extends Shape;

### 属性

#### endX: number;

结束点坐标 x，默认值 `0`。

#### endY: number;

结束点坐标 y，默认值 `0`。

#### readonly length: number;

只读属性长度。

#### readonly angle: number;

只读属性角度。

## Link

连接线。使用 `3` 个点生成一段圆弧或线段。

### 构造函数

#### Link(startX, startY, endX, endY, controlX, controlY, originX, originY) extends Line;

### 属性

#### controlX: number;

控制点坐标 x，默认值 `undefined`。

#### controlY: number;

控制点坐标 y，默认值 `undefined`。

#### readonly startAngle: number;

只读属性起始角度，在绘制或 `rebuildPath()` 之后才会产生此值。

#### readonly endAngle: number;

只读属性结束角度，在绘制或 `rebuildPath()` 之后才会产生此值。

## Curve

贝塞尔曲线。有以下 `3` 种表现形式：

1. 存在 0 个控制点，表现为线段
2. 存在 1 个控制点，表现为二次贝塞尔曲线
3. 存在 2 个控制点，表现为三次贝塞尔曲线

### 构造函数

#### Curve(startX, startY, endX, endY, controlX1, controlY1, controlX2, controlY2, originX, originY) extends Link;

### 属性

#### controlX1: number;

控制点 `1` 的坐标 x，等价于属性 `controlX`。

#### controlY1: number;

控制点 `1` 的坐标 y，等价于属性 `controlY`。

#### controlX2: number;

控制点 `2` 的坐标 x。

#### controlY2: number;

控制点 `2` 的坐标 y。

#### readonly startAngle: number;

只读属性起始角度。

#### readonly endAngle: number;

只读属性结束角度。

#### readonly centerAngle: number;

只读属性中段角度。

## Triangle

等腰三角形。使用顶角垂线长度与顶角角度来定义。

### 构造函数

#### Triangle(startX, startY, length, angle, originX, originY) extends Shape;

### 属性

#### length: number;

顶角垂线长度，默认值 `10`。

#### angle: number;

顶角角度，默认值 `Math.PI / 3`。

## Droplet

水滴形

### 构造函数

#### Droplet(startX, startY, length, angle, originX, originY) extends Triangle;

## Heart

心形

### 构造函数

#### Heart(startX, startY, length, angle, originX, originY) extends Triangle;

## Pin

大头针形

### 构造函数

#### Pin(startX, startY, length, angle, originX, originY) extends Triangle;

## Path

`window.Path2D` 的包装器路径

### 构造函数

#### Path(startX, startY, path, originX, originY) extends Shape;

### 属性

#### path: Path2D;

内部路径数据。

### 方法

#### addPath(path: string | Path2D | Path): this;

添加路径的方法。

##### 参数

- path，可以是 svg path 字符串、Path2D 对象或 Path 对象。

#### moveTo(x: number, y: number): this;

移动画笔。

##### 参数

- x，坐标 x
- y，坐标 y

#### lineTo(x: number, y: number): this;

绘制连线。

##### 参数

- x，坐标 x
- y，坐标 y

#### arcTo(x1: number, y1: number, x2: number, y2: number, radius: number): this;

依据两个控制点绘制圆弧。

##### 参数

- x1，朝向的控制点 1 坐标 x
- y1，朝向的控制点 1 坐标 y
- x2，圆弧朝向的控制点 2 坐标 x
- y2，圆弧朝向的控制点 2 坐标 y
- radius，圆弧半径

#### quadraticCurveTo(cpx: number, cpy: number, x: number, y: number): this;

绘制二次贝塞尔曲线。

##### 参数

- cpx，控制点坐标 x
- cpy，控制点坐标 y
- x，结束点坐标 x
- y，结束点坐标 y

#### bezierCurveTo(cp1x: number, cp1y: number, cp2x: number, cp2y: number, x: number, y: number): this;

绘制三阶贝塞尔曲线。

##### 参数

- cp1x，控制点 1 坐标 x
- cp1y，控制点 1 坐标 y
- cp2x，控制点 2 坐标 x
- cp2y，控制点 2 坐标 y
- x，结束点坐标 x
- y，结束点坐标 y

#### rect(x: number, y: number, w: number, h: number): this;

绘制矩形。

##### 参数

- x，矩形起始点坐标 x
- y，矩形起始点坐标 y
- w，矩形宽度
- h，矩形高度

#### arc(x: number, y: number, radius: number, startAngle: number, endAngle: number, anticlockwise?: boolean): this;

绘制圆弧。

##### 参数

- x，中心点坐标 x
- y，中心点坐标 y
- radius，半径
- startAngle，起始角度
- endAngle，结束角度
- anticlockwise，是否逆时针绘制

#### ellipse(x, y, radiusX, radiusY, rotation, startAngle, endAngle, anticlockwise?: boolean): this;

绘制椭圆。

##### 参数

- x，中心点坐标 x
- y，中心点坐标 y
- radiusX，长轴半径
- radiusY，短轴半径
- rotation，旋转角度
- startAngle，起始角度
- endAngle，结束角度
- anticlockwise，是否逆时针绘制

#### closePath(): this;

闭合路径。

## PolyShape

以原始数据点来驱动的图形

### 构造函数

#### PolyShape(startX, startY, points, length, originX, originY) extends Shape;

### 属性

#### points: Array;

原始数据点的一维列表，默认值 `[]`。

#### length: number;

目标数据点的长度，默认值 `0`。

#### readonly built: Array;

构建的数据点列表，由 `buildPoints` 构建而来。

#### readonly data: Array;

目标数据点列表，由构建的数据点 `built` 经过矩阵变换而来。

### 方法

#### append(x: number, y: number): this;

添加一组数据点 `x, y`。

##### 参数

- x，坐标 x
- y，坐标 y

#### insert(index: number, x: number, y: number): this;

在索引 `index` 处插入一组数据点 `x, y`。

##### 参数

- index，索引
- x，坐标 x
- y，坐标 y

#### delete(index: number): this;

删除索引 `index` 处的一组数据点。

##### 参数

- index，索引

#### update(index: number, x: number, y: number): this;

更新索引 `index` 处的一组数据点。

##### 参数

- index，索引
- x，坐标 x
- y，坐标 y

#### peek(index: number, offset: number): number;

查看索引 `index` 处，偏移量 `offset` 处的坐标值。

##### 参数

- index，索引
- offset，偏移量

#### clear(): this;

清空所有数据点。

#### rebuildPoints(): void;

`PolyShape` 统一组建参数，重建数据点的方法。通常由子类覆写，自动执行，使得 `buildPoints()` 与对象解耦。

#### buildPoints(startX, startY, points, length, built): void;

`PolyShape` 具体构建数据点的方法。通常由子类覆写，由 `rebuildPath()` 触发调用。

##### 参数

- startX，起始绘制点 x
- startY，起始绘制点 y
- points，原始数据点
- length，目标数据点长度
- built，构建的数据点
- ...，由 rebuildPoints() 组建而来

## PolylineLike

私有、抽象的类折线段

### 构造函数

#### PolylineLike(startX, startY, points, length, originX, originY) extends PolyShape;

## Polyline

折线段

### 构造函数

#### Polyline(startX, startY, points, length, originX, originY) extends PolylineLike;

## Bezier

多阶贝塞尔曲线

### 构造函数

#### Bezier(startX, startY, points, length, originX, originY) extends PolylineLike;

### 方法

#### elevateDegree(): this;

贝塞尔曲线升阶，新增一组控制点并保持曲线的形状。

## PolygonLike

私有、抽象的类多边形

### 构造函数

#### PolygonLike(startX, startY, points, length, originX, originY) extends PolylineLike;

## Polygon

多边形

### 构造函数

#### Polygon(startX, startY, points, length, originX, originY) extends PolygonLike;

## Rectangle

矩形

### 构造函数

#### Rectangle(startX, startY, points, length, originX, originY) extends PolygonLike;

### 属性

#### readonly width: number;

只读属性宽度。

#### readonly height: number;

只读属性高度。

#### readonly left: number;

只读属性左侧边界。

#### readonly top: number;

只读属性上侧边界。

#### readonly right: number;

只读属性右侧边界。

#### readonly bottom: number;

只读属性下侧边界。

## Circle

圆形

### 构造函数

#### Circle(startX, startY, points, length, originX, originY) extends PolygonLike;

### 属性

#### readonly radius: number;

只读属性半径。

#### readonly centerX: number;

只读属性圆心坐标 x。

#### readonly centerY: number;

只读属性圆心坐标 y。

## SuperEllipse

超椭圆

### 构造函数

#### SuperEllipse(startX, startY, points, length, exponent, originX, originY) extends PolygonLike;

### 属性

#### exponent: number;

超椭圆公式中的幂值，默认值 `3`。

## SmoothLine

平滑曲线

### 构造函数

#### SmoothLine(startX, startY, points, length, tension, originX, originY) extends PolyShape;

### 属性

#### tension: number;

张力，默认值 `1`。

## Event

事件订阅

### 构造函数

#### Event();

### 方法

#### on(type: string, listener: Function): this;

注册类型为 `type` 的 `listener` 事件监听器。

#### off(type: string, listener: Function): this;

移除类型为 `type` 的 `listener` 事件监听器。

#### trigger(type: string, event: any): this;

触发类型为 `type` 的事件，以 `event` 为事件对象，若 `event` 非原始数据类型，会被赋予 `stop()` 方法的能力，用于停止事件分发。

#### onregister(type);

首次注册 `type` 事件时的回调。

### onunregister(type);

最终移除 `type` 事件时的回调。
