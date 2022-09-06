---
layout: post
title: “local push notification 以iOS與Android實現”
date: 2022-07-22
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: data:image/png;base64,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
category: programming
tags: [ios, android]
---

#Backend
使用node.js+socket.io
由server端進行推播

```javascript
const express = require('express');
const app = express();
const http = require('http');
const server = http.createServer(app);
const { Server } = require("socket.io");
const io = new Server(server);

var ip = require("ip");
console.log( 'Server addr:' + ip.address() + ': YOUR_PORT' );

//輸入輸出stream
const readline = require('readline').createInterface({
  input: process.stdin,
  output: process.stdout
});

//進行輸入
var recursiveAsyncReadLine = function () {
  readline.question('Command: ', function (answer) {
    if (answer == 'exit') //we need some base case, for recursion
      return readline.close(); //closing RL and returning from function.
    console.log('input:'+answer);

    io.sockets.emit('msg', answer);

    recursiveAsyncReadLine(); //Calling this function again to ask new question
  });
};

//io listener
io.on('connection', (client) => {
    console.log('Client connected');
    client.on('message', data => {
      data = data.toString()  
      console.log(data)
    });

});

server.listen(YOUR_PORT, () => {
  console.log('listening on *:YOUR_PORT');
});

recursiveAsyncReadLine()
```

該簡易server可做到以廣播方式進行推播
因此可同時作用在android and ios端

#iOS
##環境設定
需要實作NEAppPushProvider
權限需另外向Apple申請
[Local Push Connectivity Entitlement申請連結](https://developer.apple.com/documentation/networkextension/local_push_connectivity)
並於產生profile時添加
![entitlement](https://tva1.sinaimg.cn/large/e6c9d24egy1h4frik6g61j20u00ue77y.jpg)

建立新專案
並於target建立新的關聯項目
範例中命名為testpushprovider
![](https://tva1.sinaimg.cn/large/e6c9d24egy1h4frys5vzgj21bi0k0acb.jpg)

專案中使用

```
pod init
```

並於pod file中添加

``` 
pod 'Socket.IO-Client-Swift', '~> 16.0.1'
```

並使用安裝

```
pod install
```

如需移除

```
pod deintegrate
```

跟android gradle 中implement 差不多意思
上圖中可見```Build Phase > Link Lib將Socket.IO framework導入專案```

修改pushprovider中的info

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>NSExtension</key>
	<dict>
		<key>NSExtensionPointIdentifier</key>
		<string>com.apple.networkextension.app-push</string>
		<key>NSExtensionPrincipalClass</key>
		<string>$(PRODUCT_MODULE_NAME).TestPushProvider</string>
	</dict>
</dict>
</plist>
```

以及.entitlements添加所需權限

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>com.apple.developer.networking.networkextension</key>
	<array>
		<string>app-push-provider</string>
	</array>
	<key>com.apple.security.application-groups</key>
	<array>
		<string>group.com.ftc.testpushnotification</string>
	</array>
</dict>
</plist>
```


至於主程式的所需權限如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>com.apple.developer.networking.networkextension</key>
	<array>
		<string>app-push-provider</string>
	</array>
	<key>com.apple.developer.networking.wifi-info</key>
	<true/>
	<key>com.apple.security.application-groups</key>
	<array>
		<string>group.com.ftc.testpushnotification</string>
	</array>
</dict>
</plist>
```

並勾選
![](https://tva1.sinaimg.cn/large/e6c9d24egy1h4fs6akmdyj20u00iqq49.jpg)


##開發流程
找個地方設定NEAppPushManager

```swift
let mgr = manager ?? NEAppPushManager()
var websocketURLComponents = URLComponents()
websocketURLComponents.scheme = "http"
websocketURLComponents.host = "xx.xx.xx.xxx"
websocketURLComponents.port = YOUR_PORT
let websocketURLString = websocketURLComponents.string ?? ""
if websocketURLString != mgr.providerConfiguration["host"] as? String || mgr.matchSSIDs != onboardSSID || mgr.isEnabled == false {
    mgr.providerBundleIdentifier = pushProviderBundleIdentifier
    mgr.delegate = self
    mgr.isEnabled = true
    mgr.providerConfiguration = [
        "host": websocketURLString,
    ]
    mgr.matchSSIDs = onboardSSID //array 儲存wifi SSID最多十組
    mgr.saveToPreferences { error in
        if let error = error {
            print("Couldn't save push manager prefs: \(error)")
        }
    }
}
```
只要進到對應網域

pushprovider中實作socket
設定要監聽的event: self.onConnect, self.onDisconnect, self.onMessage
即可連線

```swift
manager = SocketManager(socketURL: url, config: config)
socket = manager!.defaultSocket
socket?.on(clientEvent: .connect, callback: self.onConnect)
socket?.on(clientEvent: .disconnect, callback: self.onDisconnect)
socket?.on("msg", callback: self.onMessage)
socket?.connect()
```

emit event在ios中不能用"message"原因我不太清楚
反正我是收不到
在這邊改用"msg"

--
Tip: 
pushprovider是由系統啟動
因此console裡看不到log
使用```cmd+shift+2 > open console```
用於觀測pushprovider是否成功運行

--

#Android

採用remote service(with AIDL)
避免app連同service一起關閉
背景同樣以socket連線

需注意AIDL
建立資料夾一般是在src/main/java....
AIDL則是在src/main/aidl...
檔名為.aidl
開立接口給app供未來使用

```java
interface ISocketServiceInterface {
    void connectSocket();
    void disConnectSocket();
    void sendMessage(in SocketMessage message);
    void addMessageListener(ISocketMessageListener listener);
    void removeMessageListener(ISocketMessageListener listener);
}
```

socket建立過程與ios差不多
畢竟同一套lib

```java
Socket socket = null;
try {
    socket = IO.socket("http://HOST:PORT");
} catch (URISyntaxException e) {
    e.printStackTrace();
}
socket.on(Socket.EVENT_CONNECT, onConnect);
socket.on(Socket.EVENT_DISCONNECT, onDisconnect);
socket.on(Socket.EVENT_CONNECT_ERROR, onConnectError);
socket.on("msg", onMessage);
socket.connect();
```








