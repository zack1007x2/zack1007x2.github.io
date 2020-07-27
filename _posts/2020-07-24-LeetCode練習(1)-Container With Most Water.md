---
layout: post
title: "LeetCode練習(1)-Container With Most Water"
date: 2020-07-24
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: https://lh3.googleusercontent.com/HAJ6n1YkKq8zy2TokpKN1fU0sv30tsdistq0wTdvlC-KE-aZw5sbSa6FOzGaCUMWlb8Gy9oJIC6_4_rxIyU0MyV-4VwycJea2PmSHz0Y_sgdWjYSjB7_wKWe3EQYWTGW8lhGzHLIhQ=s225-p-k
category: programming
tags: [LeetCode]
---

[](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

題目大意：給定一串數列 從中找到兩個數字作為高度兩者之間的間隔當作寬度算出可以裝的最大水量



想法：

每個立柱都和自己後方的立柱和算彼此之間可形成的容積

然後只記下最大容積

```kotlin
    fun maxArea(height: IntArray): Int {
        var max =0
        for(i in height.indices){
            for(j in i+1 until height.size){
                val size = if(height[i]>height[j])height[j]*(j-i)else height[i]*(j-i)
                if(size>max)
                    max=size
            }
        }
        return max
    }
```

