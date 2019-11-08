---
layout: post
title: Data Binding各種比較
date: 2019-10-05
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: http://zack1007x2.github.io/assets/images/thumbs/kotlin.png
category: programming
tags: [kotlin, android]
---

### **LiveData vs. ObservableField**

LiveData和ObservableField都可作為viewModel中的被觀察變數

只是無論view 是否為visiable或gone

View都會接收到ObservableField中變數的變化而改變

可能會因為該View不是Activate狀態導致Crash

而LiveData正好能解決該問題

LiveData的特性為資料會根據生命週期來發出變數改變的callback

##### ref : [ livedata-vs-observablefield-for-data-binding ][1]

----

### **ViewModel  vs. AndroidViewModel**

![][image-1]
我們有兩種方式宣告view model

~~```java
public class MainViewModel extends ViewModel {
    ...
}
~~```

~~``` java
public class MainViewModel extends AndroidViewModel {
  ...
}
~~```

特色在於這兩種view model都會在綁定的活動完全死去才回收該Object

有就是說view的資料不會因為程式重新執行而被reset 

導致需要重跑資料等情況發生


而ViewModel 和activity/fragment的生命週期脫鉤，

因此不希望將context或activity/fragment 傳入ViewModel 中

可能會導致memory leak(activity/fragment 已被回收但仍有遺留在ViewModel的部分)

AndroidViewModel 則可以解決需要context的窘境

與ViewModel差異在於AndroidViewModel會提供application的context

----

### MutableLiveData vs. SingleLiveEvent

同樣發送內容改變通知給subscribe的view

但當configuration 出現變化時SingleLiveEvent只會傳送一次

而MutableLiveData可能會重複通知

因此對於提示訊息、畫面跳轉等動作就很適合用SingleLiveEvent來處理

使用方式跟MutableLiveData一樣

----

在Fragment中使用Data Binding的話可以在onCreateView使用inflate，並return binding.getRoot()

[1]:	https://stackoverflow.com/questions/53836429/livedata-vs-observablefield-for-data-binding

[image-1]:	https://developer.android.com/images/topic/libraries/architecture/viewmodel-lifecycle.pn