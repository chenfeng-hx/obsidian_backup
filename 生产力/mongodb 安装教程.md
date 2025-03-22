# mongodb 安装教程



以我的为例，下载的是压缩包文件，所以解压之后，创建 data 和 log 两个文件夹，如果是 msi 文件则会自动创建，在 data 目录中还需要再次创建一个 db 目录

![image-20240222171031624](http://images.xiaohai-hx.cn/复习笔记/面试题/image-20240222171031624.png)

然后在 bin 目录下执行命令

```shell
mongod -dbpath D:\Environment\mongodb\data\db
./mongod -dbpath D:\Environment\mongodb\data\db    
```



将 bin 目录放入环境变量中，然后用 mongosh 连接



## 开机自启

在log目录下创建mongo.log 文件，在 D:\Environment\mongodb\ 下创建 mongo.config 文件，内容如下：

```
dbpath=D:\Environment\mongo\data\db
logpath=D:\Environment\mongo\log\mogno.log
```

管理员身份打开cmd，执行：

```
D:\Environment\mongo\bin>mongod.exe --config "D:\Environment\mongo\mongo.config" --install --serviceName "MongoDB"
```

打开服务，找到 mongoDB 选项，点击启动服务即可

![image-20240413162345630](http://images.xiaohai-hx.cn/复习笔记/面试题/image-20240413162345630.png)

