**dragula**原文文档：<https://github.com/bevacqua/dragula>

**dragula**中文文档

# 灵感

你是否曾经想要一个可以直接使用的拖放库?这不仅仅依赖于臃肿的框架，它有强大的支持?当元素被删除时，
它知道该把它们放在哪里?这不需要你做无数的事情来让它工作吗?我也是!

# 特性

- 非常容易设置
- 没有臃肿的依赖
- **自动排序**
- 为拖放项提供可视化的阴影效果
- 触摸事件
- 无需任何配置即可无缝地处理单击

# 安装

你可以从npm上安装

```
npm install dragula --save
```

或者使用bower

```
bower install dragula --save
```

或使用CDN资源

```
<script src='https://cdnjs.cloudflare.com/ajax/libs/dragula/$VERSION/dragula.min.js'></script>
```

如果你不用任何包管理器，可以在**dist**文件夹中下载**dragula**使用。不过我们强烈推荐通过npm来使用。

**引用js**

这儿有一个警告。不能在你web应用的<head>中引入js文件。在<head>放置js引用是非常不好的习惯，dragula也不支持这样使用。

请把dragula引用放在<body>标签中。

**引用CSS**

为了让dragula能够正常工作，您需要合并一些CSS样式。

在你的文档中你可以通过**dist/dragula.css**或**dist/dragula.min.css**来引入。如果你使用的是stylus，你可以通过以下方法来引入样式：
```
@import 'node_modules/dragula/dragula';
```

# 用法

Dragula为您的应用程序提供了最简单的API。

## dragula(containers?, options?)

默认情况下，dragula允许用户在任何容器中拖动一个元素，并将其放到列表中的任何其他容器中。如果元素被放置到容器之外的任何地方，
那么根据==revertonoverflow==和removeonoverflow选项，事件将被优雅地取消。

注意，拖动只在单击鼠标左键时触发，而且只有在没有按下元键时才触发。

下面的示例允许用户从左边拖动元素到右边，从右边拖动到左边。

```js
dragula([document.querySelector('#left'), document.querySelector('#right')]);
```

你也可以提供一个**options**对象。下面是options的默认值：

```js
dragula(containers, {
  isContainer: function(el) {
    return false; // 只有drake.containers中的元素才考虑
  },
  moves: function(el, source, handle, sibling) {
    return false; // 默认情况下，元素总是可拖拽的
  }，
  accepts: function(el, target, source, sibling) {
    return true; // 默认情况下，元素可被放置在任何`containers`中
  },
  invalid: function(el, handle) {
    return false; // 默认情况下，初始时不阻止任何拖拽
  },
  direction: 'vertical', // 当元素被放置时安装Y轴方向放置
  copy: false, // 默认情况下，元素将被移动，而不是复制
  copySortSource: false, 元素在复制源容器中可以重新被排序
  revertOnSpill: false, // 值为true时，当元素放置溢出出，元素将被放回源容器
  removeOnSpill: false, // 值为true时，元素溢出将移除该元素
  mirrorContainer: document.body, // 设置元素的附加镜像元素
});
```
你可以省略containers参数，并在后面动态添加containers参数。

```js
var drake = dragula({
  copy: true
});
drake.containers.push(container);
```

你也可以在options对象中设置containers参数。

```js
var drake = dragula({containers: containers});
```

你也可以不设置任何参数，没有containers和options对象参数。

```js
var drake = dragula();
```

下面是options细节。

### options.containers

设置该项和通过`dragula(containers, options)`设置第一个参数效果一样。

### options.isContainer

除了通过dragula指定的containers参数，或者给drake.containers动态push或unshift方法添加的containers，你也可以使用这个方法来为drake实例指定
逻辑序列的containers.

下面例子为这个drake实例将所有带CSS类**dragula-container**的DOM元素当做dragula containers.

```js
var drake = dragula({
  isContainer: function(el) {
    return el.classList.contains('dragula-container');
  }
});
```

### options.moves

你可以定义一个 **moves** 方法，当点击一个元素时，方法moves(el, source, handle, sibling)将被调用。如果该方法返回 **false**, 则不会开始一个拖
动事件，也不会阻止这个事件。句柄元素将是原始的单击目标，这有助于测试该元素是否是预期的“拖动句柄”。

### options.accepts

你可以用签名(el, target, source, sibling)来声明一个 **accepts** 方法。调用这个方法确保来自源容器的元素能够放在目标容器的兄弟元素之前。这个兄弟
元素可以为null，则元素将作为目标容器的最后一个元素放置。注意，如果设置了 **options.copy = true**， 放置的是元素的拷贝，而不是原始拖拽的元素。

同时注意，拖动开始的位置总是放置元素的有效位置， 即使 **accepts** 对所有用例返回false.

### options.copy






