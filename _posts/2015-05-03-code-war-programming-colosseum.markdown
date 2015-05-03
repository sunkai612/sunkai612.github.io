---
layout: post
title:  "Code War - 程式競技場"
date:   2015-05-03 20:00:00
categories: jekyll update
tags: featured
image: /assets/article_images/2015-05-03-code-war-programming-colosseum/forest_rails_road.jpg
---

最近發現了一個很有趣的網站：[Code War][code-war]

在這裡面可以挑戰許多各式各樣的程式題，讓你從一個個的挑戰中鍛鍊自己（[Code Kata][code-kata]）。並且在挑戰結束後，還可以觀看其他人的解法，從中學習大家不同的思考與解題方式，來達成 [Code War][code-war] 預期達成的目的：

>Achieve mastery through challenge -- Improve your skills by training with others on real code challenges

#話不多說，開始挑戰
<br>
用 GitHub 帳號登入後，點選左側第二個圖示（Kata），篩選了語言為 Ruby 後，稍微觀察了難度選擇，發現似乎是數字越高的難度越低。因此毫不猶豫地選擇了最低階的 8 kyu，接著隨便點了一題開始測驗！

#題目
<br>
###要求：
寫一個會回報 x 倍數的 function，他會吃兩個變數、執行後會回傳陣列的 function，變數分別是陣列長度（n）和乘數（x）

###進一步假設：
兩個變數一定是正整數且大於零

###範例：
{% highlight ruby %}
count_by(1,10) #should return [1,2,3,4,5,6,7,8,9,10]
count_by(2,5)  #should return [2,4,6,8,10]
{% endhighlight %}


#解法
<br>
###硬幹
{% highlight ruby %}
def count_by(x, n)
  ary = []                      
  (1..n).each {|t| ary << x * t } # 這邊也可以用 ary.push
  ary                             # 預設會 return 方法最後一行運算結果，因此此處 return 可以省略
end
{% endhighlight %}
很顯然最硬來的方法，就是把一個個數產出來，依序塞進陣列中，最後再返回陣列。
<br><br>
###優雅一點
{% highlight ruby %}
def count_by(x, n)
  (1..n).map {|t| x * t }
end
{% endhighlight %}
這邊運用了 Enumerable 中 map 預設會回傳陣列的特質。
<br><br>
###以 Range 內的方法為主
{% highlight ruby %}
def count_by(x, n)
  (x..n*x).step(x).to_a
end
{% endhighlight %}
Range 中 step 會每隔 x 個就執行後面的 block，沒給 block 會回傳 Enumerable Object。
<br><br>
###以 Numeric 內的方法為主
{% highlight ruby %}
# Numeric 是 Integer 老爸，Fixnum 爺爺
def count_by(x, n)
  x.step(by: x).take(n)
end
{% endhighlight %}
Numeric 中 step 會以原數為開端，用 by 拿到的 x 為間距，累加去執行後面的 block。一樣沒給 block 的話就回傳 Enumerable Object。
<br><br>
###不想用 Range 但想用 map
{% highlight ruby %}
def count_by(x, n)
  1.upto(n).map { |n| x * n }
end
{% endhighlight %}
Integer 中的 upto 會從原數開始，一直到 n，一個個去執行後面的 block。老樣子，沒給 block 的話就回傳 Enumerable Object。
<br><br>
###筆者喜歡的寫法
{% highlight ruby %}
def count_by(x, n)
  Array.new(n) {|i| x*(i+1)}
end
{% endhighlight %}
運用陣列內建類方法，創建長度為 n 的陣列，各元素為依序根據 index 執行 block 的結果。

#結論
<br>
看了這麼多，是否也開始覺得有趣了呢：）？<br><br>
如果有想到其他的解法，歡迎留言跟我說喲！<br><br>
然後也一起加入來玩 [Code War][code-war] 吧XD

<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-62540281-1', 'auto');
  ga('send', 'pageview');

</script>

[code-war]:    http://www.codewars.com/
[code-kata]:   http://en.wikipedia.org/wiki/Kata_(programming)
