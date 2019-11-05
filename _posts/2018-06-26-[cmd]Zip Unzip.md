---
layout: post
title: "[CMD]常用指令"
date: 2018-06-26
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: https://lh3.googleusercontent.com/HAJ6n1YkKq8zy2TokpKN1fU0sv30tsdistq0wTdvlC-KE-aZw5sbSa6FOzGaCUMWlb8Gy9oJIC6_4_rxIyU0MyV-4VwycJea2PmSHz0Y_sgdWjYSjB7_wKWe3EQYWTGW8lhGzHLIhQ=s225-p-k
category: e04誰會記得
tags: [cmd]
---

- 壓縮

``` python
zip -r xxx.zip /.../xxx  
```

- 解壓縮

``` python
unzip xxx.zip
```

``` python
unrar -e xxx.rar
```

``` python
7za e xxx.7z
```

- 隱藏檔案

``` python
defaults write com.apple.finder AppleShowAllFiles TRUE
killall Finder
```

- 還原隱藏

``` python
defaults write com.apple.finder AppleShowAllFiles FALSE
killall Finder
```

- 批量轉副檔名
-- JAVA轉成TXT

``` python
find . -iname "*.java" -exec bash -c 'mv "$0" "${0%\.java}.txt"' {} \;
```

- 開啟第二個eclipse

``` python
open -n /Applications/Eclipse.app
```
