# 一、卸载当前node

1.使用 IObit Uninstaller 卸载nodejs

2.删除环境变量

3.从下列的目录中找到相关的内容并删除掉：
C:\Program Files (x86)\nodejs
C:\Program Files\nodejs
C:\Users\{User}\AppData\Roaming\npm（或%appdata%\npm）
C:\Users\{User}\AppData\Roaming\npm-cache（或%appdata%\npm-cache）

# 二、安装nvm

地址：https://github.com/coreybutler/nvm-windows/releases/tag/1.1.12

下载安装包点击安装

cmd 运行 nvm 命令，查看是否安装成功：

```shell
Running version 1.1.12.
```

**配置国内源**

到你NVM安装路径，打开setting.txt文件，追加：

```shell
# node使用淘宝源
node_mirror: http://npm.taobao.org/mirrors/node/ 
# npm使用淘宝源
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

# 三、安装node

```shell
nvm list
```

显示没有可用安装

安装命令

```shell
nvm install 12.1.0
nvm use 12.1.0
node -v
```



# 四、安装cnpm

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

# 五、各种报错

## 报错一

```shell
> chromedriver@2.27.2 install C:\Users\HAIBIN\Desktop\renren-fast-vue\node_modules\chromedriver
> node install.js

Downloading https://chromedriver.storage.googleapis.com/2.27/chromedriver_win32.zip
Saving to C:\Users\HAIBIN\AppData\Local\Temp\chromedriver\chromedriver_win32.zip
events.js:173
      throw er; // Unhandled 'error' event
      ^

Error: connect ECONNREFUSED 142.251.42.251:443
    at TCPConnectWrap.afterConnect [as oncomplete] (net.js:1054:14)
Emitted 'error' event at:
    at TLSSocket.socketErrorListener (_http_client.js:402:9)
    at TLSSocket.emit (events.js:196:13)
    at emitErrorNT (internal/streams/destroy.js:91:8)
    at emitErrorAndCloseNT (internal/streams/destroy.js:59:3)
    at processTicksAndRejections (internal/process/task_queues.js:84:17)
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.9 (node_modules\fsevents):
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: ENOTEMPTY: directory not empty, rmdir 'C:\Users\HAIBIN\Desktop\renren-fast-vue\node_modules\.staging\fsevents-198323bd\node_modules'

npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! chromedriver@2.27.2 install: `node install.js`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the chromedriver@2.27.2 install script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\HAIBIN\AppData\Roaming\npm-cache\_logs\2024-01-11T07_34_17_898Z-debug.log
```

如果报错chromedriver,先删除项目里的node_nodules

报错的信息里有个chromedriver的链接：https://chromedriver.storage.googleapis.com/2.27/chromedriver_win32.zip

点进去下载

下载后安装

```
npm install chromedriver --chromedriver_filepath=D:\download\chromedriver_win32.zip
```

改成自己的下载位置

再次

```shell
npm install
npm run dev
```

## 报错二

```shell
npm ERR! cb() never called!

npm ERR! This is an error with npm itself. Please report this error at:
npm ERR!     <https://npm.community>

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\HAIBIN\AppData\Roaming\npm-cache\_logs\2024-01-11T07_40_42_854Z-debug.log
```

1. 删除下载好的node_modules
2. 删除package-lock.json文件
3. 清除npm缓存 npm cache clean --force
4. npm install