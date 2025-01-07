```java
Unexpected mutation of “xxxx” prop vue/no-mutatiing-props
```

环境：

- eslint 7.32.0
- vue 2.6.11
- elementUI 2.15.1

在上述版本下，我使用 eslint 进行语法校验，发现报了一下错误：

![image-20211102095545509](https://oss.puppetdevz.top/image/note/253df3a085af6d21e599da9017239c6b.png)

<!--more-->

## 1.1 问题分析

eslint 会校验 vue 单向数据流。

vue 的单向数据流的概念如下：

- `父级prop` 的更新会向下流动到子组件中，子组件中所有的 prop 都将会刷新为最新的值
- 但是反过来则不行。你不应该在一个子组件内部改变 prop。否则 Vue 会在浏览器的控制台中发出如上图的警告

> Vue 阻止从子组件意外变更父级组件的状态内容，因为这样会导致应用的数据流向杂乱无章。

对此，eslint 能够进行捕获并报错提示。解决这个问题，只需要按照 vue 官方给出的建议进行修改即可。

## 1.2 问题解决

vue 官方建议如下：

- 子组件中的 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。在这种情况下，最好定义一个 `本地的 data prop` 并将这个本地 prop 当作其初始值：

  ```js
  props: ['goodsItem'],
  data: function () {
    return {
      localGoods: this.goodsItem
    }
  }
  ```

- 如果这个 prop 以一种原始的值传入且需要进行转换。在这种情况下，最好使用这个 prop 的值来定义一个计算属性：

  ```js
  props: ['goodsItem'],
  computed: {
    normalizedSize: function () {
      return this.goodsItem.trim().toLowerCase()
    }
  }
  ```

这里我们采用第一种方式就可以解决，定义一个 localGoods 去接收父组件传递进来的 prop

> 注意：在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变变更这个对象或数组本身将会影响到父组件的状态。
