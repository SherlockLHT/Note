## 前提：

**`vue`可以在文本（{{  ...}}）中插入`js`语法（表达式），但是表达式太长太复杂，影响可读性。为此，引入计算属性，将表达式放在函数内部。**



## 语法：

包含在`vue`实例的`computed`参数内部。

**示例：**

```html
<div id="app">
    <p>原始字符串: {{ message }}</p>
    <p>计算后的字符串: {{ dealedMessage }}</p>
</div>
```

```js
new Vue({
    el: '#app',
    data: {
        message: 'sherlock'
    },
    computed: {
        //这个是计算属性的getter
        dealedMessage: function(){
            return "name" + this.message;
        }
    }
});
```

### `getter`和`setter`

计算属性默认只有`getter`，上例中，没有指定，则默认，若要指定，也可以写成如下方式：

```js
var app = new Vue({    
    el: '#app',    
    data: {        
        message: 'sherlock'    
    },    
    computed: {   
        dealedMessage: {
            //计算属性的getter
            get: function(){
                return "name" + this.message;
            },
            //计算属性的setter
            set: function(new_message){
                this.message = new_message;
            }
        }
    }
});
```

当调用`app.dealedMessage = "new_message"`时，`setter`就会被调用。

##  计算属性和方法的区别

- 在`vue`实例内的`methods`内新建方法，也可以达到相同的效果；

**区别如下：**

> **计算属性**是基于其依赖来进行缓存的。只有相关依赖发生改变时，才会重新取值。

上例中，只有当`vue`实例的`message`变化时，`dealedMessage`才会调用，即只要`message`没有改变，即使多次调用`dealedMessage`，返回的也都是第一次的结果，而且并没有执行函数。而函数，每次调用都会执行一次。

**为什么要使用计算属性？**

如上所说，计算属性和方法的区别在于依赖缓存。假设现在邮一个新能开销比较大的计算属性A，A需要遍历一个巨大的数组并作大量的计算。然后，还有计算属性B，依赖A，则B每次执行A时，都是缓存，若使用方法，则不可避免的，每次都需要重新执行A。