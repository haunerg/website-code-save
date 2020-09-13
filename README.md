### hexo基本命令

描述 | 命令 | 缩写
--- | --- | --- 
生成静态页面 | hexo generate | hexo g
开启服务 | hexo server | hexo s
部署到github | hexo deploy | hexo d
新建文章 | hexo new "articleName" | -
新建页面 | hexo new page "htmlName" | -
查看帮助 | hexo help | - 
查看hexo版本号 | hexo version

### 组合命令
描述 | 命令 |
--- | --- |  
生成页面并预览 | hexo s -g | 
生成页面并上传到github | hexo d -g | 


图标更换地址： https://www.bootcss.com/p/font-awesome/#


## 错误处理

#### 1. 
```
fatal: in unpopulated submodule '.deploy_git'
FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous> (D:\100-github\website-code-save\node_modules\hexo-util\lib\spawn.js:51:21)
    at ChildProcess.emit (events.js:315:20)
    at ChildProcess.cp.emit (D:\100-github\website-code-save\node_modules\cross-spawn\lib\enoent.js:34:29)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:275:12)
```

#### 解决办法: 删除```.deploy_git```，重新打包上传部署。
