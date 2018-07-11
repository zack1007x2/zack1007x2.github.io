---
layout: post
title: 檢測memory leak 使用 valgrind
date: 2018-07-11
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: https://lh3.googleusercontent.com/fesu-l5g6yAEiWxbsD-4TiXo6rYJ68r7u7MsolVwnnfgnLDC7h0uAc8lF74ZSbZpvTd_lY3A4iYAnAy7VzxAp_fKxQrwiyALqUOHmlC8miKg9lXDlGDvPQuZv9QWTAyUtttyZLHGDw=w2400
category: programming
tags: memory_leak candcpp
---


# 關於valgrind

參考： [ https://zh.wikipedia.org/wiki/Valgrind ]( https://zh.wikipedia.org/wiki/Valgrind )



------
# 五種memory leak說明

- "definitely lost" 
means your program is leaking memory -- fix those leaks!


- "indirectly lost" 
means your program is leaking memory in a pointer-based structure. (E.g. if the root node of a binary tree is "definitely lost", all the children will be "indirectly lost".) If you fix the "definitely lost" leaks, the "indirectly lost" leaks should go away.


- "possibly lost" 
means your program is leaking memory, unless you're doing unusual things with pointers that could cause them to point into the middle of an allocated block; see the user manual for some possible causes. Use --show-possibly-lost=no if you don't want to see these reports.


- "still reachable" 
means your program is probably ok -- it didn't free some memory it could have. This is quite common and often reasonable. Don't use --show-reachable=yes if you don't want to see these reports.


- "suppressed" 
means that a leak error has been suppressed. There are some suppressions in the default suppression files. You can ignore suppressed errors.




------
# valgrind安裝

嘗試過現在已經無法在新版本的OSX上跑了

所幸這陣子學到如何使用docker

立馬用先前寫好的dockerfile啟動一個ubuntu環境

``` python
sudo apt-get install valgrind
```

以最近執行的專案為例

先將專案compile

``` python
gcc -g -o Output main.c graph.c Node.c tree.c forest.c graph_generator.c Array.c ./rngs/rngs.c -lm
```


之後對輸出的Output檔做檢測


```c
valgrind
--leak-check=full           //基本項目
--show-leak-kinds=all       //基本項目
--track-origins=yes         //詳細檢測 會追到問題源頭 跑很慢  建議減少執行完所需loop次數
--verbose                   //邊執行程式邊檢測
--log-file=valgrind-out.txt //檢測結果輸出
./Output                    //要執行的程式
```


# 檢測結果範例：[完整報告](https://drive.google.com/open?id=1EOt4l_efHkWQsyVoTQ1Z36jWNZO2T24f)

```c
==144== LEAK SUMMARY:
==144==    definitely lost: 227,088,368 bytes in 632,449 blocks
==144==    indirectly lost: 58,794,688 bytes in 486,723 blocks
==144==      possibly lost: 11,216 bytes in 32 blocks
==144==    still reachable: 0 bytes in 0 blocks
==144==         suppressed: 0 bytes in 0 blocks
```

在SUMMARY這段可以看到基本上錯誤不少

- definitely lost : 是明顯需要改正的地方 memory leak狀況也最嚴重

通常是該釋放的忘了做  也是最好修正的



- still reachable : 是有幾個struct在loop中重複使用從未free過

雖然不影響執行 但長時間下來可能會耗盡系統資源




------
# 未修正點


- main.c:141~189

```c
    nodesPos = malloc( 2 * n * sizeof(float) );
    transAbi = malloc( n * sizeof(int) );
    weight = malloc( n * n * sizeof(int) );
    Nodes = malloc( n * sizeof(Node) );

    heads = malloc( typenumber * sizeof(Array) );
    tails = malloc( typenumber * sizeof(Array) );
    v3Tails = Array_Create(n);
    v3Heads = Array_Create(n);
    forestMember = malloc( n * sizeof(Array) );
    mergeEdge = malloc( n * sizeof(Array) );
    mergeNodes = Array_Create(n) ;
    mergeForest = Array_Create(n) ;

    partTails = malloc( typenumber * sizeof(Array) );
    partHeads = malloc( typenumber * sizeof(Array) );

    Rjset = malloc( typenumber * sizeof(Array) );
    for( i = 0 ; i < typenumber ; i++ )
    {
        Rjset[i] = Array_Create(n);
    }
    Rset = Array_Create(n);
    for( i = 0 ; i < n ; i++ )
    {
        Nodes[i] = NULL;
        forestMember[i] = Array_Create(n) ;
        mergeEdge[i] = Array_Create(n) ;
    }

    CA = malloc( n * n * sizeof(int) );
    M = malloc( n * sizeof(int) );

//    lastR = malloc(sizeof(double)*n);
//    lastv = malloc(sizeof(int)*n);

    num_of_pass = malloc(sizeof(int)*typenumber);
    total_lifetime_before = 0;
    total_lifetime_after = 0;

    bs = malloc(sizeof(int)*typenumber);

    temp_r = malloc( n * sizeof(double) );
    temp_v2_max = malloc( n * sizeof(double) );

    int **nodesProduce = malloc( typenumber * sizeof(int*) ); // 記錄產生application的sensor
    for(i=0;i<typenumber;i++) nodesProduce[i] = malloc( n * sizeof(int) );
    int **nodesAssist = malloc( typenumber * sizeof(int*) );  // 記錄幫助application傳輸的sensor
    for(i=0;i<typenumber;i++) nodesAssist[i] = malloc( n * sizeof(int) );`

```

main()進loop前初始化的地方

之後會重複使用

猜測可能到執行結束都未釋放導致

還不至於crash

日後看有沒有更好的改法

# 查證後無需修正

- graph.c:218

```c
Node_Create( neighborNum, &Nodes[i] , i);
```

就算沒使用也預先生成leaf點造成的浪費

# 不明原因

```c
==747== 3920 errors in context 8 of 35:
==747== Conditional jump or move depends on uninitialised value(s)
==747==    at 0x10D06E: check_v1_v2_v3 (tree.c:330)
==747==    by 0x109E14: main (main.c:437)
```
- tree.c:330

```c
if(phi_max[i]<temp_phi){
    ....
```

與未初始化的值進行比較

檢查code有確實做好init value 不太懂為何出錯
