---
layout: post
title: Refactoring: Sebuah Pengenalan
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

protected function itemTotal($item)
{
    return $item->price * $item->quantity;
}

protected function calcSalesTax($amount)
{
    return $amount * 0.07;
}
Kemudian method calcTotal() kita sederhanakan menjadi berikut:

public function calcTotal()
{
    $total = 0;
    foreach($this->items as $item) {
        $total += $this->itemTotal($item);
    }
    $total += $this->calcSalesTax($total);
    return $total;
}
Perhatikan, method itemTotal() melakukan perhitungan dengan variable yang ada di object SalesItem.
Sudah selayaknya mothod itemTotal() kita pindah ke class SalesItem dengan nama method ‘total’ saja.

public function total()
{
    return $this->price * $this->quantity;
}
Kode dalam method calcTotal menjadi lebih sederhana, sebagai berikut:

public function calcTotal()
{
    $total = 0;
    foreach($this->items as $item) {
        $total += $item->total();
    }
    $total += $this->calcSalesTax($total);
    return $total;
}
Berikut adalah hasil akhir dari kedua class tersebut:

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