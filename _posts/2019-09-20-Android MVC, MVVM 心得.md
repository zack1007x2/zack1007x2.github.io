---
layout: post
title: Android MVVM 心得
date: 2019-09-20
backgrounds:
- https://lh3.googleusercontent.com/vwuOQZ5xS\_\_kQZVuTPaBZxChACmwIEeXrkznajiHJTxYso\_IpI2JD\_1LxsF\_5ZsWWi6Nq1jGexF00qjDuYsE-b45VXWJBQUNa50lhWeJ4E5Dyg\_c0Yb9eo1nSuu8D6nZKrNKPH6y9Q
thumb: http://zack1007x2.github.io/assets/images/thumbs/android.jpg
category: programming
tags: [android, MVC]
---

java object 之間視為傳值
但傳object是傳其位址之值

MVVM



## Model
activity負責view 有關的model
其他model各司其職

## View

xml, 自製的view

## ViewModel

存放給view觀察的變數
這些變數透過呼叫model來更新

<!--{% highlight scss %}
.container {
    background: $background-color;
    padding: 0;
    position: relative;
    margin: 0 auto;
    width: $content-width;
    z-index: 10;

    @include media-query($on-palm) {
        background: rgba($background-color, .95);
        width: 100%;
    }
}
{% endhighlight %}-->



<!--{% highlight js %}
var name = "John";
alert("Hello " + name);
{% endhighlight %}-->




<!--{% highlight html %}
<main id="sb-site">
    <div class="container">
        <div class="wrapper">
            ...
        </div>
        ...
    </div>
</main>
{% endhighlight %}-->

<!--![Sample Image](http://placehold.it/600x480)-->