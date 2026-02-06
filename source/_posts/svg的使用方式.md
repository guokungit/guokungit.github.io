---
title: svg的使用方式
date: 2026-02-06 09:12:13
tags: css、svg
categories: 前端
---
# SVG 零基础全面教程（含核心图形+样式+渐变案例）
SVG（Scalable Vector Graphics，可缩放矢量图形）是基于 XML 语法的矢量图形格式，**放大/缩小不会失真**，可直接嵌入HTML、通过代码绘制或作为文件引入，广泛用于图标、可视化图表、网页装饰等场景。SVG 支持绘制基础图形、文本、自定义路径，还能通过属性/滤镜实现描边、阴影、渐变等效果，所有图形均可通过 CSS/JS 动态修改。

本文从基础介绍到高级样式，逐一讲解核心用法并附带**可直接运行的代码案例**，所有案例均为「HTML 内嵌 SVG」形式，复制即可在浏览器中预览效果。

## 一、SVG 基础介绍
### 核心特点
1. 矢量格式：由点、线、面的数学公式描述，无像素损失；
2. 可编辑：直接修改 XML 代码或用设计工具（AI、Figma）编辑；
3. 轻量：纯文本格式，体积小，可被浏览器直接解析；
4. 可编程：支持 CSS 样式、JS 交互（如点击、动画）。

### 基础结构
SVG 需通过 `<svg>` 标签定义画布，核心属性：
- `width/height`：画布宽高（可设像素`px`、百分比`%`，无单位默认px）；
- `viewBox`：视口坐标系，格式`x y w h`（x/y为画布左上角原点，w/h为视口宽高，实现自适应缩放）；
- `xmlns`：SVG 命名空间（必填，固定值`http://www.w3.org/2000/svg`）。

**基础骨架（HTML 内嵌）**：
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>SVG 教程</title>
</head>
<body>
    <!-- SVG 画布：宽400px，高300px，原点(0,0)，视口与画布等比例 -->
    <svg width="400" height="300" viewBox="0 0 400 300" xmlns="http://www.w3.org/2000/svg">
        <!-- 所有图形/样式都写在这里 -->
    </svg>
</body>
</html>
```
> 后续所有案例均基于此骨架，仅修改 `<svg>` 内部内容。

## 二、基础图形绘制（核心标签+案例）
SVG 提供专属标签绘制基础图形，无需复杂路径，核心是**位置+尺寸属性**。

### 1. 矩形（`<rect>`）
绘制矩形/圆角矩形，核心属性：
- `x/y`：矩形左上角坐标；
- `width/height`：矩形宽高；
- `rx/ry`：圆角半径（rx/ry相同可只写一个）；
- `fill`：填充颜色（`none`为无填充）。

**案例：普通矩形+圆角矩形**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 普通红色矩形 -->
    <rect x="20" y="20" width="100" height="80" fill="#f00" />
    <!-- 蓝色圆角矩形（无填充，后续讲描边） -->
    <rect x="150" y="20" width="100" height="80" rx="10" ry="10" fill="#00f" rx="15" />
</svg>
```

### 2. 圆形（`<circle>`）
绘制正圆，核心属性：
- `cx/cy`：圆心坐标（circle center）；
- `r`：圆的半径。

**案例：纯色圆形+空心圆形**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 绿色实心圆 -->
    <circle cx="80" cy="80" r="60" fill="#0f0" />
    <!-- 橙色空心圆（fill为none，仅描边） -->
    <circle cx="250" cy="80" r="60" fill="none" stroke="#f90" stroke-width="5" />
</svg>
```

### 3. 椭圆形（`<ellipse>`）
绘制椭圆（圆形的扩展，横竖半径不同），核心属性：
- `cx/cy`：椭圆中心坐标；
- `rx`：水平半径（x轴方向）；
- `ry`：垂直半径（y轴方向）。

**案例：椭圆基础绘制**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 紫色椭圆：水平半径80，垂直半径50 -->
    <ellipse cx="150" cy="100" rx="80" ry="50" fill="#90f" />
    <!-- 黄色空心椭圆 -->
    <ellipse cx="300" cy="100" rx="60" ry="40" fill="none" stroke="#ff0" stroke-width="4" />
</svg>
```

### 4. 线条（`<line>`）
绘制直线，核心属性：
- `x1/y1`：起点坐标；
- `x2/y2`：终点坐标；
- **注意**：线条无填充（`fill`无效），需通过`stroke`设置描边颜色，否则不可见。 M L大写字母代表绝对坐标，m l小写代表相对坐标。（基于画布坐标系）

**案例：单条直线+十字线**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 黑色直线：起点(20,20)，终点(380,20)，线宽3 -->
    <line x1="20" y1="20" x2="380" y2="20" stroke="#000" stroke-width="3" />
    <!-- 红色竖线+蓝色横线：十字线 -->
    <line x1="200" y1="20" x2="200" y2="180" stroke="#f00" stroke-width="2" />
    <line x1="20" y1="100" x2="380" y2="100" stroke="#00f" stroke-width="2" />
</svg>
```

### 5. 多边形（`<polygon>`）
绘制**闭合多边形**（三角形、五角星、六边形等），核心属性：
- `points`：顶点坐标集合，格式`x1 y1 x2 y2 x3 y3 ...`（空格/逗号分隔均可）；
- 图形自动闭合（最后一个顶点会连接到第一个顶点）。

**案例：三角形+五角星**
```svg
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
    <!-- 橙色三角形：三个顶点坐标 -->
    <polygon points="100 50, 50 150, 150 150" fill="#f90" />
    <!-- 红色五角星：五个顶点坐标（经典五星参数） -->
    <polygon points="250 50, 320 180, 200 100, 300 100, 180 180" fill="#f00" />
</svg>
```

### 6. 多线条（`<polyline>`）
绘制**开放的多段线**（与`<polygon>`的唯一区别：不自动闭合），核心属性同`<polygon>`：`points`。

**案例：折线+波浪线**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 蓝色折线：开放多段线，线宽3 -->
    <polyline points="20 100, 80 50, 140 150, 200 80" fill="none" stroke="#00f" stroke-width="3" />
    <!-- 绿色波浪线：连续折点模拟波浪 -->
    <polyline points="220 100, 250 80, 280 100, 310 120, 340 100, 370 80" fill="none" stroke="#0f0" stroke-width="2" />
</svg>
```

### 7. 文本（`<text>`）
在SVG画布中绘制文字，支持单行/多行、文字定位、样式修改，核心属性：
- `x/y`：文字基线的起始坐标；
- `font-size/font-family/font-weight`：字体样式（与CSS一致）；
- `fill`：文字颜色（描边用`stroke`）；
- `text-anchor`：文字对齐方式（`start`/`middle`/`end`，默认start）。

**案例：基础文字+居中文字+带描边文字**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 基础黑色文字 -->
    <text x="20" y="50" font-size="24" font-family="微软雅黑">SVG 文字教程</text>
    <!-- 居中蓝色文字（text-anchor: middle） -->
    <text x="200" y="100" font-size="28" font-family="微软雅黑" fill="#00f" text-anchor="middle" font-weight="bold">居中文字</text>
    <!-- 带描边的黄色文字 -->
    <text x="20" y="150" font-size="22" fill="#ff0" stroke="#000" stroke-width="1">描边文字效果</text>
</svg>
```
> 多行文字可用`<tspan>`标签嵌套在`<text>`中，通过`dy`设置行间距。

### 8. 路径（`<path>`）
SVG 中**最强大的标签**，可绘制任意复杂图形（曲线、圆弧、不规则图形等），核心属性：
- `d`：路径指令（核心），由**指令字母+坐标**组成，指令分**绝对坐标（大写字母）**和**相对坐标（小写字母）**；
- 常用基础指令：
  1. `M x y`/`m dx dy`：移动到起点（Move to，无绘制）；
  2. `L x y`/`l dx dy`：绘制直线到目标点（Line to）；
  3. `C x1 y1 x2 y2 x y`/`c`：三次贝塞尔曲线；
  4. `A rx ry x-axis-rotation large-arc-flag sweep-flag x y`/`a`：绘制圆弧；
  5. `Z`/`z`：闭合路径（连接到起点，无坐标）。

**案例1：用path绘制矩形（替代`<rect>`）**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 绝对坐标：M(20,20)→L(120,20)→L(120,100)→L(20,100)→Z闭合 -->
    <path d="M20 20 L120 20 L120 100 L20 100 Z" fill="#f0f" />
</svg>
```

**案例2：用path绘制心形（经典贝塞尔曲线案例）**
```svg
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
    <!-- 心形路径：M起点→两个贝塞尔曲线→Z闭合，红色填充 -->
    <path d="M200 50 C150 0, 50 80, 200 200 C350 80, 250 0, 200 50 Z" fill="#f00" />
</svg>
```
> 复杂路径建议用设计工具（AI/Figma）绘制后导出SVG，直接复制`d`属性即可，无需手动写指令。

## 三、SVG 核心样式属性
### 描边属性（基础样式，所有图形通用）
描边用于设置图形的边框样式，核心属性均以`stroke`开头，需配合`fill: none`实现「空心图形」效果，常用属性：
1. `stroke`：描边颜色（颜色值、渐变、none）；
2. `stroke-width`：描边宽度（像素值，无单位默认px）；
3. `stroke-linecap`：线条端点样式（`butt`平端/`round`圆端/`square`方端，默认butt）；
4. `stroke-linejoin`：线条拐角样式（`miter`尖角/`round`圆角/`bevel`斜角，默认miter）；
5. `stroke-dasharray`：虚线样式（值为「实线长度 空白长度」，多个值循环）；
6. `stroke-dashoffset`：虚线偏移量（控制虚线的起始位置）。

**案例：描边属性综合演示**
```svg
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
    <!-- 圆端直线 + 圆角拐角多边形 -->
    <line x1="20" y1="50" x2="380" y2="50" stroke="#00f" stroke-width="8" stroke-linecap="round" />
    <polygon points="100 100, 150 200, 50 200" fill="none" stroke="#f90" stroke-width="6" stroke-linejoin="round" />
    <!-- 虚线圆形 + 偏移虚线矩形 -->
    <circle cx="300" cy="150" r="40" fill="none" stroke="#0f0" stroke-width="3" stroke-dasharray="10 5" />
    <rect x="250" y="220" width="100" height="60" fill="none" stroke="#f00" stroke-width="2" stroke-dasharray="8 4" stroke-dashoffset="6" />
</svg>
```

### 模糊和阴影效果（滤镜`<filter>`）
SVG 的模糊、阴影效果需通过**滤镜（`<filter>`）**实现，滤镜定义在`<defs>`（可复用元素容器）中，通过`id`标识，再通过`filter`属性引用。
#### 1. 模糊效果（`<feGaussianBlur>`）
核心属性：`stdDeviation`（模糊程度，值越大越模糊）。
#### 2. 阴影效果（`<feDropShadow>`）
核心属性：
- `dx/dy`：阴影的水平/垂直偏移量；
- `stdDeviation`：阴影模糊度；
- `flood-color`：阴影颜色。

**案例：模糊+阴影综合效果**
```svg
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
    <!-- 定义可复用滤镜：放在defs中 -->
    <defs>
        <!-- 模糊滤镜：id=blur，模糊度5 -->
        <filter id="blur" x="-10%" y="-10%" width="120%" height="120%">
            <feGaussianBlur in="SourceGraphic" stdDeviation="5" />
        </filter>
        <!-- 阴影滤镜：id=shadow，偏移(5,5)，模糊3，黑色阴影 -->
        <filter id="shadow" x="-10%" y="-10%" width="120%" height="120%">
            <feDropShadow dx="5" dy="5" stdDeviation="3" flood-color="#0006" />
        </filter>
        <!-- 模糊+阴影组合滤镜 -->
        <filter id="blurShadow" x="-10%" y="-10%" width="120%" height="120%">
            <feGaussianBlur in="SourceGraphic" stdDeviation="2" />
            <feDropShadow dx="3" dy="3" stdDeviation="2" flood-color="#0008" />
        </filter>
    </defs>
    <!-- 应用滤镜：filter=url(#滤镜id) -->
    <rect x="20" y="20" width="100" height="80" fill="#f00" filter="url(#blur)" />
    <circle cx="250" cy="60" r="40" fill="#00f" filter="url(#shadow)" />
    <text x="200" y="200" font-size="32" fill="#f90" font-weight="bold" text-anchor="middle" filter="url(#blurShadow)">滤镜效果</text>
</svg>
```
> `<defs>`是SVG的「定义区」，里面的元素不会直接渲染，仅用于复用，是SVG样式/渐变/滤镜的标准写法。

## 四、SVG 渐变效果（线性+径向，含金色渐变）
渐变是SVG中常用的高级样式，分为**线性渐变**和**径向渐变**，均需定义在`<defs>`中，通过`id`引用，核心是**渐变停止点（`<stop>`）**，用`offset`设置渐变位置（0%~100%），`stop-color`设置渐变颜色。

### 1. 线型渐变（`<linearGradient>`）
沿**直线方向**的渐变，核心属性：
- `x1/y1/x2/y2`：渐变的起始/结束坐标（0%/0% 到 100%/100% 为对角线，0%/0% 到 100%/0% 为水平渐变，0%/0% 到 0%/100% 为垂直渐变）；
- 渐变停止点`<stop>`：必须包含`offset`（渐变位置）和`stop-color`（渐变颜色），可选`stop-opacity`（透明度）。

**案例：水平线性渐变+垂直线性渐变**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <defs>
        <!-- 水平线性渐变：从左到右，蓝→白→红，id=linear1 -->
        <linearGradient id="linear1" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" stop-color="#00f" />
            <stop offset="50%" stop-color="#fff" />
            <stop offset="100%" stop-color="#f00" />
        </linearGradient>
        <!-- 垂直线性渐变：从上到下，绿→黄，id=linear2 -->
        <linearGradient id="linear2" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#0f0" />
            <stop offset="100%" stop-color="#ff0" />
        </linearGradient>
    </defs>
    <!-- 应用线性渐变：fill=url(#渐变id) -->
    <rect x="20" y="20" width="150" height="160" fill="url(#linear1)" />
    <ellipse cx="300" cy="100" rx="70" ry="80" fill="url(#linear2)" />
</svg>

### 2. 金色渐变（线性渐变进阶，模拟金属质感）
金色渐变的核心是**暖黄+亮金+浅棕**的色彩搭配，通过多段`<stop>`实现金属光泽，是实际开发中常用的图标/装饰渐变效果。

**案例：金色渐变按钮（带描边+圆角）**
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <defs>
        <!-- 金色线性渐变：模拟金属质感，id=goldGradient -->
        <linearGradient id="goldGradient" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#ffd700" /> <!-- 亮金色 -->
            <stop offset="40%" stop-color="#f9c80e" /> <!-- 暖金色 -->
            <stop offset="60%" stop-color="#e6b800" /> <!-- 暗金色 -->
            <stop offset="100%" stop-color="#d4a700" /> <!-- 金棕色 -->
        </linearGradient>
    </defs>
    <!-- 金色渐变圆角矩形（按钮）：带金色描边，圆角20 -->
    <rect x="100" y="50" width="200" height="100" rx="20" ry="20" 
          fill="url(#goldGradient)" 
          stroke="#c89f00" 
          stroke-width="3" />
    <!-- 按钮文字 -->
    <text x="200" y="110" font-size="28" font-family="微软雅黑" font-weight="bold" 
          fill="#8b6914" text-anchor="middle">金色按钮</text>
</svg>
```

### 3. 径向渐变（`<radialGradient>`）
沿**圆心向外辐射**的渐变（如太阳、球体的光影效果），核心属性：
- `cx/cy/r`：渐变的中心坐标和半径（默认50%/50%/50%，即画布中心）；
- `fx/fy`：渐变的焦点坐标（高光点，默认与cx/cy重合）；
- 渐变停止点`<stop>`与线性渐变一致。

**案例：基础径向渐变+球体光影效果（进阶）**
```svg
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
    <defs>
        <!-- 基础径向渐变：蓝→紫→粉，id=radial1 -->
        <radialGradient id="radial1" cx="50%" cy="50%" r="50%" fx="50%" fy="50%">
            <stop offset="0%" stop-color="#00f" />
            <stop offset="50%" stop-color="#90f" />
            <stop offset="100%" stop-color="#f0f" />
        </radialGradient>
        <!-- 球体光影径向渐变：模拟黄色球体，id=ballGradient（焦点偏移实现高光） -->
        <radialGradient id="ballGradient" cx="50%" cy="50%" r="50%" fx="30%" fy="30%">
            <stop offset="0%" stop-color="#fff" stop-opacity="0.9" /> <!-- 高光点 -->
            <stop offset="30%" stop-color="#ff0" /> <!-- 亮黄色 -->
            <stop offset="80%" stop-color="#e6b800" /> <!-- 暗黄色 -->
            <stop offset="100%" stop-color="#d4a700" stop-opacity="0.8" /> <!-- 阴影 -->
        </radialGradient>
    </defs>
    <!-- 基础径向渐变圆形 -->
    <circle cx="150" cy="150" r="80" fill="url(#radial1)" />
    <!-- 球体光影效果（径向渐变+阴影滤镜） -->
    <circle cx="300" cy="150" r="80" fill="url(#ballGradient)" filter="url(#shadow)" />
    <!-- 复用之前的阴影滤镜，若单独运行需重新定义shadow滤镜 -->
    <filter id="shadow" x="-10%" y="-10%" width="120%" height="120%">
        <feDropShadow dx="6" dy="6" stdDeviation="4" flood-color="#0006" />
    </filter>
</svg>
```

## 五、完整综合案例（所有知识点整合）
以下案例整合了**基础图形、描边、滤镜、线性渐变、金色渐变、径向渐变、文本、路径**所有知识点，复制即可直接运行：
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>SVG 综合案例</title>
    <style>body { background: #f5f5f5; }</style>
</head>
<body>
    <svg width="500" height="400" viewBox="0 0 500 400" xmlns="http://www.w3.org/2000/svg">
        <!-- 定义区：滤镜+所有渐变 -->
        <defs>
            <!-- 阴影滤镜 -->
            <filter id="shadow" x="-10%" y="-10%" width="120%" height="120%">
                <feDropShadow dx="4" dy="4" stdDeviation="3" flood-color="#0006" />
            </filter>
            <!-- 模糊滤镜 -->
            <filter id="blur" x="-10%" y="-10%" width="120%" height="120%">
                <feGaussianBlur stdDeviation="4" />
            </filter>
            <!-- 线性渐变：蓝白渐变 -->
            <linearGradient id="linearBlue" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#0099ff" />
                <stop offset="100%" stop-color="#e6f7ff" />
            </linearGradient>
            <!-- 金色渐变 -->
            <linearGradient id="gold" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#ffd700" />
                <stop offset="40%" stop-color="#f9c80e" />
                <stop offset="60%" stop-color="#e6b800" />
                <stop offset="100%" stop-color="#d4a700" />
            </linearGradient>
            <!-- 径向渐变：球体光影 -->
            <radialGradient id="ball" cx="50%" cy="50%" r="50%" fx="35%" fy="35%">
                <stop offset="0%" stop-color="#fff" stop-opacity="0.8" />
                <stop offset="40%" stop-color="#ff6600" />
                <stop offset="100%" stop-color="#e63900" />
            </radialGradient>
        </defs>

        <!-- 1. 金色渐变圆角矩形（按钮）+ 阴影 -->
        <rect x="50" y="30" width="120" height="60" rx="15" fill="url(#gold)" stroke="#c89f00" stroke-width="2" filter="url(#shadow)" />
        <text x="110" y="65" font-size="18" font-weight="bold" fill="#8b6914" text-anchor="middle">金色按钮</text>

        <!-- 2. 线性渐变矩形 + 描边 -->
        <rect x="200" y="30" width="120" height="60" rx="5" fill="url(#linearBlue)" stroke="#0099ff" stroke-width="2" />
        <text x="260" y="65" font-size="18" fill="#fff" text-anchor="middle">线性渐变</text>

        <!-- 3. 径向渐变球体 + 阴影 -->
        <circle cx="400" cy="60" r="40" fill="url(#ball)" filter="url(#shadow)" />
        <text x="400" y="120" font-size="16" fill="#666" text-anchor="middle">径向渐变球体</text>

        <!-- 4. 五角星（多边形）+ 描边+模糊 -->
        <polygon points="250 150, 320 280, 200 200, 300 200, 180 280" fill="#f00" stroke="#ff0" stroke-width="3" filter="url(#blur)" />

        <!-- 5. 路径绘制心形 + 阴影 -->
        <path d="M100 250 C50 180, 150 180, 100 350 C250 180, 150 180, 100 250 Z" fill="#ff69b4" filter="url(#shadow)" />

        <!-- 6. 虚线椭圆 + 圆端线条 -->
        <ellipse cx="400" cy="250" rx="60" ry="40" fill="none" stroke="#00f" stroke-width="3" stroke-dasharray="10 5" />
        <line x1="340" y1="250" x2="460" y2="250" stroke="#f00" stroke-width="4" stroke-linecap="round" />

        <!-- 7. 底部标题文本 -->
        <text x="250" y="380" font-size="24" font-family="微软雅黑" font-weight="bold" fill="#333" text-anchor="middle">SVG 综合案例</text>
    </svg>
</body>
</html>
```

## 总结
1. SVG 是矢量图形格式，基于XML语法，核心画布标签为`<svg>`，需指定`width/height/viewBox`；
2. 基础图形通过专属标签绘制：`<rect>`（矩形）、`<circle>`（圆形）、`<ellipse>`（椭圆）、`<line>`（线条）、`<polygon>`（闭合多边形）、`<polyline>`（开放多段线）、`<text>`（文本）；
3. `<path>`是最强大的绘制标签，通过`d`属性的指令（M/L/C/A/Z）绘制任意复杂图形；
4. 描边属性以`stroke`开头，核心是`stroke`（颜色）、`stroke-width`（宽度）、`stroke-dasharray`（虚线）；
5. 模糊/阴影效果需通过`<filter>`滤镜实现，定义在`<defs>`中，通过`id`引用；
6. 渐变分**线性渐变（`<linearGradient>`）**和**径向渐变（`<radialGradient>`）**，均需定义在`<defs>`中，通过`<stop>`设置渐变停止点，金色渐变是线性渐变的进阶应用，通过暖黄/亮金/金棕的色彩搭配实现金属质感；
7. SVG 所有可复用样式（滤镜、渐变）建议放在`<defs>`中，通过`url(#id)`引用，提升代码复用性。

所有案例均为HTML内嵌SVG形式，可直接在Chrome/Firefox/Edge等现代浏览器中运行，也可将`<svg>`标签保存为`.svg`文件单独使用。

你需要在SVG中为图形、文本等元素添加超链接的方法，SVG 提供了专属的`<a>`标签来实现超链接功能，用法和HTML的`<a>`标签相似但属性有细微差异，同时支持**内部锚点跳转**和**外部网页跳转**，还能结合target设置打开方式，下面为你补充完整的SVG超链接教程，包含核心属性、各类案例和注意事项。

### 一、SVG 超链接核心标签与属性
SVG 超链接使用`<a>`标签（命名空间与SVG一致，无需额外声明），包裹需要添加链接的图形/文本/路径等元素，核心属性：
1. **`xlink:href`/`href`**：指定跳转目标（核心）
   - 外部链接：需带协议（`http://`/`https://`），如`href="https://www.baidu.com"`；
   - 内部锚点：如`href="#section1"`（跳转到页面内id为section1的元素）；
   - 本地文件：如`href="test.html"`。
   > 注意：SVG 1.1用`xlink:href`（需引入xlink命名空间），SVG 2.0已废弃xlink，直接用`href`即可，现代浏览器（Chrome/Edge/Firefox）均支持`href`，推荐使用。
2. **`target`**：设置链接打开方式，与HTML一致
   - `_blank`：新标签页打开（最常用）；
   - `_self`：当前标签页打开（默认）；
   - `_parent`/`_top`：框架页中使用。
3. **`title`**：鼠标悬浮时的提示文字，提升交互体验。

### 二、必备前置：若兼容旧版需引入xlink命名空间
如果需要兼容低版本浏览器（如旧版IE），需在`<svg>`根标签添加xlink命名空间，现代浏览器可省略，格式如下：
```svg
<svg width="400" height="300" 
     xmlns="http://www.w3.org/2000/svg" 
     xmlns:xlink="http://www.w3.org/1999/xlink"> <!-- 旧版兼容必备 -->
    <!-- 超链接代码 -->
</svg>
```

### 三、SVG 超链接经典案例（全覆盖常用场景）
所有案例均基于**现代浏览器**（直接用`href`），复制即可运行，包含「单个图形链接」「多个图形统一链接」「文本链接」「路径/渐变图形链接」等核心场景。

#### 案例1：单个基础图形添加超链接（圆形→百度，新标签页）
为圆形添加超链接，鼠标悬浮时指针变为手型，点击跳转到百度新标签页，带悬浮提示：
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- a标签包裹圆形，href指定链接，target=_blank新标签页，title悬浮提示 -->
    <a href="https://www.baidu.com" target="_blank" title="点击跳转到百度">
        <circle cx="100" cy="100" r="60" fill="#0099ff" cursor="pointer" />
    </a>
    <!-- 可选：添加提示文本 -->
    <text x="100" y="105" font-size="16" fill="#fff" text-anchor="middle">点击跳转</text>
</svg>
```
> 新增`cursor="pointer"`：强制鼠标悬浮时显示手型，部分浏览器默认不生效，建议添加，提升交互一致性。

#### 案例2：多个图形统一添加超链接（按钮组合→掘金）
将「圆角矩形+文本」组合成按钮，包裹在同一个`<a>`标签中，点击按钮任意位置均可跳转：
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 超链接包裹按钮（矩形+文本），统一跳转 -->
    <a href="https://juejin.cn" target="_blank" title="点击进入掘金">
        <rect x="80" y="60" width="240" height="80" rx="20" fill="#ff7200" cursor="pointer" />
        <text x="200" y="110" font-size="24" font-weight="bold" fill="#fff" text-anchor="middle">掘金社区</text>
    </a>
</svg>
```

#### 案例3：文本直接添加超链接（SVG文字→知乎）
单独为SVG文本添加超链接，实现「文字链接」效果，可结合描边/颜色美化：
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <a href="https://www.zhihu.com" target="_blank" title="点击进入知乎">
        <text x="200" y="100" font-size="32" font-weight="bold" fill="#0084ff" text-anchor="middle" cursor="pointer">
            知乎-有问题，就会有答案
        </text>
    </a>
</svg>
```

#### 案例4：复杂图形（路径/渐变/多边形）添加超链接
为之前教程中的**金色渐变按钮、心形路径、五角星多边形**添加超链接，兼容渐变/滤镜/复杂路径：
```svg
<svg width="500" height="400" xmlns="http://www.w3.org/2000/svg">
    <defs>
        <!-- 金色渐变（复用之前的定义） -->
        <linearGradient id="gold" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#ffd700" />
            <stop offset="40%" stop-color="#f9c80e" />
            <stop offset="60%" stop-color="#e6b800" />
            <stop offset="100%" stop-color="#d4a700" />
        </linearGradient>
    </defs>

    <!-- 1. 金色渐变按钮超链接→GitHub -->
    <a href="https://github.com" target="_blank" title="点击进入GitHub">
        <rect x="50" y="50" width="180" height="80" rx="20" fill="url(#gold)" stroke="#c89f00" stroke-width="2" cursor="pointer" />
        <text x="140" y="100" font-size="22" fill="#8b6914" font-weight="bold" text-anchor="middle">GitHub</text>
    </a>

    <!-- 2. 心形路径超链接→小红书 -->
    <a href="https://xiaohongshu.com" target="_blank" title="点击进入小红书">
        <path d="M350 100 C300 30, 400 30, 350 200 C500 30, 400 30, 350 100 Z" fill="#ff69b4" cursor="pointer" />
        <text x="350" y="230" font-size="16" fill="#ff69b4" text-anchor="middle">小红书</text>
    </a>

    <!-- 3. 五角星多边形超链接→B站 -->
    <a href="https://bilibili.com" target="_blank" title="点击进入B站">
        <polygon points="250 200, 320 350, 200 270, 300 270, 180 350" fill="#fb7299" cursor="pointer" />
        <text x="250" y="380" font-size="16" fill="#fb7299" text-anchor="middle">B站</text>
    </a>
</svg>
```

#### 案例5：内部锚点跳转（SVG内/HTML页面内）
超链接不仅支持外部网页，还能跳转到**当前SVG内的元素**或**HTML页面内的锚点**，适合长页面/SVG可视化图表的锚点导航：
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>SVG内部锚点</title>
    <style>
        body { height: 2000px; /* 让页面出现滚动条，方便测试 */ }
        #bottom { margin-top: 1000px; }
    </style>
</head>
<body>
    <!-- SVG中的锚点→页面底部的bottom元素 -->
    <svg width="400" height="100" xmlns="http://www.w3.org/2000/svg">
        <a href="#bottom" title="跳转到页面底部" cursor="pointer">
            <rect x="50" y="20" width="300" height="60" rx="10" fill="#0099ff" />
            <text x="200" y="60" font-size="20" fill="#fff" text-anchor="middle">点击跳转到页面底部</text>
        </a>
    </svg>

    <!-- 页面底部锚点元素 -->
    <div id="bottom">
        <h2>页面底部锚点位置</h2>
        <!-- SVG内部元素锚点：跳转到SVG内的circle元素 -->
        <svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
            <!-- 锚点目标：给圆形设置id=svgAnchor -->
            <circle id="svgAnchor" cx="200" cy="100" r="60" fill="#f00" />
            <!-- 返回到SVG锚点的链接 -->
            <a href="#svgAnchor" title="跳转到红色圆形" cursor="pointer">
                <text x="200" y="180" fill="#333" text-anchor="middle">点击跳转到红色圆形</text>
            </a>
        </svg>
    </div>
</body>
</html>
```

### 四、关键注意事项
1. **`<a>`标签的包裹范围**：需完整包裹需要添加链接的**所有元素**（如图形+文本），否则仅点击被包裹的部分生效；
2. **鼠标指针样式**：部分浏览器中SVG的`<a>`标签不会默认显示手型，需为内部元素添加`cursor="pointer"`属性，强制设置鼠标悬浮样式；
3. **外部链接必须带协议**：跳转外部网页时，`href`必须写`http://`或`https://`，否则浏览器会当作本地路径解析（如`href="www.baidu.com"`会跳转失败）；
4. **渐变/滤镜/defs元素**：`<a>`标签包裹带渐变/滤镜的元素时，渐变/滤镜仍定义在`<defs>`中，不影响链接功能，正常引用即可；
5. **事件冲突**：如果为SVG元素添加了JS点击事件（如`onclick`），超链接会与JS事件冲突，**JS事件优先级更高**（如需同时生效，需在JS中手动触发跳转）。

### 五、补充：JS手动触发SVG超链接跳转
若需要结合JS逻辑（如验证后再跳转），可移除`<a>`标签，通过`onclick`事件手动实现跳转，示例：
```svg
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
    <!-- 点击圆形，执行JS跳转逻辑 -->
    <circle cx="200" cy="100" r="60" fill="#0099ff" cursor="pointer"
            onclick="jumpToBaidu()" title="点击跳转到百度" />
    <text x="200" y="105" font-size="16" fill="#fff" text-anchor="middle">JS触发跳转</text>

    <script type="text/javascript">
        // 自定义跳转逻辑（可添加验证、延迟等操作）
        function jumpToBaidu() {
            // 验证逻辑示例
            if (confirm("是否确认跳转到百度？")) {
                window.open("https://www.baidu.com", "_blank"); // 新标签页打开
                // window.location.href = "https://www.baidu.com"; // 当前标签页打开
            }
        }
    </script>
</svg>
```

### 总结
1. SVG 超链接核心是`<a>`标签，**现代浏览器直接用`href`**，旧版兼容需加xlink命名空间和`xlink:href`；
2. 核心属性：`href`（跳转目标）、`target`（打开方式）、`title`（悬浮提示），配合`cursor="pointer"`优化鼠标样式；
3. 支持**外部网页、本地文件、内部锚点**三种跳转方式，外部链接必须带`http/https`协议；
4. 可包裹单个/多个元素，兼容渐变、路径、多边形等所有SVG图形，无特殊限制；
5. 如需JS控制跳转，可移除`<a>`标签，通过`onclick`结合`window.open/window.location.href`实现，支持自定义逻辑。

该超链接用法可直接整合到之前的SVG教程所有案例中，为任意图形/文本添加跳转功能。