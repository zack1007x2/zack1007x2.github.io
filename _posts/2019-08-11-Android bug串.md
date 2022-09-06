---
layout: post
title: "Android bug串"
date: 2019-08-11
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: http://zack1007x2.github.io/assets/images/thumbs/android.jpg
category: programming
tags: [android]
---



## 前言

#### 關於EditText

數字前有0

方法一利用Regex 過濾不符規則的字串

方法二如下

```kotlin
fun trimLeadingZeros(string: String): String? {
        var string = string
        var startsWithZero = true
        while (startsWithZero) {
            if (string.startsWith("0") && string.length >= 2 && !string.substring(1, 2)
                    .equals(".", ignoreCase = true)
            ) {
                string = string.substring(1)
            } else {
                startsWithZero = false
            }
        }
        return string
    }
```

 在EditText離開focus後 使用上方function把多餘0去除

#### 關於ScrollView

Scrollview 滑動時關閉鍵盤

```kotlin
sc_container.setOnTouchListener { v, event ->
            if (event != null && event.action == MotionEvent.ACTION_MOVE) {
                val imm :InputMethodManager= 
              			context!!.getSystemService(Context.INPUT_METHOD_SERVICE)
              										as InputMethodManager
                val isKeyboardUp:Boolean = imm.isAcceptingText

                if (isKeyboardUp) {
                    imm.hideSoftInputFromWindow(v!!.windowToken, 0)
                }
            }
            false
        }
```





#is_material