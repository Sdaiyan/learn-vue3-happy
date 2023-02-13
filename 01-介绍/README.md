# Vue 原理
[Vue3 仓库](https://github.com/vuejs/core)

这里简化分成三部分来学习 Vue3 的核心模块

## 响应式模块 Reactivity
响应式模块实现了追踪 Js 变量，当这些变量发生变化时，我们可以收到通知，从而做一些操作，例如 **更新DOM**。

## 编译模块 Compiler
可以将 HTML 模版或者我们编写的 `<template>` 编译成 `render function`.

## 渲染模块 Render
渲染模块会经历以下的阶段

### 渲染 Render
此阶段会调用 `render function` 获得一个 VNode 

### 挂载 Mount
此阶段会根据 VNode 来创建 DOM

### 补丁 Patch
此阶段会对比旧的 VNode 和新的 VNode，获得变化的部分来更新到页面上

## 一个简单的例子
当我们编写一个这样的组件时

```html
<template>
  <div>{{ msg }}</div>
</template>

<script setup>
  import 'ref' from 'vue'
  const msg = ref('hello')
</script>
```

### 1. 编译
首先编译模块会将我们的 template 编译成一个 `render function`
例如 我们上面的 html 会被编译成这个样子 不用去理解它 只需要知道 `render function` 大概是什么样子就可以了

如果想知道你的 `<template>` 会被编译成什么样子，可以在这个网站预览

https://vue-next-template-explorer.netlify.app/

```javascript
import { toDisplayString as _toDisplayString, createElementVNode as _createElementVNode, openBlock as _openBlock, createElementBlock as _createElementBlock } from "vue"

export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("template", null, [
    _createElementVNode("div", null, _toDisplayString(_ctx.msg), 1 /* TEXT */)
  ]))
}
``` 

### 2. 初始化响应式模块
这个具体原理会在后面说到，这里实际上就是初始化了我们声明的响应式数据，并且监听他的读和写。

### 3. 渲染阶段
通过调用 `render function` 获取了 VNode。
同时因为调用了 `render function`，读取了 msg。触发了**依赖收集**。

### 4. 挂载
通过之前的 VNode 我们将还原一个真实的 Node 挂载到 DOM 上

### 5. 补丁
当 msg 发生变化时。因为经过**依赖收集**，视图会知道 msg 变化，Vue 会重新调用 `render function` 获得一个新的 VNode。
此时 Vue 将新的 VNode 和 旧的 VNode 进行对比。获得需要更新的部分，并且更新到 DOM 上。