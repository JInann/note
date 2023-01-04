图元

片元

裁剪坐标系

设备坐标系

数据用vec2 vec3 vec4存储



顶点着色器

​	gl_Position

​	gl_PointSize

片元着色器

​	gl_FragColor





```javascript
var canvas = document.querySelector('#canvas');
var gl = canvas.getContext('webgl') || canvas.getContext("experimental-webgl");

// 获取顶点着色器源码
var vertexShaderSource = document.querySelector('#vertexShader').innerHTML;
// 创建顶点着色器对象
var vertexShader = gl.createShader(gl.VERTEX_SHADER);
// 将源码分配给顶点着色器对象
gl.shaderSource(vertexShader, vertexShaderSource);
// 编译顶点着色器程序
gl.compileShader(vertexShader);

// 获取片元着色器源码
var fragmentShaderSource = document.querySelector('#fragmentShader').innerHTML;
// 创建片元着色器程序
var fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);
// 将源码分配给片元着色器对象
gl.shaderSource(fragmentShader, fragmentShaderSource);
// 编译片元着色器
gl.compileShader(fragmentShader);

//创建着色器程序
var program = gl.createProgram();
//将顶点着色器挂载在着色器程序上。
gl.attachShader(program, vertexShader); 
//将片元着色器挂载在着色器程序上。
gl.attachShader(program, fragmentShader);
//链接着色器程序
gl.linkProgram(program);

gl.useProgram(program);

//设置清空画布颜色为黑色。
gl.clearColor(0.0, 0.0, 0.0, 1.0);

//用上一步设置的清空画布颜色清空画布。
gl.clear(gl.COLOR_BUFFER_BIT);

//绘制点。
gl.drawArrays(gl.POINTS, 0, 1);
```



