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

这儿有一个警告。不能在你web应用的\<head>中引入js文件。在\<head>放置js引用是非常不好的习惯，dragula也不支持这样使用。

请把dragula引用放在\<body>标签中。

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


如果copy属性设为true（或返回值为true的方法），那么元素将被复制到拖放区，而不是移动到拖放区。这意味着以下的不同:


Event(事件) | Move(移动) | Copy（复制）
-----------| ------------|-----------
drag | 元素在源容器会被虚化 | 无事发生
drop | 元素被移动到目标位置 | 元素被克隆到目标位置
remove | 元素从DOM中删除 | 无事发生
cacel | 元素留在原容器中 | 无事发生

如果传递了一个方法，那么每当开始拖动一个元素时，就会调用它，以决定是否应该遵循复制行为。考虑如下例子：

```js
copy: function(el, source) {
  return el.className === 'you-may-copy-us';
}
```

### options.copySortSource

如果copy被设置为true(或返回true的方法)，并且copySortSource也为true，那么用户将能够对复制源容器中的元素进行排序。

```js
copy: true,
copySortSource: true
```

### options.revertOnSpill

默认情况下，将元素溢出到任何容器外都会将该元素移回由反馈阴影预览的下拉位置。将revertonoverflow设置为true将确保丢弃在任何已批准的容器之外的元素被移动回拖放事件开始的源元素，而不是停留在反馈阴影预览的拖放位置。

### options.removeOnSpill

默认情况下，将元素溢出到任何容器外都会将该元素移回由反馈阴影预览的下拉位置。将removeon溢出设置为true将确保从DOM中删除任何已批准容器之外的元素。注意，如果将copy设置为true，则remove事件不会触发。

### options.direction

当一个元素被放入容器中时，它将被放置在鼠标被释放的位置附近。如果方向是“垂直”(默认值)，则考虑Y轴。否则，如果方向为“水平”，则考虑X轴。

### options.invalid

你可以提供invalid(el, handle)方法。当不能触发drag事件的元素被拖动时这个方法会返回true值。句柄参数是单击的元素，而el是要拖动的项。
下面例子，默认不能阻止任何drag事件。

```js
function invalidTarget(el, handle) {
  return false;
}
```

注意，invalid方法会在单击的DOM元素和drake容器的所有父元素(直到直接子元素)上调用。

例如，当单击的元素(或其任何父元素)是锚标记时，可以将invalid设置为返回false。

```js
invalid: function(el, handle) {
  return el.tagName === 'A';
}
```

### options.mirrorContainer

拖动时显示镜像元素的DOM元素将被追加到其中。默认为document.body。

### options.ignoreInputTextSelection

启用此选项时，如果用户单击输入元素，则在其鼠标指针退出输入之前不会启动拖动。这意味着用户可以选择包含在可拖动元素中的输入中的文本，并且仍然可以通过将鼠标移到输入之外来拖动元素——这样您就可以同时获得这两个方面的好处。

默认情况下启用此选项。通过设置为false来关闭它。如果它被禁用，用户将无法使用鼠标在dragula容器中的输入中选择文本。

## API

dragula方法返回一个带有简洁API的小对象。我们将把dragula返回的API称为drake。

### drake.containers

此属性包含在构建此drake实例时传递给dragula的容器集合。您可以随意推动更多的容器和拼接旧容器。

### drake.dragging

当拖拽元素时，dragging属性为true.

### drake.start(item)

进入没有阴影的拖动模式。当为现有的拖放解决方案提供键盘快捷键时，此方法非常有用。即使一开始不会创建一个阴影，用户只要单击item并开始拖动它，
就会得到一个阴影。注意，如果他们单击并拖动其他东西，.end将在获取新项目之前被调用。

### drake.end()

优雅地结束拖动事件，就像使用由预览阴影标记的最后一个位置作为拖放目标一样。正确的取消或删除事件将被触发，这取决于项目是否被删除回它最初被提取
的位置(这本质上是一个被视为取消事件的no-op)。

### drake.cancel(revert)

如果drake管理的元素当前正在被拖动，该方法将优雅地取消拖动操作。您还可以在方法调用级别传递restore，有效地生成与revertonoverflow相同的结果。

### drake.remove()

### drake.on(Events)

drake是事件触发器。下面使用drake.on(type, listener)可以跟踪到下面的事件。


事件名称 | 事件参数 | 事件说明
--- | --- | ---
drag | el, source | el是从source提出来的
dragend | el | el的拖拽事件被cancel、remove或drop触发
drop | el, target, source, sibling | el被放在兄弟元素之前的目标位置
cancel | el, container, source | 拖拽el到外部地方时会放回原位置
remove | el, container, source | 拖拽el但没放置时将从DOM中删除
shadow | el, container, source | 
over | el, container, source | el在container中
out | el, container, source | 拖拽el到container以外的地方
cloned | clone, container, type | 

### drake.canMove(item)

返回drake实例是否可以接受DOM元素项的拖动。当满足以下所有条件时，此方法返回true，否则返回false。

- item是drake指定容器之一的子元素
- item 通过相关的invalid检查
- item通过一个moves检查

### drake.destroy()

删除dragula用于管理容器之间的拖放操作的所有拖放事件。如果在拖动元素时调用.destroy，则该拖动将被有效地取消。

## CSS 

Dragula只使用四个CSS类。下面将快速解释它们的用途，但是您可以查看dist/dragula。查看相应的css规则。

- 拖动时将gu-unselectable添加到mirrorContainer元素中。您可以在拖动某个对象时使用它来样式化mirrorContainer。
- 当拖动源元素的镜像时，将向其添加gu-transit。它只是增加了不透明度。
- 将gu-mirror添加到镜像中。它处理固定的定位和z索引(并删除元素上的任何先前空白)。请注意，镜像被附加到mirrorContainer，而不是它的初始容器。在使用嵌套规则对元素进行样式化时，请记住这一点，例如.list .item {padding: 10px;}。
- gui-hide是一个辅助类，设置元素display:none


