1. 安装docker

2. 下载jenkins镜像

   1. docker pull images:tag

3. 安装jenkins

   1. 在docker中启动一个jenkins容器 指定端口号 绑定目录

      1. 绑定这两个

         "/data/jenkins:/var/jenkins_home"                "/var/run/docker.sock:/var/run/docker.sock"

   2. 进入容器 命令（docker exec -it containerId bash）

4. 安装jenkins插件

   1. [NodeJS Plugin](https://plugins.jenkins.io/nodejs)

   2. [Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh)   系统设置 最下方 passphrase是密码

5. 配置jenkins

   1. node环境
      1. 可恶的node-sass（用淘宝源）
   2. [可选]安装yarn
   3. ssh配置【服务器+jenkins】【密钥/密码】

6. 配置项目

   1. General
      1. 描述
      2. 选择github项目，填写url
   2. 源码管理
      1. git
      2. git地址
      3. 添加Credentials(github账号，如果是公开的可不填)
      4. 选择分支
   3. 构建环境provide Node&.... (需要jenkins插件 NodeJS Plugin)
   4. 填写构建命令
      1. 执行shell
         1. 装依赖
         2. 打包
      2. send files or execute commands over ssh (需要jenkins插件Publish Over SSH)
         1. Source files 可选文件夹或文件
         2. Remove prefix 移除source files 的开头部分(可选)
         3. Remote directory 复制到的目标目录
         4. Exec command 执行的命令

7. 安装配置nginx

   1. 同安装jenkins

   2. 启动容器

      ```bash
      docker run -p 80:80 -v /home/www/:/home/www/ -v /home/www/nginx.conf:/etc/nginx/nginx.conf -u root -d nginx
      ```

      

      

      

8. 完工

      

项目流程 

执行jenkins构建

jenkins拉取git代码

jenkins执行构建并

[保存构建结果 test.20191223.1.tar]

jenkins发送构建结果至目标服务器



回滚

执行jenkins回滚命令

找到需要回滚的代码

jenkins发送构建结果至目标服务器

