# Raysync JS-SDK 文档
#### 概要
镭速传输引擎JS-SDK

#### SDK获取
- [Github地址](https://github.com/yangyi000/Raysync)
- npm install raysync
- [官网下载](https://www.raysync.cn)

#### 要求
- [部署最新镭速服务](https://www.raysync.cn)
- 安装镭速传输引擎
- ie11+，chrome 等支持websocket的浏览器

#### 版本说明
适用版本：最新版镭速

#### 快速开始/安装
**浏览器引入：**

```javascript
<script src="your path/raysync.min.js"> </script>
```

**使用npm安装SDK的开发包：**

```javascript
npm install raysync
```
成功安装完成之后，即可使用 import 或 require 进行引用。

```javascript
var raysync = require('Raysync')
new Raysync({webServer:'http://127.0.0.1:8090/api'});
```
`Raysync`接收一个`webServer`参数：部署`Raysync`服务的地址
```javascript
xxxx.xxxx.xxxx/api
```

### Raysync JS-SDK API说明
所有接口均为异步，与`webserver`通讯接口返回`Promise`，使用`.then()`处理返回结果，使用`.catch()`处理错误； 所有与镭速传输客户端的通讯接口在调用后采用事件注册的方法处理返回结果，如下：
```javascript
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

**关于上传下载的使用**

在成功建立客户端连接后，需要对传输系统做目录切换定位操作，即调用        `getFileList(url)`，此时`url`即为后续上传文件的目标位置，在重新调用      `getFileList`之前上传的文件都会在该目录下面，比如：
```javascript
Raysync.getFileList('/raysync/test') ;
```
此时会返回结果中的list目录为`/raysync/test`目录下的文件列表，此时调用upload上传文件会全部传至这个路径下面，如：
```javascript
Raysync.upload(type，path)  //type为上传类型：0：文件；1：文件夹
```
此时调用下载`download`接口对这个目录下的所有文件进行下载操作，如：
```javascript
Raysync.download(['/raysync/test/a.html','/raysync/test/b.html'])

```
此时就可以下载到该目录下的`a.html`和`b.html`，如需下载其他路径下的文件，需调用`getFileList(url)`切换至目标位置，然后调用下载即可

### 接口调用：
#### 1.login登陆命令，传输流程必须先调用登陆
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|account|string|账号|Y|
|password|string|密码|Y|
**返回值：**

```json
{
	type:'login',
	result:0, //0:成功；非0:失败    
	data:{
		userId:1001, // 用户id        
		account:'test', // 账号        
		client_version:'3.0.0.8', // 服务端对应客户端版本号
		isActived:true // license是否激活    
		},
	message:'login success'
}
```
**简单示例：**

```javascript
Raysync.login(accout,password).then(function(res){
	// 成功处理
}).catch(function(res){
	// 失败处理
})
```

### 2.CreateWebSocket 建立和传输客户端的连接
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|account|string|账号|Y|
|password|string|密码|Y|
**简单示例：**

```javascript
Raysync.CreateWebSocket (accout,password)     //账号：account,密码：password。必须
Raysync.obserable.on('webSocket',function(result){
//注册事件回调监听是否连接成功
// result = {"type":"webSocket","result":0,"message":"seccess:connected to raysync client","body":""}
})
```
### 3.getFileList获取文件列表
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|url|string|文件列表路径|Y|
**返回值：**


```json
{
	type：'LS'，
	result:0 //0:成功；非0:失败
	LS:[{
		name: '',// 文件/文件夹名
		Size:1000,//大小，单位Byte
		time:100000,//最后修改时间，单位s
		type:true,//是否为文件夹，true文件夹false文件
	}]
	Dir:''//当前列表路径
	message:'' //结果描述
}
```


**示例：**

```javascript
Raysync.getFileList(url)                       //url：服务器上文件路径
//注册事件获取返回的列表信息
Raysync.obserable.on('LS',function(res){
//res.result    状态，0为正常，非0失败
//res.list       文件列表数据，包含name（文件名）,size（大小）,time（最后修改时间）,type（是否为文件夹）信息
//res.Dir       当前列表对应的服务器路径
//res.message  信息结果说明
})
```

### 4.Upload上传文件/文件夹
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|type|string|0：文件；1：文件夹|Y|
|path|string|上传至服务端路径|Y|
**无返回**
**示例：**

```javascript
Raysync.upload(type,path)                  //type：0：文件；1：文件夹
```

### 5.Download下载文件/文件夹
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|list|Arry|由每个文件的路径组成的数组|Y|
**无返回**

**示例：**

```javascript
Raysync.download(list)                  //list：文件全路径组成的数组，//list：['/test/a.html','/test/b.js']
```

### 6.RM删除文件/文件夹
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|list|Arry|由每个文件的路径组成的数组|Y|
**无返回**
**示例：**

```javascript
Raysync.RM(list)                  //list：文件全路径组成的数组，//list：['/test/a.html','/test/b.js']
```

### 7.MKdirRequest新建文件夹
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|dir|string|新建文件夹名|Y|
|url|string|服务端路径|Y|
**返回值：**
```json
{
	result: 0, //  0成功；非0失败
	type: "MKDIR",
	message: 'MKDIR RESULT'
}
```
**示例：**
```javascript
Raysync.MKdirRequest(dir,url)         //dir：新建文件夹名，url：服务器路径，即哪个目录下新建
//注册事件回调监听是否新建成功
Raysync.obserable.on('MKDIR',function(res){
//res.result      0成功；非0失败
})
```
### 8.Rename重命名
**参数说明：**

| Key  | type  |说明|是否必须Y/N|
| ------------ | ------------ | ------------ | ------------ |
|oldname|string|新名（全路径形式传递）|Y|
|newname|string|j旧名（全路径形式传递）|Y|
**返回值：**
```json
{
	result: 0, //  0成功；非0失败
	type: "rename",
	message: 'rename RESULT'
}
```
**示例：**
```javascript
Raysync.rename(oldname,newname)              //oldname：新名，newname：旧名。以全路径形式传递，如：'/test/a.html'更名'/test/b.html'
Raysync.obserable.on('rename',function(res){        //注册事件回调监听是否成功//res.result      0成功；非0失败
})
```
### 9.getTranscount获取当前用户正在传输的任务数
**无参数**
**返回值：**
```json
{
	result:  0, //  0成功；非0失败
	count: 1, // 当前任务数量
	type: "GET_TRANS_COUNT",
	message: 'GET_TRANS_COUNT'
}
```
**示例：**
```javascript
Raysync.getTranscount()              // 无参数需求
//注册事件回调监听是否成功
Raysync.obserable.on('GET_TRANS_COUNT',function(res){
	//res.result      0成功；非0失败
	//res.count      当前任务数
})
```
### 10.showTransferList打开传输列表
**无参数**
**无返回**
**示例：**
```javascript
Raysync.showTransferList()              // 无参数需求
```
