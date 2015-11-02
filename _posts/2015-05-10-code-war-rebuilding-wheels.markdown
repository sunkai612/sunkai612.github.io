---
layout: post
title:  "Code War - 關於重造輪子的練習"
date:   2015-05-10 20:00:00
categories: jekyll update
tags: featured
image: /assets/article_images/2015-05-03-code-war-programming-colosseum/forest_rails_road.jpg
---

最近幾天依舊無聊的在挑戰 [Code War][code-war]

[Code War][code-war] 是一個程式挑戰網站，在這裡面可以挑戰許多各式各樣的程式題，讓你從一個個的挑戰中鍛鍊自己（[Code Kata][code-kata]）。並且在挑戰結束後，還可以觀看其他人的解法，從中學習大家不同的思考與解題方式，來達成 [Code War][code-war] 預期達成的目的：

>Achieve mastery through challenge -- Improve your skills by training with others on real code challenges

#重造輪子的練習
<br>
在上次我們挑戰了[「產出 n 個，x 的倍數」][count-by]、[自幹個枚舉][enum-all]之後，有多做了好些題目，發現有一類題目十分特別...
#題組
<br>
###要求一：
做一個算餘數的 function，但是不用內建的 % operator。

###範例一：
{% highlight ruby %}
describe "modulo" do
  Test.assert_equals(modulo(9, 3), 9 % 3, "get correct modulo")
  Test.assert_equals(modulo(7.5, 2), 7.5 % 2, "get correct modulo")
  Test.assert_equals(modulo(2.5, 1.3), 2.5 % 1.3, "get correct modulo")
  Test.assert_equals(modulo(7, 2.5)}, 7 % 2.5, "get correct modulo")
end
{% endhighlight %}

###要求二：
寫一個 function，效果等價於弱版的三元判斷式，第一個參數是布林判斷值，第二個參數是布林為真時回傳的物件，第三個參數是布林為否時回傳的物件。

###範例二：
{% highlight ruby %}
describe "_if(bool, if_t_obj, if_f_obj)" do
  Test.assert_equals(_if(true, true, false), true, "successfully get true object")
  Test.assert_equals(_if(false, true, false), false, "successfully get false object")
  Test.assert_equals(_if(3, false, nil), false, "successfully get true object")
  Test.assert_equals(_if(nil, nil, false), false, "successfully get false object")
end
{% endhighlight %}

###要求三：
延續要求二，改寫這個 function，讓他效果近似於三元判斷式，第一個參數是布林判斷值，第二個參數是布林為真時執行的 callback，第三個參數是布林為否時執行的 callback。

## 待續

<!-- ###範例三：
{% highlight ruby %}
describe "_if(bool, if_t_proc, if_f_proc)" do
  Test.assert_equals(_if(true, true, false), true, "successfully get true object")
  Test.assert_equals(_if(false, true, false), false, "successfully get false object")
  Test.assert_equals(_if(3, false, nil), false, "successfully get true object")
  Test.assert_equals(_if(nil, nil, false), false, "successfully get false object")
end
{% endhighlight %}


#解法
<br>
###老樣子，硬幹起手勢
{% highlight ruby %}
def all? array, &block
  array.each {|e| return false unless block.call(e)}
  true
end
{% endhighlight %}
迭代這個陣列，如果發現執行結果為 false 的話，就中斷返回 false。迭代平安結束的話丟 true 回去。
有趣的是在觀看別人解法時，看到一個有趣的寫法：
{% highlight ruby %}
def all? array, &block
  true.tap do
    array.each {|e| return false unless block.call(e)}
  end
end
{% endhighlight %}
<br>
###除了迭代外，還可用 reduce
{% highlight ruby %}
def all? array, &block
  array.reduce(true){|result, e| result && yield(e)}
end
{% endhighlight %}
一樣一個個執行完，並且將結果和之前的結果比較。但這個缺點是如果途中就發現不合了，還是會繼續執行，是一個速度比較慢的寫法。
<br><br>
###條條大路通羅馬
{% highlight ruby %}
# 筆者在觀看大家解法時，發現了許多不同的方法，羅列如下：

# 比相同
def all? array, &block
   array.select{|i| yield(i)} == array
end

# 比長度
def all? array, &block
  array.count(&block) == array.length  
end

# 查內容
def all? array, &block
  !array.map(&block).include?(false)
end

# 假設 All Pass
def all? array, &block
  array.reject(&block).empty?
end

{% endhighlight %}
不過以上方法都有和 reduce 一樣的缺點，得整個陣列執行完才能得到結果，速度較慢。
<br><br>
###運用 Enumerable 內建 all?
{% highlight ruby %}
def all? array, &block
  array.empty? || (array.all? &block)
end
{% endhighlight %}
筆者的寫法：先檢查是否為空，再執行檢查。
但殊不知看其他人解法時發現：
{% highlight ruby %}
def all? array, &block
  array.all? &block
end
{% endhighlight %}
原來 Enumerable#all? 預設就會回傳 true 了！

# 題外話
<br>
###all? 的相反 any?
<br>
相較於 all? 會檢查是否全部元素都符合條件，any? 則會檢查是否有至少一個元素符合條件。
{% highlight ruby %}
[1,3,5,7,8].any? {|e| e.even?}
#=> true
{% endhighlight %}
<br>
###JavaScript 裡面相應的方法
<br>
在 JavaScript 裡面，all? 跟 any? 分別對應到 every 跟 some，不過寫法略有不同：
{% highlight js %}
isBiggerThanTwo = function(element, index, array){
  return element > 2;
}

[3,4,5,6,7].every(isBiggerThanTwo)
// => true
[2,3,4,5,6].every(isBiggerThanTwo)
// => false
[3,4,5,6,7].some(isBiggerThanTwo)
// => false
[2,3,4,5,6].some(isBiggerThanTwo)
// => true
{% endhighlight %}
也可以運用另一個寫法延遲要判斷的大小：
{% highlight js %}
isBiggerThanX = function(x){
  return this > x;
}

[3,4,5,6,7].every(isBiggerThanX, 2)
// => true
[2,3,4,5,6].every(isBiggerThanX, 2)
// => false
[3,4,5,6,7].some(isBiggerThanX, 2)
// => false
[2,3,4,5,6].some(isBiggerThanX, 2)
// => true
{% endhighlight %}
#結論
<br>
如果有想到其他的解法，歡迎留言跟我說喲！<br><br>
然後也一起加入來玩 [Code War][code-war] 吧XD
 -->


[code-war]:    http://www.codewars.com/
[code-kata]:   http://en.wikipedia.org/wiki/Kata_(programming)
[count-by]:    http://sunkai612.github.io/jekyll/update/2015/05/03/code-war-programming-colosseum.html
[enum-all]:    http://localhost:4000/jekyll/update/2015/05/06/code-war-enumerable-all.html
