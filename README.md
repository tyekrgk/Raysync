# Raysync
镭速传输引擎JS-SDK

跟新时间：2018-07-10

### SDK

* [Github地址](https://github.com/yangyi000/Raysync/)
* npm install raysync
* [官网下载](https://www.raysync.cn/)

#### 要求
* 部署镭速服务
* 安装镭速传输引擎
* ie11+，chrome 等支持websocket的浏览器

#### 安装
* 浏览器引入：
``` javascript

<script src="your path/raysync.min.js"></script>
```
* 使用npm安装SDK的开发包：
```javascript
npm install raysync
```
成功安装完成之后，即可使用 import 或 require 进行引用。
```javascript
var raysync = require('Raysync')
raysync.getFileList('/')
```
### Raysync JS-SDK API说明
所有接口均为异步，与webserver通讯接口返回Promise，使用`.then()`处理返回结果，使用`.catch()`处理错误；
所有与镭速传输客户端的通讯接口在调用后采用事件注册的方法处理返回结果，如下：<br>
``` javascript
// getFileList 获取服务器上对应目录文件列表
raysync.getFileList(url)          //url：服务器上文件路径

raysync.obserable.on('LS',function(res){
    // res.result     状态，0为正常，非0失败  
    // res.list       文件列表数据，包含name（文件名）,size（大小）,time（最后修改时间）,type（是否为文件夹）信息
    // res.Dir        当前列表对应的服务器路径
    // res.message    信息结果说明
})
// 注意：不要多次注册同一事件
```
