### 一、使用`<script>`引用

#### 本地引用

```html
<script src="vue.js"></script>
```

#### 网络引用

```html
<!-- Staticfile CDN（国内） -->
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
<!-- unpkg,该版本会和npm发布的最新版保持一致 -->
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<!-- cdnjs -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.1.8/vue.min.js"></script>
```

### 二、npm安装

- 适用于大型项目；
- 可以和很多模块打包器配置使用；
- npm版本必须3.0以上；

安装方法：

```
npm install vue
cnpm install vue	//npm速度慢，使用淘宝镜像
```

### 三、命令行工具(CLI)

后续更新