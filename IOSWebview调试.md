#### 1.手机safari开启web调试功能

#### 2.客户端设置`com.apple.security.get-task-allow`为true，或者由客户端在他的电脑上直接打包到手机上。

#### 3. 安装`ios_webkit_debug_proxy`

   用于与ios webview通信

   - http://localhost:9221/json 设备列表
   - http://localhost:9222/json 设备上打开的页面
   - [ws://localhost:9222/devtools/page/1](https://github.com/google/ios-webkit-debug-proxy/blob/master) 页面ws调试地址

[GITHUB 地址](https://github.com/google/ios-webkit-debug-proxy)

#### 4. 4.1与4.2二选一即可

##### 4.1 安装`remotedebug-ios-webkit-adapter`

   ```
   npm i remotedebug-ios-webkit-adapter -g
   remotedebug_ios_webkit_adapter --port=9000
   ```

   由于iOS webkit和chrome 的调试协议不同，这个包相当于ios webview与chrome devtools之间的一个翻译。

   在`chrome://inspect/#devices ` 中勾选 `Discover network targets`,并配置`127.0.0.1:9000`即可在chrome中调试ios webview。

   > 此包已于2021年3月29日停止维护

##### 4.2 安装`ios-safari-remote-debug-kit`

   先执行`generate`脚本，下载webkit

   然后复制对应版本的Protocol文件，如下

   ```bash
   # 复制文件
   /Users/jxh/Desktop/github/ios-safari-remote-debug-kit/src/WebKit/Source/WebInspectorUI/UserInterface/Protocol/Legacy/iOS/16.0/InspectorBackendCommands
   # 到
   /Users/jxh/Desktop/github/ios-safari-remote-debug-kit/src/WebKit/Source/WebInspectorUI/UserInterface/Protocol
   ```

   16.0 为版本号，需选择比手机版本小的。比如手机版本16.3，选择16.0

   执行`start`脚本，启动web服务。也可自行部署网站，文件夹为`UserInterface`。Main.html为入口文件。

   调试地址示例`http://$HOST:$PORT/Main.html?ws=localhost:9222/devtools/page/1`

[GITHUB 地址](https://github.com/HimbeersaftLP/ios-safari-remote-debug-kit)

#### 5.mac可直接跳过3,4步，直接使用Mac safari浏览器调试

   

   