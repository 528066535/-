## 态势感知界面的结构 目录

#### 布局

 1. rem

 2. flex

#### 动画

 1. 平移

 2. 轮动

 3. vue动画

#### 图表

 1. 折线图

 2. 柱状图

 3. 地图





## 布局

### 1.rem

#### 概念

rem是CSS3新增的相对长度单位，是指相对于根元素html的font-size计算值的大小。如果想要设置12px的字体大小也就是12px/16px = 0.75rem。

#### 作用

由于px是相对固定单位，字号大写直接被定死，无法随着浏览器进行缩放。

em和rem都是相对单位，em是相对于其父元素的font-size，页面层级越深，em换算越复杂，麻烦。

rem直接相对于根元素html，避开层级关系，移动端新型浏览器对其支持较好。

#### 写法

1rem = 20px;

```
html{
  font-size: 20px; 
}
```

#### 那如何根据窗口大小，自动缩小和变大？

举个板栗：

1、我给页面设置的根字体为20px，假定设计图给我的640尺寸，那我应该怎么一个计算方式。
 
20 * document.documentElement.clientWidth /（640）= 当前页面根字体大小px。
 
2、我给页面设置的根字体为20px，假定设计图给我的750尺寸，那我应该怎么一个计算方式。

20 * document.documentElement.clientWidth /（750）= 当前页面根字体大小px。

#### 项目中的例子

```
    //extend.js
    function setFont() {
        let baseWidth = 1920;
        let width = $(window).width();

        let prefix = width / baseWidth;
        $('html').css({'font-size': 625 * prefix + '%'});
    }

    setFont();

    $(window).on('resize', () => {
        setFont();
    });
```

1rem = ?px;


### 2.flex

#### 兼容性

ie10+

#### 用法

任何一个容器都可以指定为 Flex 布局。

```
.box{
  display: flex;
}
```

注意，设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

#### 基本概念

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

#### 容器的常见属性

a.flex-direction（属性决定主轴的方向）

```
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

b.flex-wrap

默认情况下，项目都排在一条线（又称"轴线"）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。

```
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
第一个是不换行， 第二个是换行， 第三个不换行的情况下，第一行放在最下面。

c.flex-flow

flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

c.justify-content

justify-content属性定义了项目在主轴上的对齐方式。

```
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

e.align-items

align-items属性定义项目在交叉轴上如何对齐。

```
.box {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```

![align-items](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071011.png)

f.align-content

align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

```
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

![align-content](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071012.png)


#### 项目常见属性

a.order
b.flex-grow
c.flex-shrink
d.flex-basis
e.flex
f.align-self

## 动画

### 1.平移

### 2.轮动

### 3.vue动画

## 图表

### 1.折线图

### 2. 柱状图

### 3. 地图
