---
layout: post
title: "LeetCode練習(4)-3Sum"
date: 2020-07-27
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: /assets/images/thumbs/leetcode.png
tags: [leetcode]
---



題目大意：給一串數列，窮舉出三個數合為0的組合

```reStructuredText
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```



想法：

直覺想到的解法但會有執行時間太長的問題

```kotlin
fun threeSum(nums: IntArray): List<List<Int>> {
        var cur: MutableList<Int>  = mutableListOf()
        var ret: MutableList<List<Int>> = mutableListOf()

        for(i in nums.indices){
            cur.clear()
            cur.add(0,nums[i])
            for(j in i+1 until nums.size){
                cur.add(1,nums[j])
                for(k in j+1 until nums.size){
                    cur.add(2,nums[k])
                    if(sum_zero(cur)){
                        if(ret.isEmpty() || !containsSet(ret, cur))
                            ret.add(cur.toList())
                        cur.removeAt(2)
                        continue
                    }else{
                        cur.removeAt(2)
                    }
                }
                cur.removeAt(1)
            }
        }

        return ret
    }
    private fun sum_zero(arr:MutableList<Int>):Boolean{
        return arr[0]+arr[1]+arr[2]==0
    }

    private fun containsSet(ret: MutableList<List<Int>>, cur: MutableList<Int> ):Boolean{
        for (r in ret){
            if(r.toSet().equals(cur.toSet()))
                return true
        }
        return false
    }
```

花點時間想不到適當解法

solution是利用和為0的情況 解必有正有負或都是0

因此先對input排序

接著取 第i位做第一筆資料 第i+1位當low做第二筆 末位當high做第三筆

| i | low | .. | high | 

總和為0時 low++ high-- 直到low>high ,i才進位

| i | ... | i | low | high | 

Solution:

```kotlin
    fun threeSum(nums: IntArray): List<List<Int>> {

        var ret = HashSet<List<Int>>()
        if(nums.size <= 2) {
            return ret.toList()
        }
        nums.sort()
        for(i in 0 until nums.size - 2) {
            var low = i + 1
            var high = nums.size - 1
            while (low < high) {
                var sum = nums[i] + nums[low] + nums[high]
                when {
                    sum == 0 -> {
                        ret.add(listOf(nums[i], nums[low], nums[high]))
                        low++
                        high--
                    }
                    sum < 0 -> low++
                    else -> high--
                }
            }
        }

        return ret.toList()
    }
```

心得：自認不算直覺但合理的解法 也好懂