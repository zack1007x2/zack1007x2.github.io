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

# MVVM

-— 

## Model

涉及資料庫, service, web等

只要能修改到data都歸類於此

使用Repository來統一管理

並以singleton方式建立Repository

讓其他class可隨意存取所需的data

通常Repository可依照用途作細分

## View

主要由activity, fragment 來決定view的呈現

## ViewModel

存放給view觀察和給model修改的變數

類似controller的角色

勿將activity或fragment的context傳入viewmodel

會讓viewmodel回收時產生memory leak

通常會讓同一個activity建立的多個fragments

共用一個viewmodel來達到資料共享的目的

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

<!--![Sample Image](http://placehold.it/600x480)--