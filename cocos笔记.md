# cocos笔记

## Hello world

1. 下载cocos create
2. 下载游戏引擎
3. 创建一个新项目
4. 打开编辑器，点击运行按钮

## 一、节点/层级

1. 树形结构
2. 子节点定位基于父元素
3. 基于锚点确认位置
4. 默认锚点为(0.5,0.5),节点左下角为(0,0),右上角为(1,1) 
5. 节点默认的属性(2D)
   1. position 位置 (x,y)
   
   2. rotation 旋转(z)
   
   3. scale 缩放
   
   4. layer 层
   
      

## 二、资源/文件

1. 场景(.scene)

2. 图片

   1. 精灵图 SpriteFrame （由*.png/ *.jpg 等图片资源生成 ）
      1. 九宫格图。省空间，自由拉伸。使用方式：1. SpriteFrame->edit->拖动线条，选择区域。2. Sprite组件->type->sliced
      2. 进度条。type选择filled，fillType按需求选择横向**HORIZONTAL**或纵向**VERTICAL**。fillStart: 0 -1 fillRange:0-1
   2. 图集 Atlas （plist文件+png文件）包含多个SpriteFrame

3. 动画

   1. 缓动系统
   2. Animation Clip .anim
   3. 骨骼/龙骨动画（动效师提供，ske.json+tex.json+tex.png）

4. 脚本（.ts）

   1. 装饰器
      1. 类
      2. 属性
   2. 生命周期函数
   3. 常用api
      1. node 获取当前节点
      2. node.children 获取子节点
      3. node.active 获取/设置激活状态
      4. getComponent() 获取节点上的组件
      5. getPosition(),setPosition() 获取/设置位置
      6. find('Canvas/x/x') find('child',parentNode) 根据路径获取节点 
      7. assetManager.loadRemote(url,callback) 获取远端资源

5. 预制体 (.prefab)

6. 其他资源

   1. 音乐

   2. 字体
      1. 一般字体（*.ttf）

      2. 位图字体 （.fnt+.png  由专门的软件生成）

         

## 三、场景

​		在 Cocos Creator 3.0 中，游戏场景（Scene）是游戏开发时组织游戏内容的中心，也是呈现给玩家所有游戏内容的载体。而场景文件本身也作为游戏资源存在，并保存了游戏的大部分信息，也是创作的基础。

​		**1. 创建** 在 **资源管理器** 中右键点击想要放置场景文件的文件夹，然后选择 **创建 -> Scene** 即可。

​		**2. 场景切换** 如果需要在游戏过程中切换场景，可通过 [director.loadScene](https://docs.cocos.com/creator/3.5/api/zh/class/Director?id=loadscene) 等 API 来实现游戏中动态场景加载及切换。

## 四、常用组件

1. UITransfrom  设置大小、锚点
2. UIOpacity 设置透明度
3. Sprite  显示图像
   1. 通过属性 spriteFrame 设置贴图
4. Label 显示文字
   1. 通过属性 string 设置显示的内容
5. 动画组件
   1. Animation cocos动画组件
   2. dragonBones.ArmatureDisplay 龙骨动画组件
   3. spine
   4. 等等
6. Button 
   1. 通过clickEvents 绑定点击事件，需要参数  节点，节点组件，方法名
7. Layout
   1. 自动布局，可以设置容器内部节点排序方向、容器或节点缩放的逻辑
8. ScrollView
9. Mask
10. Widget




1. 场景

   1. 切换场景     cc.director.loadScene('game')
2. 组件

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
3. tween

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


