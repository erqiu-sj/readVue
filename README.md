# Shijie qiu 阅读源码准用

重点

- 最核心整个的运行时：`**/packages/runtime-core`

- 基于自定义 dom 的入口在 src/index.ts `**/packages/runtime-dom`

## VueRander 加载都发生了什么？

首先会在 `**/packages/runtime-core/renderer.ts`中 388 行 baseCreateRender 创建 App 并且初始化,基于 rootComponents 生成 vnode

然后运行 render 调用 patch `429行` 基于 vnode 的类型进行不同类型的组件处理

此时会产生两个分支

- 处理 `shapeFlag & ShapeFlags.COMPONENT` 类型

  组件初始化并且先创建 `compoents` `instance` 对象

  后 `setup components` 初始化 `props` `slots` 调用 `setup()` 设置 `runder` 函数

  第三步 调用 `setupRenderEffect` 渲染函数 `在1218行`调用 `render` 函数获取 `vnode (子组件)` 触发生命周期 `beforemount hook`

  调用 `patch` 初始化子组件 (递归) 后触发生命周期 `mounted hook`

  完成组件更新

- 处理 shapeFlag & ShapeFlags.TELEPORT 类型

  先 element 初始化 调用 `hostCreateElement()` 创建真实的 element

  后处理 children 节点 开始判断 文字类型会调用 `hostSetElementText()` 数组类型则会循环调用 `patch()`

  再调用`hostPatchProp()`设置元素的 prop

  触发 `beformount()` 钩子

  再调用渲染函数 `hostInsert()` 插入真实的 dom 树

  触发 `Mounted()` 钩子

  完成 element 更新

这上千行的 rander 函数把我眼泪看出来了
