Vite使用命令行和环境变量实现一个仓库下多页面的单独打包

背景：多个h5页面放在同一个仓库下管理，使用[多页面应用模式](https://vitejs.cn/guide/build.html#multi-page-app)，每次打包/运行，会把所有页面都进行重新编译。随着页面数量增加，每次打包所需时间也会越来越长。

思路：通过动态设置打包入口，每次只运行/打包一个页面，速度大大提升。

解决方法：

在项目根目录创建.env文件，每次运行修改.env.local文件`VITE_ENTRY=pageName`，然后入口路径通过读取环境变量获取。

```javascript
export default defineConfig(async ({ mode }) => {
	let { VITE_ENTRY } = loadEnv(mode, ".");
  ...
  return {
    root: resolve(__dirname, "src/pages/" + VITE_ENTRY + "/"),
    ...
    build: {
        ...
        rollupOptions: {
          input: {
            main: resolve(
              __dirname,
              "src/pages/" + VITE_ENTRY + "/",
              "index.html"
            ),
          },
        },
        outDir: resolve(__dirname, "dist/", VITE_ENTRY),
      },
  }
} 

```

考虑到如果每次运行不同的页面，都需要手动修改`.env`文件 ，而且手动修改可能会出错，于是增加了命令行交互功能。通过代码读取仓库中的pages列表，然后使用`inquirer`包开发命令行交互功能。

```javascript
// 获取所有的页面名称，根据Mtime排序，最新修改的在前面
function getPagesOrderByMtime() {
  const pagesPath = "./src/pages/";
  const res = readdirSync(pagesPath)
    .filter((v) => v != ".DS_Store") // mac自动生成的文件，忽略
    .map((v) => ({
      name: v,
      mtime: statSync(
        pagesPath +
          v +
          "/" +
          readdirSync(pagesPath + v).reduce((p, c) => {
            if (
              statSync(pagesPath + v + "/" + c).mtime.valueOf() >
              statSync(pagesPath + v + "/" + p).mtime.valueOf()
            ) {
              p = c;
            }
            return p;
          })
      ).mtime.valueOf(),
    }));
  res.sort((a, b) => {
    return b.mtime - a.mtime;
  });
  return res.map((v) => v.name);
}


export default defineConfig(async ({ mode }) => {
	let { VITE_ENTRY } = loadEnv(mode, ".");
  if (!VITE_ENTRY) {
    const prompt = createPromptModule();
    const { entry } = await prompt([
      {
        type: "list",
        name: "entry",
        message: "请选择要运行的页面：",
        choices: getPagesOrderByMtime(),
      },
    ]);
    VITE_ENTRY = entry;
  }
  return {
    ...
  }
}
```

接下来执行vite脚本，会先从环境变量中读取入口路径，如果环境变量中没有，就会让用户选择一个入口来运行。

从开发的角度来说，有了控制台输入就可以删除从环境变量中读取这个逻辑。保留这个逻辑是为了支持CI/CD可以直接从环境变量中读取。

目前很多自动构建流程都是代码推送到远端后自动触发的。由于修改了打包逻辑，所以需要简单配置下，改为手动触发。比如gitlab来说，需要配置读取不到`VITE_ENTRY`变量时不执行流水线，然后在gitlab上运行流水线的时候，填写要构建的页面路径

```yml
workflow:
  rules:
    - if: $VITE_ENTRY
      when: always
    - when: never
```



