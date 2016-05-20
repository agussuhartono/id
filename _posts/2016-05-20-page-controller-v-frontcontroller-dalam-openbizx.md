---
layout: post
title: PageController v FrontController dalam OpenbizX
comments: true
category: OpenbizX
tags: Refactoring, OpenbizX, Openbiz, Controller, FrontController, PageController
---

5 Bulan lalu, sy merefactor (<i>rename</i>) Openbiz View menjadi WebPage dalam OpenbizX.
Karena sebenarnya hal itu bukan bagian view dalam MVC, melainkan controller.

Dalam arsitektur MVC, controller ini ada 2 jenis, 
- menggunakan PageController
- menggunakan FrontController

## PageController
Cara kerja PageController ini lebih simpel,
satu page satu controller dan bisa langsung di akses dari file controller tersebut dibuat.
Hanya satu action dalam PageController.

Contoh ya :

{% highlight php %} 
// article.php
class ArticleController extends PageController
{
	public process()
	{
		$model = new ArticleModel();
		$this->view->render(['model' => $model]);
	}
}

$controller = new ArticleController();
$controller->process();

{% endhighlight %}

It's, very simple :D

Kenapa kok tidak begini saja?

{% highlight php %} 
// article.php

$model = new ArticleModel();
$view = new View();
$view->render(['model' => $model]);
	
{% endhighlight %}

Kan jadi lebih simple?
Tidak, itu memang terlihat lebih simple, 
tetapi dampaknya justru tidak simple lagi, 
karena mengekspos variable menjadi GLOBAL.

JIka kita ingin menambah proses dalam controller 
dan membutuhkan variable tingkat controller,
maka variable tersebut akan menjadi global juga. 

## FrontController

Oke, untuk yang menggunakan FrontController, contohnya sudah banyak tersebar.
Pada umumnya framework-framework yang ada saat ini menggukan FrontController.
Kelebihan menggunakan FrontController ini adalah route menjadi lebih fleksibel.

## Controller dalam OpenbizX
Nah yang unik dari OpenbizX ini adalah menggabungkan ke dua hal diatas, PageController dan FrontController sekaligus.
Tetap mempertahankan kesederhanaan PageController tetapi menggunakan fleksibilitas  FrontController.
Tidak itu saja, PageController di OpenbizX saya rencanakan bisa memiliki action tambahan, 
tetapi action ini hanya dipakai untuk sesuatu yang terkait dengan controller.

Sekian dulu ya. Lanjut nanti lagi :D 

Agus Suhartono

