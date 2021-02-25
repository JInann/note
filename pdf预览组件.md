# pdf预览组件

#### 1.初始化

```javascript
const loadComp = (url,path)=> ()=>new Promise((resolve)=>{
  const script = document.createElement('script')
  script.src = url
  script.type = "text/javascript"
  script.onload = ()=>{
    path = path.split('.')
    let target = window
    path.forEach(p=>{
      target = target[p]
    })
    resolve(target)
  }
  script.onerror = ()=>{
    resolve({
      render(h){
        return h('span',{},'error')
      }
    })
  }
  document.body.append(script)
})
export default {
  components:{
    'pdf':loadComp('http://open.anyitech.ltd/module/pdf.js','anyipdf.pdf')
  },
}
```



#### 2.使用

```vue
<pdf url="/doc/demo.pdf"></pdf>
```



#### 3.参数说明

| props  | 类型   | 说明                               |
| ------ | ------ | ---------------------------------- |
| url    | string | pdf文件的路径                      |
| height | string | 画布高度，css长度单位，默认值100vh |
|        |        |                                    |

​	



