- `Bootstrap`是一个CSS框架，用于页面布局

#### 一、引入CSS文件

##### (1) 本地`bootstrap`

```html
<link rel="stylesheet" href="bootstrap-3.3.7/dist/css/bootstrap.css">
```

##### (2) 网络`bootstrap`

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" >
```

#### 二、引入jQuery

- 用到`Bootstrap`提供的js插件，需要`jQuery`框架

```html
<link type="text/css" rel="stylesheet" href="bootstrap-3.3.7/dist/css/bootstrap.css">
<!-- 在bootstrap.js之前引入 -->
<script type="text/javascript" src="jquery-3.3.1.js"></script>
<script type="text/javascript" src="bootstrap-3.3.7/dist/js/bootstrap.js"</script>
```

#### 三、移动设备优先

- 移动设备优先是`Bootstrap 3`的最显著的变化；
- `Bootstrap 2`需要手动应用另一个CSS，才能实现移动设备友好；

```html
<meat name="viewport" content="width=device-width, initial-scale=1.0">
```

- width属性控制设备宽度，分辨率不同，将被设置位device-width，确保正确显示；
- initial-scale=1.0确保网页加载时，以1:1的比例呈现，不会有任何缩放；