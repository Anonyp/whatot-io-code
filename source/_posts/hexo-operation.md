title: hexo operation
date: 2015-02-03 15:11:52
categories: blog
tags: [blog, hexo]
---

## hexo相关链接

官网 http://hexo.io/
开始使用 http://hexo.io/docs/setup.html
插件列表 https://github.com/hexojs/hexo/wiki/Plugins

## 开始使用

```
    npm install hexo -g
    hexo init blog
    cd blog
    npm install
    hexo server
```


## 安装与卸载插件

在blog目录执行（以hexo-generator-feed为例）

安装插件
```
$ npm install hexo-generator-feed --save
```

卸载插件
```
$ npm uninstall hexo-generator-feed
```
> **手动卸载**：在blog根目录下的node_modules目录删除对应插件的目录，
然后手动删除插件在package.json文件中的项目，其次是删除相关配置文件。


## 更新blog组件，包括插件

在blog目录执行
```
$ npm update
```
实质上是通过blog目录的package.json文件更新各大组件


## 写blog流程

1. 创建新blog ``$ hexo new "新blog名"``
2. 编辑内容
3. 生成静态blog ``$ hexo generate``
4. 预览 ``$ hexo server``
5. 部署 ``$ hexo deploy``
