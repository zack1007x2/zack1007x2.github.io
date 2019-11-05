---
layout: post
title: 使用Regex做ReplaceAll
date: 2019-11-05
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: http://zack1007x2.github.io/assets/images/thumbs/han.jpg
category: programming
tags: [Regex, Kotlin, Android]
---
當我們接收到Json格式資料時
可以透過Serialize方式將JsonObject直接轉為目標Object
我們以UserSetting的簡單資料欄位舉例
```kotlin
data class UserSetting(
    @SerializedName("credit_info")
    var creditInfo: Any?,
    @SerializedName("mission_detect_range")
    var missionDetectRange: Double,
    @SerializedName("price_increase_alert")
    var priceIncreaseAlert: Boolean
)
```


表示我們在收到Json格式資料
透過Gson.fromJson(data, Userdata::class.java)作轉換時
json中的credit_info 會對應到UserSetting.creditInfo的欄位中

 不過當我們使用不同平台登入獲得使用者資料時
 其欄位名稱通常不盡相同
 可以透過alternate標籤來兼容不同欄位名稱的問題
 
 例如
 平台A creditInfo欄位可能為 credit_info 
 平台B creditInfo欄位可能為 credit
 這時我們可以將 @SerializedName("credit_info")  改為
 @SerializedName(value = "creditInfo", alternate = ["credit_info, credit"])
 來達到兼容的目的

因為欄位眾多考慮採用replaceAll方式搭配Regex作處理
首先
``` plaintext
find:          ("\w+")
replace:       value =$1, alternate=\[$1]
```
將
@SerializedName("credit\_info")
取代為
@SerializedName(value ="credit\_info", alternate=\["credit\_info"])

再使用
``` plaintext
find:          (\_)+(\B)+(\w+",)
replace:       \u$2$3
```
將 @SerializedName(value ="credit\_info", alternate=\["credit\_info"]) 取代為
 @SerializedName(value ="creditInfo", alternate=\["credit\_info"]) 


之後在 alternate欄位中便可隨意增加兼容的屬性
