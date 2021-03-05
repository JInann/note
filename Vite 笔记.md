#### Vite + vue2 与 webpack/vue-cli + vue2的比较

##### 目录不同

​	vite 根目录即为代码目录

​	webpack 一般上代码都在src中，根目录多为配置文件、项目文件

##### import/require 不同

​	vite 多了import.meta.glob/ import.meta.globEager 方法

​		glob(filePath:string) 返回 `{path1:():Promise<module>=>{...},path2:...}`  filePath 可匹配多个文件 `eg ./img/*.*`  filePath 只能为字符串 不能为变量，拼接也不行

​		globEager(filePath:string) 返回的结构基本同上，但是值不为返回promise的方法，直接返回实际值

​	vite在js脚本中没有require，在template中可以使用require

​	导入vue文件时，须带后缀`.vue`

##### 入口文件不同

​	入口文件为html，且须在html中以module形式引入入口js

```
<script type="module" src="./main.js"></script>	
```

##### 多页面配置

```javascript
import { defineConfig } from 'vite'
const  { createVuePlugin} = require('vite-plugin-vue2')
import {resolve} from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [createVuePlugin()],
  build:{
    rollupOptions:{
      input:{
        'check-in':resolve(__dirname,'pages/check-in/index.html'),
        'steps-challenge':resolve(__dirname,'pages/steps-challenge/index.html'),
      }
    }
  }
})
```

​	***入口文件是什么路径 打包后的html就是什么路径***	

​	如，上面的check-in 打包后在dist/pages/check-in/index.html。但是打包后的资源统一在dist/assets下。所以	如果设置base为 ‘./’ 相对路径，不适用与html文件不在根目录的情况。如要配置多页面，部署到网站根目录时无	须处理，部署子目录时bash须为具体路径。

##### 打包模式及环境变量

使用 `--mode xxx`切换环境

自动加载根目录下的 .env .env.xxx文件

```
NODE_ENV=production
VITE_APP_TITLE=production
```

在项目代码中使用import.meta.env访问 `VITE_`开头的环境变量

在vite.config.js中

```javascript
import { defineConfig, loadEnv } from 'vite'
export default ({mode})=>{
	let env = loadEnv(mode,'.')
	return defineConfig({
		// ...config
	})
}

```

