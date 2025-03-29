---
created: 2025-03-22T23:10
updated: 2024-05-25T22:32
创建时间: 2025-03-22T23:10
更新时间: 2025-03-29T17:36
查看次数: 1
---
# Hexo 建站流程

## 安装环境

本地已经有了node环境和git环境，现在安装 Hexo

```shell
npm i -g hexo
```

然后跳转到指定的目录下，进行项目初始化

```shell
hexo init <folder_name>
cd <folder_name>
npm i
```

此时运行 `hexo server` 即可启动项目。



## 主题设置

引入 `butterfly` 主题

```shell
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
```

然后修改Hexo根目录下的 `_config.yml`，把主题改为 `butterfly`

然后安装 pug 以及 stylus 的渲染器插件（不安装直接启动项目有问题）

```
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

将 `theme/butterfly/_config.yml` 文件爱你中的内容复制一份，粘贴到根目录下新建的 `_config.butterfly.yml` 文件中，方便后续 `butterfly` 升级造成的配置问题



