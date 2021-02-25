# vue 老旧项目使用typescript

### 先装包

```bash
npm i typescript ts-loader @types/webpack-env -D
```

### 创建tsconfig.json文件

 ```json
{
  "compilerOptions": {
    "sourceMap": true,
    "strict": true
  },
  "include": ["src/**/*"],
}

 ```

### 创建类型声明文件index.d.ts

```typescript
declare module '*.vue'{
  import Vue from 'vue'
  export default Vue
} // 解决引入vue文件报错 Cannot find module 'xxx.vue' or its corresponding type declarations.
declare const weixin:any // 解决使用全局变量报错 Cannot find name 'xxx'
interface DateConstructor{ // 拓展Date构造函数上的方法 
   getDateByAge:Function, // 解决Property 'prop' does not exist on type 'typeXXX'. 此类报错
}
interface Date{ // 拓展Date实例上的方法
  toFormat:Function
}

```

### 配置webpack

1. ##### 修改入口文件配置 entry

   比如原入口文件为 ./src/index.js 修改为 ./src/index.ts 

2. ##### 配置loader

   ```javascript
    {
      test: /\.ts$/,
        loader: 'ts-loader',
        options: { appendTsSuffixTo: [/\.vue$/] }
    },
   ```

3. ##### 配置resolve,添加ts

   ```js
   resolve: {
     // ...
   	extensions: ['.ts', '.js', '.vue', '.json'],
     // ...
   }
   ```

   

### 搬砖从文件重命名开始

 1. ##### 将你的js文件后缀名改为ts

 2. ##### 打开vue文件，将script部分 lang=”javascript“(如有) 改为 lang=”ts“

    ```vue
    <style>...</style>
    <template>...</template>
    <script lang="ts">
      // ...code
    </script>
    ```

 3. ##### 根据报错提示，修改代码。(字少事多坑大，手动滑稽)

### notes

1. 此方法仅适用用旧的js项目升级ts

2. 未引入eslint

3. 对于老项目来说，可以渐进式升级。部分使用ts，部分使用js。

4. ts文件内引入js时会报错并提示 `无法找到模块“./xxx”的声明文件。“/path/xxx.js”隐式拥有 "any" 类型。`。在类型声明文件中添加一条声明  `declare module '*/xxx'`,xxx为你的js模块名。

5. 在老项目中可能有部分公共模块，还在使用，可能由于历史原因，改造成本较大。可通过添加对应的类型声明文件，声明模块的方法，做到类型提示。例，现有一个 src/common/http文件,新建util.d.ts文件

   ```typescript
   declare module '*/common/util'{
     interface requestParams<T>{
       method:"get"|"post"|"delete"|'put'|string,
       url:string,
       data:T
     }
     export default class{
       post <requestT,responseT>(params:requestParams<requestT>):Promise<responseT>
       get <requestT,responseT>(params:requestParams<requestT>):Promise<responseT>
     }
   }
   ```

   

