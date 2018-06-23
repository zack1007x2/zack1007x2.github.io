---
layout: post
title: “docker練習-簡易remote compiler”
date: 2018-06-23
backgrounds:
- https://lh3.googleusercontent.com/ua4SxNBvdWHW\_pXNE6CgLAl4pS6iqdue02CQEE0ErNYKn4a0WnUABbsOOynL5wQymTcN0CbRoZq9hF\_QHeYOjVRBP611d6scXE\_umXBcGMbQ2V\_bHb\_-htdWbUcj9SKxXvY6MJ2wWhQ=w2400
thumb: https://lh3.googleusercontent.com/wTIhpYFnFM-HP6itoA778orBB4LdQ_eKJtKi-nrytngoGC4QU148SxAWENdnscP2WNC4k-aKa9PaC2WeTETUV1TuOEn-gGWkfO0hCJ0ZBv773rmGYxjucVsDiwOfUZyx_3v9W3UAVA=w2400
category: 研究生日記
tags: [docker,  python]
---

## 課堂筆記
### 使用docker build
使用Dockerfile 建立client image 並上傳docker hub
client Dockerfile 如下
``` python
# build image from src image
FROM ubuntu  

# Create app directory
WORKDIR /usr/src/app
# copy build(folder) from (host) .../build to   (container)/usr/src/app
COPY build ./

# install necessary tools
RUN apt-get update
RUN apt-get install -y inotify-tools
RUN apt-get install -y vim
RUN apt-get install -y net-tools
RUN apt-get install -y openssh-server
RUN apt-get install -y python
RUN mkdir -p /usr/src/app/src
RUN mkdir -p /usr/src/app/result
RUN chmod 777 ./run.sh

# port for socket
EXPOSE 5000
```

server 與其類似多增加些compiler功能

使用`docker build . `
建立client image 之後使用
`docker commit`, `docker push`
上傳至docker hub

### 使用docker-compose
docker-compose檔如下
``` yml
version: '3'
services:
#建立兩個container : client, server
#第一個container 
#client 使用剛剛建立並上傳到dockerhub的image 
#將container 命名為client1
    client:
        image: dockerhub_id/demo_client:latest
        container_name: client1
# 防止無任何工作的container關閉
    tty: true
        stdin_open: true
# 開啟container的port方便從外部連線 ssh-\>22, python socket-\>5000
    expose:
        - "22"
        - "5000"
# 建立後執行sh
    command: '/usr/src/app/run.sh'
# 使用自定義的bridge連接兩台container
    networks:
        mybridge:
            ipv4_address: 172.28.1.2
# 使用bind mount共享host資料夾
    volumes:
        - /Users/Zack/Documents/DockerFile/client/src:/usr/src/app/src
        - /Users/Zack/Documents/DockerFile/client/result:/usr/src/app/result
#第二個container 名為server1
#使用其Dockerfile建立在local
server:
    build:
#docker file 路徑及檔名
        context: ./server
        dockerfile: Dockerfile
        container_name: server1
    tty: true
    stdin_open: true
    expose:
        - "22"
        - "5001"
    command: '/usr/src/app/run.sh'
    networks:
        mybridge:
            ipv4_address: 172.28.1.3
# 使用底下建立的volume作為其儲存空間
    volumes:
        - server-data:/usr/src/app/src
# 建立自訂義volume
volumes:
    server-data:
# 建立自訂義bridge
networks:
    mybridge:
        driver: bridge
        ipam:
        driver: default
        config:
            - subnet: 172.28.0.0/16
```

`docker-compose up`
運行這兩個container


### remote compiler
#### 架構
[![][image-1]][1]
#### client端
container run起來後會執行此程式
run.sh
``` python
	#!/bin/bash
	#執行receiver_cli.py 等待server container回傳compile結果
	#監聽 /usr/src/app/src 有無新的檔案建立
	#若有就執行sent.py 傳送該檔案給server container
	inotifywait -m /usr/src/app/src -e close_write |
	    while read path action file; do
	          echo "The file '$file' appeared in directory '$path' via '$action'"
	        #upload to server
	        ext="${file##*.}"
	        filename="${file%.*}"
	        case "${ext}" in
	          c)
	           python sent.py $file
	           ;;
	          cpp)
	           python sent.py $file
	           ;;
	          java)
	           python sent.py $file
	           ;;
	        esac
	done
```

當sh執行 偵測到建立新的 .c/.cpp/.java 會執行此程式

sent.py
```python
	import socket
	import sys
	#python sent.py arg1 此參數為檔名
	arg1 = sys.argv[1]
	print arg1
	
	#傳送目標的 addr 
	HOST = '172.28.1.3'
	PORT = 5001
	ADDR = (HOST,PORT)
	BUFSIZE = 4096
	#欲傳送的檔案
	srcfile = "./src/"
	srcfile += arg1
	
	#組成欲傳送的檔案
	bytes = arg1+'@%@%@'+open(srcfile).read()
	
	print len(bytes)
	
	
	client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	print "open"
	client.connect(ADDR)
	print "connected"
	client.send(bytes)
	
	client.close()
```

另外有個等待接收compiler產物的receiver

receiver\_cli.py
```python
	import socket
	import sys
	
	#設定自身為receiver server
	HOST = '172.28.1.2'
	PORT = 5000
	ADDR = (HOST,PORT)
	BUFSIZE = 4096
	
	serv = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	
	serv.bind(ADDR)
	serv.listen(5)
	
	print 'listening ...'
	
	while True:
	  conn, addr = serv.accept()
	  print 'client connected ... ', addr
	  getFileName=False
	
	#監聽通道 當有檔案進來時@%@%@分割檔名及內容
	  while True:
	    data = conn.recv(BUFSIZE)
	    if not data: break
	
	    if not getFileName:
	#將結果存在./result/
	      myfile = open('./result/' + data.split('@%@%@')[0], 'w')
	      print 'receive file : ' + data.split('@%@%@')[0]
	      getFileName = True
	      myfile.write(data.split('@%@%@')[1])
	    else:
	      myfile.write(data)
	    print 'writing file ....'
	
	  myfile.close()
	  print 'finished writing file'
	
	conn.close()
	#執行完會重新accept connection 等待下一個檔案
	print 'client disconnected'
```

server端作法類似
詳見[https://github.com/zack1007x2/hw2\_docker\_pratice][2]

[1]:	https://lh3.googleusercontent.com/Qqa_B-vv2N1HG96FPKl11-3oCqDMwh2YIcKnQ8OViejZS-l7SZpL718frqW8khNYtlrNt-iCsF3Q1-5_XAkPJ8He8YvTHbJGK_tKNs5uasFTKqkzT2Hp-JnNmNo6q5SnCOcUyeCkYw=w2400
[2]:	https://github.com/zack1007x2/hw2_docker_pratice

[image-1]:	https://lh3.googleusercontent.com/nQynC_oweHlz95v5f-kaEz3WOouWnSl_PNiYkQIGcjtgnTJebpad3BDMkYv9yHRkIHhLRRn0CCsnCVHH2MJAqCgW6JUcYw01c_ZCstluRVt41Y_fm67KINAwXYoPb7kxJ_4mRi_dzA=w2400