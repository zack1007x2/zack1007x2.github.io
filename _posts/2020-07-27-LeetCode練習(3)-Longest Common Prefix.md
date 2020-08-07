---
layout: post
title: "LeetCode練習(3)-Longest Common Prefix"
date: 2020-07-27
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: /assets/images/thumbs/leetcode.png
category: programming
tags: [leetcode]
---



題目大意：尋找最長共同前綴字



```reStructuredText
Example 1:
Input: ["flower","flow","flight"]
Output: "fl"
```

```reStructuredText
Example 2:
Input: ["dog","racecar","car"]
Output: ""
```



想法：假設input=[ F G H ]

剔除掉null input情況

F和G找到共同最長prefix P後 再讓P和H比對並不影響結果

因此我們先讓Ｆ和Ｇ兩邊都substring到相同大小來比對

如果F長度太長或比對後兩者相異就把F長度-1再進行比對

直到找出F和G共同prefix P再用P和H去找到新的P

```kotlin
   fun longestCommonPrefix(strs: Array<String>): String {
        if(strs.isEmpty()){
            return ""
        }
        var ret=strs[0]
        var i=1
        while(i < strs.size){
            while(ret.length-1>=0){
                if(strs[i].length<ret.length || ret!=strs[i].substring(0, ret.length))
                    ret = ret.substring(0,ret.length-1)
                else
                    break
            }
            i++
        }
        return ret
    }
```

心得：另有使用indexOf方式概念其實相同,  如下

```java
public String longestCommonPrefix(String[] strs) {
    if(strs == null || strs.length == 0)    return "";
    String pre = strs[0];
    int i = 1;
    while(i < strs.length){
        while(strs[i].indexOf(pre) != 0)
            pre = pre.substring(0,pre.length()-1);
        i++;
    }
    return pre;
}
```

用indexOf()==0來尋找相符的第0位相等的



TODO：

類似題目找共同字串可能會較有趣 未來遇到再補上