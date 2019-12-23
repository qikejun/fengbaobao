---
title: vue之nextTick
date: 2019-11-11 14:11:27
categories:
  - Vue
  - Vue
tags: Vue
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoer6.jpg'
---

## nextTick 使用

#### 使用场景

* 需要在视图更新之后，基于新的视图进行操作。 

> 动态改变视图，接着基于更新后视图，获取属性(内容，长宽，属性值等在改变后会相应改变的值)或操作时，在nextTick中操作

```js
//改变数据
vm.message = 'changed'

//直接获取相关节点的内容，获取不到
console.log(vm.$el.textContent) // 并不会得到'changed'

//nextTick
Vue.nextTick(function(){
    console.log(vm.$el.textContent) //可以得到'changed'
})
```

#### created、mounted

需要注意的是，在 created 和 mounted 阶段，如果需要操作渲染后的试图，也要使用 nextTick 方法。

官方文档说明：

> 注意 mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 替换掉 mounted

```js
mounted: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}
```

