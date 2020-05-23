# 起步

```javascript
option:{
    template:'{{count}}'
    data(){
        return {
            count:0
        }
    }
}
el = document.createElement('div')
createApp(option).mount(el)
```

option.template 取值

1. template 模板字符串
2. dom元素
3. 选择器
4. 不传值，在mount传入

# effect()

```javascript
let a 
let b = reactive({number:0})
effect(()=>{
	a = b.number
})
log(a===0) // true
b.number = 2
log(a===2) // true

```

支持多个属性

支持嵌套属性

```javascript
it('should observe delete operations', () => {
    let dummy
    const obj = reactive({ prop: 'value' })
    effect(() => (dummy = obj.prop))

    expect(dummy).toBe('value')
    delete obj.prop
    expect(dummy).toBe(undefined)
  })
```

支持has

```javascript
it('should observe has operations', () => {
    let dummy
    const obj = reactive<{ prop: string | number }>({ prop: 'value' })
    effect(() => (dummy = 'prop' in obj))

    expect(dummy).toBe(true)
    delete obj.prop
    expect(dummy).toBe(false)
    obj.prop = 12
    expect(dummy).toBe(true)
  })
```

原型链上的也可以

```javascript
 it('should observe properties on the prototype chain', () => {
    let dummy
    const counter = reactive({ num: 0 })
    const parentCounter = reactive({ num: 2 })
    Object.setPrototypeOf(counter, parentCounter)
    effect(() => (dummy = counter.num))

    expect(dummy).toBe(0)
    delete counter.num
    expect(dummy).toBe(2)
    parentCounter.num = 4
    expect(dummy).toBe(4)
    counter.num = 3
    expect(dummy).toBe(3)
  })
```



effect(fn)

返回fn（）的值

```javascript
// 暴露出去的
function effect(fn,option){
    if(fn is Effect){fn = fn.raw} //如果fn已经是effect了
    effect = createReactiveEffect(fn,option)// 工厂模式，创建effect实例
    if(!lazy) effect()  // lazy的不立即执行
    return effect
}
function createReactiveEffect(fn,option){
   const effect= function(){
      if(!effect.active) if(scheduler)return undefined else 1fn(...arg)
       
       if(!effectStack.includes(effect)){ // 解决的循环触发的问题，当effect1触发了effect2，effect2中又触发effect1时 effect1不会重新执行
       
       //清除依赖 effect每次执行都会重新收集依赖
       cleanup(effect)
       // 设置当前激活的effect为此effect
       enableTracking()
       activeEffect = effect
       // 执行方法收集依赖
       return fn() // 返回值
       // 重置当前激活的effect
       resetTracking()
       activeEffect = lastEffect
       }
   } 
    effect._isEffect = true
    effect.acitve = true;
    effect.raw = fn;
    effect.deps = [dep,dep] // dep是target.key的所有依赖，当target.key被触发，dep中所有effect被执行；当effect被stop， 会遍历这数组，把自己从此dep中删掉，这样，target.key更新时，自己就不会被触发
    effect.options = options
    return effect
}
```



### computed

computed 原理是effect

```javascript
function computed(getterOrOption){
    const getter = getter or option.getter
    const setter = option.setter
    let dirty = true
    const runner = effect(getter,{
        lazy:true,
        computed:true,
        scheduler(){
            // getter 中的数据更新了，触发所有依赖，重新求值
            if(!dirty){
                dirty = true
                trigger(computed,'set','value')
            }
        }
    })
    let value
    let computed={
        _isRef:true,
        effect:runner,
        get value(){
            if(dirty) value  = runner(), dirty = false
            track(...)
            return value
        },
        set value(){
            setter()
        }
    }
    return computed
}
```







```javascript
const targetMap={
    [obj]:{
        a:dep as Set
    } as depsMap
}
dep = [effect1,effect2,activeEffect]
activeEffect:{
    deps:[target.obj.a as dep,dep2,dep3]
}
let obj = {
	a:1
}
```



```javascript
function trigger(target,type,key,newValue){
    depsMap = targetMap[target]
    runlist = <effect>[]
    // type = clear
   	// depsMap中所有effect 都添加到队列中
    // type = clear 结束
    // 如果target是数组 且key=== length
    // 添加length的dep 和 大于newValue的dep到队列  数组部分更新，牛！
    // 结束
    // 如果是key！=void 0 添加对应key的dep到队列中
    // type = add delete set 看不懂
    // 运行队列
    // 有scheduler 执行scheduler 没有则执行effect // 分computedRunners 和普通effect 分开执行 先执行computedRunners
}
```

shallow---浅



ref/reactive/computed 将一个对象/值/fn 转换为observed

observedObj getter 可以触发 effect.tarck收集依赖,setter 可以触发effect.tirgger通知与此target.key相关联的所有effect









可监视的对象 reactiveObj

​	读取属性时，调用watch.get

​	设置属性时，调用watch.set

容纳 reactiveObj 的对象

​	把执行过程中 调用过watch.get的reactive保存下

​	当那些reactiveObj调用watch.set后要重新执行fn方法

通信对象 watch