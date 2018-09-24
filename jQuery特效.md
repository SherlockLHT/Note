### 一、隐藏和显示

```javascript
$(selector).hide(speed, callback)		//隐藏
$(selector).show(speed, callback)		//显示
$(selector).toggle(speed, callback)		//隐藏和显示切换
```

- speed：特效变换的速度，值有：fast、slow和毫秒；
- callback：在特效变换完成之后的回调函数

示例：

```javascript
$("p").toggle("slow")	//缓慢切换
```



### 二、淡入和淡出

```javascript
$(selector).fadeIn(speed, callback)			//淡入
$(selector).fadeOut(speed, callback)		//淡出
$(selector).fadeToggle(speed, callback)		//淡入淡出切换
$(selector).fadeTo(speed, opacity, callback)//渐变为给定的不透明度，0~1
```

- opacity：给特效设置为不透明度，范围为：0~1；

示例：

```javascript
$(selector).fadeTo("slow", 0.2)
```

### 三、滑动

```javascript
$(selector).slideDown(speed, callback)		//向下滑动
$(selector).slideUp(speed, callback)		//向上滑动
$(selector).slideToggle(speed, callback)	//上下滑动切换
```

### 四、动画

