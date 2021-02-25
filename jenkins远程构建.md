jenkins远程构建

1. 创建 apitoken

   1. 进入用户管理（右上角点击用户名）
   2. 进入设置
   3. Api Token 添加新Token
   4. 保存生成的apitoken
   5. 保存退出

2. 调用远程构建

   1. 拼接链接

      1. 不带参数的 http://username:token@jenkins.loaclhost/job/jobName/build
      2. 带参数的 http://username:token@jenkins.loaclhost/job/jobName/buildWithParameters?param1=xxx

   2. 请求拼接好的链接

   