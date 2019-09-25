## 态势感知目录

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

1、我给页面设置的根字体为20px，假定设计图给我的1240尺寸，那我应该怎么一个计算方式。
 
20 * document.documentElement.clientWidth /（1240）= 当前页面根字体大小px。
 
2、我给页面设置的根字体为20px，假定设计图给我的1920尺寸，那我应该怎么一个计算方式。

20 * document.documentElement.clientWidth /（1920）= 当前页面根字体大小px。

#### 项目中的例子

1rem = 100px;

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

### 2.flex

## 动画

### 1.平移

### 2.轮动

### 3.vue动画

## 图表

### 1.折线图

### 2. 柱状图

### 3. 地图
