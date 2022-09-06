---
layout: post
title: "LeetCode練習(1)-Integer to Roman"
date: 2020-07-27
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: /assets/images/thumbs/leetcode.png
category: 書摘
tags: [leetcode]
---



題目大意：將數字轉換為羅馬數字

```reStructuredText
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```



想法：字元轉換需要用到table, 接著透過 /, %逐個取得位數並轉換對應字元



```kotlin
    fun intToRoman(num: Int): String {
            var dev=1000
            val sb = StringBuilder()
            val map = HashMap<Int, String>()
            map.set(1, "I")
            map.set(5, "V")
            map.set(10, "X")
            map.set(50, "L")
            map.set(100, "C")
            map.set(500, "D")
            map.set(1000, "M")
            var cur_num = num
            while(dev>=1){
                val d =cur_num/dev
                when(d){
                    in 1..3 ->{
                        repeat(d){
                            sb.append(map.get(dev))
                        }
                    }
                    4->sb.append(map.get(dev)).append(map.get(dev*5))

                    5->sb.append(map.get(dev*5))

                    in 6..8->{
                        sb.append(map.get(dev*5))
                        repeat(d-5){
                            sb.append(map.get(dev))
                        }
                    }
                    9->{
                        sb.append(map.get(dev)).append(map.get(dev*10))
                    }
                }

                cur_num %= dev
                dev/=10
            }
            return sb.toString()
        }
```

心得：另一種解刻出了完整對應的map,  如下

```java
public static String intToRoman(int num) {
  //1000 2000 3000
    String M[] = {"", "M", "MM", "MMM"};
  //100 200 300 400...900
    String C[] = {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"};
  //10 20 30..90
    String X[] = {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"};
  //1 2 3 4...9
    String I[] = {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"};
    return M[num/1000] + C[(num%1000)/100] + X[(num%100)/10] + I[num%10];
}
```

直接轉為十進位串接，只是寫code懶得自己轉...所以就算了