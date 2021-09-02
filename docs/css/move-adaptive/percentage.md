
### 什么是百分比?

百分比是前端开发中一个动态单位，永远是当前元素的**父元素**作为参考进行计算

比如父元素的宽高都是 200px，设置子元素的宽高是 50%，那么子元素的宽高就是 100px

### 百分比的特点

1.  子元素的高度是参考父元素的宽度计算的
2.  子元素的宽度是参考父元素的高度计算的
3.  子元素的 padding 无论是水平还是垂直方向都是参考父元素的**宽度**计算的
4.  子元素的 margin 无论是水平还是垂直方向都是参考父元素的**宽度**计算的
5.  不能使用百分比来设置元素的 border