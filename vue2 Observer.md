## Observer

- 使一个对象可以被观察

- 核心方法 defineReactive

- 通过Object.definedProrerty劫持对象的get,set访问器

  当对象被访问，get被调用，dep使target（watcher）依赖当前属性

  当对象被设置新值，set被调用，dep通知subs(订阅人)值更新（update）

## Watcher

- 传入要观察的表达式或函数及数据更新时的回调函数
- 将传入的表达式或函数转换成getter（）

- 将Dep的依赖对象（target）设为自己

  调用getter，返回value并保存此value，同时

  建立了依赖关系，把自己添加到dep的subs（订阅人列表）中

- 当update()被调用，更新value,并触发构造函数传入的回调函数，

### Dep

- depen() 在defineReactive方法的get中使用，建立target（watcher）与当前值的依赖关系
- notify() 在defineReactive方法的set中使用,当值更新时触发订阅人的update（）方法