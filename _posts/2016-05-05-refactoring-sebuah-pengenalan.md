---
layout: post
title: Refactoring &#58; Sebuah Pengenalan
comments: true
category: Best Practise
tags: Refactoring
---

Refactoring adalah perbaikan desain dari kode yang sudah ada.
Dalam Refactoring, kita tidak menambah fitur, melainkan hanya memindah,
memecah, menggabung, menghapus atau mengganti nama kode yang sudah ada.
Tujuannya adalah supaya kualitas kode menjadi lebih baik.

Dalam pengenalan ini saya akan menyajikan contoh kasus refactoring.
Pada aplikasi penjualan ada dua object, yaitu Sales dan SalesItem.
Sales menyimpan informasi transaksi penjualan, sedangkan SalesItem menyimpan detil transaksinya.

Kedua object tersebut didefinisikan dalam class sebagai berikut:

{% highlight php %}
// PHP5
class SalesItem {
    public $price = 0;
    public $quantity = 0;
}


class Sales {
    protected $items = array();

    public function addItem($item)
    {
        $this->items[] = $item;
    }

    public function calcTotal()
    {
        $total = 0;
        // add totals for each item
        foreach($this->items as $item) {
            $total += $item->price * $item->quantity;
        }
        // add sales tax
        $total *= 1.07;
        return $total;
    }
}

{% endhighlight %}

Perhatikan kode dalam method calcTotal(), ada dua hal yang perlu di refactoring yaitu total item dan rumus  perhitungan pajak (tax). Kita perlu menambah dua method dalam object Sales sebagai berikut:

{% highlight php %}
protected function itemTotal($item)
{
    return $item->price * $item->quantity;
}

protected function calcSalesTax($amount)
{
    return $amount * 0.07;
}

{% endhighlight %}

Kemudian method calcTotal() kita sederhanakan menjadi berikut:

{% highlight php %}
public function calcTotal()
{
    $total = 0;
    foreach($this->items as $item) {
        $total += $this->itemTotal($item);
    }
    $total += $this->calcSalesTax($total);
    return $total;
}

{% endhighlight %}

Perhatikan, method itemTotal() melakukan perhitungan dengan variable yang ada di object SalesItem saja, tidak ada variable dari object lain.
Sudah selayaknya mothod itemTotal() kita pindah ke class SalesItem dengan nama method ‘total’ saja.  
Kita sekaligus menerapkan <i>[Single Resposibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle)</i>.

{% highlight php %}
public function total()
{
    return $this->price * $this->quantity;
}
{% endhighlight %}

Kode dalam method calcTotal menjadi lebih sederhana, sebagai berikut:

{% highlight php %}
public function calcTotal()
{
    $total = 0;
    foreach($this->items as $item) {
        $total += $item->total();
    }
    $total += $this->calcSalesTax($total);
    return $total;
}
{% endhighlight %}

Okay, berikut adalah hasil akhir dari kedua class tersebut:

{% highlight php %}
class SalesItem {
    public $price = 0;
    public $quantity = 0;

    public function total()
    {
        return $this->price * $this->quantity;
    }
}

class Sales {
    protected $items = array();
    public function addItem($item)
    {
        $this->items[] = $item;
    }

    protected function calcSalesTax($amount)
    {
        return $amount * 0.07;
    }

    public function calcTotal()
    {
        $total = 0;
        foreach($this->items as $item) {
            $total += $item->total();
        }
        $total += $this->calcSalesTax($total);
        return $total;
    }
}
{% endhighlight %}

Smoga contoh sederhana ini bisa difahami bagaimana melukan refactoring sehingga kode kita menjadi lebih berkualitas sehingga mudah difahami dan dipelihara.

Note: saya ambil dari artikel lama saya di [sini](https://web.archive.org/web/20090410083802/http://www.thephpenterprise.com/2009/04/02/refactoring-sebuah-pengenalan/).