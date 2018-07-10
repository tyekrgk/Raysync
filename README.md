# Raysync
镭速传输引擎JS-SDK

更新时间：2018-07-10

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
#### 接口详情
##### 1. login
```javascript
// login登陆命令，传输流程必须先调用登陆
Raysync.login(accout,password)     //账号：account,密码：password。必须
.then(function(res){               

}
).catch(function(res){

})
```
##### 2. CreateWebSocket
```javascript
// CreateWebSocket 建立和传输客户端的连接
Raysync.CreateWebSocket (accout,password,lang)     //账号：account,密码：password；lang:语言 'zh-CN'中文，'en-US'英文

Raysync.obserable.on(‘webSocket’,function(){     //注册事件回调监听是否连接成功
    // res.result      0:成功，非0：失败
})

```
##### 3. getFileList
```javascript
// getFileList 获取文件列表
Raysync.getFileList(url)                       //url：服务器上文件路径

Raysync.obserable.on(‘LS’,function(res){            //注册事件获取返回的列表信息
    // res.result    状态，0为正常，非0失败  
    // res.list       文件列表数据，包含name（文件名）,size（大小）,time（最后修改时间）,type（是否为文件夹）信息
    // res.Dir       当前列表对应的服务器路径
    // res.message  信息结果说明
})

```
##### 4. upload
```javascript
// upload 上传文件/文件夹
Raysync.upload(type，uploadTo)                  //type：0：文件；1：文件夹
                                                // uploadTo：上传至服务器路径

```
##### 5. download
```javascript
// download 下载文件/文件夹
Raysync.download(list)                  //list：文件全路径组成的数组，
// 如：
[
‘/test/a.html’,
‘/test/b.js’
]

```
##### 6. RM
```javascript
// RM 删除文件/文件夹
Raysync.download(list)                  //list：文件全路径组成的数组，
// 如：
[
‘/test/a.html’,
‘/test/b.js’
]

```
##### 7. MKdirRequest
```javascript
// MKdirRequest 新建文件夹
Raysync.MKdirRequest(dir,url)                      //dir：新建文件夹名，url：服务器路径，即哪个目录//下新建

Raysync.obserable.on(‘MKDIR’,function(res){        //注册事件回调监听是否新建成功
    //res.result      0成功；非0失败
})
```
##### 8. rename
```javascript
// rename 重命名
Raysync.rename(oldname,newname)                     //oldname：新名，newname：旧名。以全路径形式传递，如：‘/test/a.html’更名‘/test/b.html’

Raysync.obserable.on(‘rename’,function(res){        //注册事件回调监听是否成功
    //res.result      0成功；非0失败
})
```
##### 9. getTranscount
```javascript
// getTranscount 获取当前用户正在传输的任务数
Raysync.getTranscount()                                    // 无参数需求

Raysync.obserable.on(‘GET_TRANS_COUNT’,function(res){      //注册事件回调监听是否成功
    //res.result      0成功；非0失败
    //res.count      当前任务数
})
```
##### 10. showTransferList
```javascript
// showTransferList 打开传输列表
Raysync.showTransferList()                                     // 无参数需求
```
