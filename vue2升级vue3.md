

# vue2 -> vue3

### 创建

```javascript
// main.ts
import { createApp } from "vue";
import App from "./App.vue";
const app = createApp(App);
app.mount("#app");
```

```html
<!-- index.html -->
...
<script type="module" src="./main.ts"></script>
...
```

### 使用router

```javascript
// router.ts
import { createRouter, createWebHashHistory } from "vue-router";
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: "/",
      component: () => import("./index.vue"),
    },
  ],
});
// main.ts
import router from "./router";
app.use(router);
// App.vue
import { useRoute, useRouter } from "vue-router";
const route = useRoute()
const router = useRouter()
```

### 状态机不再使用vuex，官方推荐[pinia](https://pinia.vuejs.org/)

```typescript
// store.ts
import { createPinia, defineStore } from "pinia";
export const useStore = defineStore("store", {
  state: () => {
    return {
      news: [],
      result: {},
    };
  },
  // could also be defined as
  // state: () => ({ count: 0 })
  getters: {},
  // 没有mutations了
  actions: {
    actionsName() {
    },
  },
});
// main.ts
import store from "./store";
app.use(store);
// App.vue
import { useStore } from "./store";
// 直接访问属性，调用方法
console.log(useStore().news)
useStore().actionsName(payload)
// 变更state
useStore().$patch({
    result:newResult,
});
```

### 打包工具 vue-cli =>  [vite](https://vitejs.cn/)

1. ##### 环境变量加载，只会暴露以`VITE_`开头的变量
```javascript
// 打包阶段 vite.config.ts 
import { defineConfig, loadEnv } from "vite";
let { VITE_ENTRY } = loadEnv('production or development', ".");
// 运行时
console.log(import.meta.env.VITE_APP_TITLE)
```
2. ##### 部分情况资源未找到不会报错，注意检查路径。

3. ##### 非预设的静态资源引入`assetsInclude: ["**/*.svga"]`,以url引入`import xxx from 'xx.asd?url'`

### css

1. ##### 安装依赖`npm i sass -D`

2. ##### `/deep/`变更为`:deep(选择器)`，例:`:deep(.tab-item){}`

3. ##### transition类名变更

   1. `.fade-leave` => `.fade-leave-from`
   2. `.fade-enter` => `.fade-enter-from`

### Composition Api

1. ##### data(){} 变为 ref(),reactive()

2. ##### watch、computer、生命周期钩子、nextTick等均由 'vue' 导出

### 定义组件

1. ##### 由原来的默认导出`export default {...options}` 变为使用函数后导出`export default defineComponent(options)`

2. ##### 异步组件使用`defineAsyncComponent()` 传入一个返回promise对象的函数，例:

   ```javascript
   const AsyncComp = defineAsyncComponent(() =>
     import('./components/MyComponent.vue')
   )
   ```

### volar+typescript+eslint+prettier

1. ##### 手动安装vscode插件volar，并在vue3项目中停用vetur

2. ##### 依赖

   ```json
   {
       "@types/node": "^17.0.21",
       "@typescript-eslint/eslint-plugin": "^5.14.0",
       "@typescript-eslint/parser": "^5.14.0",
       "eslint": "^8.10.0",
       "eslint-config-prettier": "^8.5.0",
       "eslint-plugin-prettier": "^4.0.0",
       "eslint-plugin-vue": "^8.5.0",
       "prettier": "^2.5.1",
       "sass": "^1.49.9",
       "typescript": "^4.5.4",
       "vue-tsc": "^0.29.8"
     } 
   ```
   
3. ##### 配置`.eslintrc.js`

   ```javascript
   module.exports = {
     parserOptions: {
       parser: "@typescript-eslint/parser",
     },
     plugins: ["@typescript-eslint"],
     extends: [
       // add more generic rulesets here, such as:
       // 'eslint:recommended',
       "plugin:@typescript-eslint/recommended",
       "plugin:vue/vue3-recommended",
       "plugin:prettier/recommended",
       // 'plugin:vue/recommended' // Use this if you are using Vue.js 2.x.
     ],
     rules: {
       // override/add rules settings here, such as:
       // 'vue/no-unused-vars': 'error'
       "vue/no-v-html": "off",
       "@typescript-eslint/ban-ts-comment": "off",
     },
   };
   
   ```

   

   