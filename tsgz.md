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

#### 写法：
···
html{
  font-size:62.5%; 
}
···

#### 项目中的例子

···
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
···

### 2.flex

## 动画

### 1.平移

### 2.轮动

### 3.vue动画

## 图表

### 1.折线图

### 2. 柱状图

### 3. 地图
