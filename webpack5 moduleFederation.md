
## webpack5 moduleFederation 使用

### 简介：

在hostApp中暴露要给其他应用使用的模块，其他应用（clientApp）引入hostApp暴露的文件。在clientApp中像使用本地模块一样使用远程模块。HostApp和ClientApp 可互为host/client。即同一个应用，可同时提供/使用远程模块。

### 上代码：

##### 依赖 package.json：

```json
{
  // ... otherConfig
  devDependencies:{
  	// ... otherDevDependencies
    // "html-webpack-plugin": "^4.3.0",  踩坑：使用html-webpack-plugin 版本要用^4.3.0
    "vue-loader": "^15.9.3", 
	"webpack": "^5.0.0-beta.25",
    "webpack-cli": "^3.3.12",
    "webpack-dev-server": "^3.11.0"
	}
}
```

##### hostApp:

1.配置webpack.config.js

```javascript
// webpack.config.js
// 踩坑：使用vue的话，这里要改成以下方式
const VueLoaderPlugin = require('vue-loader/lib/plugin-webpack5');
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin')
{
  ...otherConfig,
  output:{
    publicPath:hostAppPath // hostApp访问路径，需要带域名/ip端口号 eg: http://localhost:9003/
  },
	...otherConfig,
  plugins:[
    	...otherConfig,
	    new ModuleFederationPlugin({
        // 导出包名
        name: 'hostApp',
        library: { type: 'var', name: 'hostApp' },
        // 入口文件名
        filename: 'remoteEntry.js',
        // 要导出的内容
        exposes: {
          // 模块名及模块的路径，模块名前面要加 "./" ,使用时 直接包名/模块名即可 
          // use: import('hostApp')
          '.':'./src/container',
          // use: import('hostApp/container') 
          './container': './src/container.js',
          // user: import('hostApp/path/path2')
         	'./path/path2': './src/container.js'
         
        }
    	})
  ]
}

```

2.写暴露的远程模块对应的文件

```javascript
// ./src/container.js
import comp from './comp' // vue组件
import util from './util' // js文件
export default comp
export util
```

##### clientApp:

1.在index.html中引入远程模块的入口文件

```html
<!-- index.html -->
<!-- 仅在hostApp配置了optimization.runtimeChunk时添加 -->
<script src="{{hostAppPath}}/{{optimization.runtimeChunk.name}}"></script> 
<!-- 这句是必须的,引入hostApp的远端入口文件 -->
<script src="{{hostAppPath}}/remoteEntry.js"></script>
```

2.配置webpack.config.js

```javascript
// webpack.config.js
new ModuleFederationPlugin({
  name: 'clientApp',
  // 在这里声明远程模块
  remotes: {
    hostApp: "hostApp",
  },
})
```

3.使用

```javascript
// otherjsfile.js
const container = await import('hostApp/container')
let comp = container.default
let util = container.util
```

```vue
// othervue.js
<template>
	<comp></comp>
</template>
<script>
export default {
  components:{
    comp:()=>import('hostApp/container')
  }
}
</script>
<style>
</style>
```



使用例子

app2+app3 互为host/client app2提供page1组件及util模块，app3提供page2组件。互相引用。

git地址：