cocos笔记

1. 层级管理

   1. 控件定位 基于父元素

2. 资源管理

   1. 文件/图片

   2. 场景

      1. 切换场景     cc.director.loadScene('game')

   3. 组件

      1. property 可以在属性检查器中修改 控件的修改要从层级管理器拖过去
         1. cc.Node Label Prefab
         2. string number
      2. onLoad()
      3. update()
         1. 每帧执行
      4. node.destory()
      5. component.onDestory()
      6. active
      7. position
      8. node
      9. getComponent
      10. cc.systemEvent.on
   11. tween
   
4. prefab
   
   1. 预制，手动实例化
   
         ```typescript
         let w = cc.instantiate(this.weapon);
         this.node.parent.addChild(w);
         w.setPosition(this.node.getPosition());
      ```
   
         

计时器

```js
 component.schedule(function() {
     // 这里的 this 指向 component
     this.doSomething();
 }, 5);

// 取消
this.count = 0;
 this.callback = function () {
     if (this.count === 5) {
         // 在第六次执行回调时取消这个计时器
         this.unschedule(this.callback);
     }
     this.doSomething();
     this.count++;
 }
 component.schedule(this.callback, 1);
```

tween中途结束要调用stop

对象池

let pool = new cc.NodePool()

new cc.NodePool('comp name')

class comp { unuser(){}  reuse(){}}

pool.put()

pool.get()

pool.size()

pool.clear()



不同的tween() 同时作用于一个节点 有目前解释不了的问题

事件冒泡要手动中止