---
title:  教程：自制网格系统（Grid System）
date:   2014-02-03 18:16:38  +0800
---

网格系统（Grid System）运用于很多网站，并且很多框架，例如 Foundation，Bootstrap 都有自己的自适应网格系统。那么，如何自制一个网格系统？

## border-box VS content-box

所有元素的默认是 content-box，即宽度不会包含 border 的宽度，在这种情况下，如果你将一个元素的宽度设定为25%，border 宽度设定为 1px，如果总宽度为 1024px，那么这个元素的实际宽度为 1024px * 0.25 + 1px * 2，比原定宽度 25% 多出了 2 个 border 宽度。如果将元素的 box-sizing 设定为 border-box ，那么这个元素的实际宽度就是总宽度的 25%，无论 border 的宽度是多少。这也意味着同样的 4 个元素，可以恰巧铺满整个宽度的位置。

所以为了省事，一般都会将所有元素的 box-sizing 重置为 border-box：

{% highlight css %}
*, *:before, *:after {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}
{% endhighlight %}

## 包装网格系统

一个网页可能会用到多次的网格系统，为了避免前后的网格系统不会发生冲突，所以必须包装每个网格系统，确保每个网格系统都是独立互不干涉的整体。

{% highlight css %}
.grid {
  width: 100%;
  margin: 0 auto;
  overflow: hidden;
}

.grid:after {
  content: "";
  display: table;
  clear: both;
}
{% endhighlight %}

## 编织网格

做好每个网格的属性：

{% highlight css %}
[class*='col-'] {
  float: left;
  padding-left: 10px;
  padding-right: 10px;
}

[class*='col-']:first-of-type {
  padding-left: 0;
}

[class*='col-']:last-of-type {
  padding-right: 0;
}
{% endhighlight %}

用 Ruby 计算下每个网格的宽度比例：

{% highlight ruby %}
class Column
  def initialize(part, total)
    @part, @total = part, total
  end

  def css_class
    ".col-#{@part}-#{@total}"
  end

  def css_width
    percentage = (@part.to_f/@total * 100).round(2)

    if (@part.to_f/@total * 100).to_i == percentage
      percentage = percentage.to_i
    end

    "#{percentage}%"
  end
end

css_hash = {}

12.times do |total|
  total += 1

  11.times do |part|
    part += 1

    if part < total || part == 1
      column = Column.new(part, total)

      if !css_hash[column.css_width].is_a?Array
        css_hash[column.css_width] = []
      end

      css_hash[column.css_width] << column.css_class
    end
  end
end

css = ""

css_hash.each { |k, v| css += "#{v.join(", ")} { width: #{k}; }\n" }

print css
{% endhighlight %}

得到的结果：

{% highlight css %}
.col-1-1 { width: 100%; }
.col-1-2, .col-2-4, .col-3-6, .col-4-8, .col-5-10, .col-6-12 { width: 50%; }
.col-1-3, .col-2-6, .col-3-9, .col-4-12 { width: 33.33%; }
.col-2-3, .col-4-6, .col-6-9, .col-8-12 { width: 66.67%; }
.col-1-4, .col-2-8, .col-3-12 { width: 25%; }
.col-3-4, .col-6-8, .col-9-12 { width: 75%; }
.col-1-5, .col-2-10 { width: 20%; }
.col-2-5, .col-4-10 { width: 40%; }
.col-3-5, .col-6-10 { width: 60%; }
.col-4-5, .col-8-10 { width: 80%; }
.col-1-6, .col-2-12 { width: 16.67%; }
.col-5-6, .col-10-12 { width: 83.33%; }
.col-1-7 { width: 14.29%; }
.col-2-7 { width: 28.57%; }
.col-3-7 { width: 42.86%; }
.col-4-7 { width: 57.14%; }
.col-5-7 { width: 71.43%; }
.col-6-7 { width: 85.71%; }
.col-1-8 { width: 12.5%; }
.col-3-8 { width: 37.5%; }
.col-5-8 { width: 62.5%; }
.col-7-8 { width: 87.5%; }
.col-1-9 { width: 11.11%; }
.col-2-9 { width: 22.22%; }
.col-4-9 { width: 44.44%; }
.col-5-9 { width: 55.56%; }
.col-7-9 { width: 77.78%; }
.col-8-9 { width: 88.89%; }
.col-1-10 { width: 10%; }
.col-3-10 { width: 30%; }
.col-7-10 { width: 70%; }
.col-9-10 { width: 90%; }
.col-1-11 { width: 9.09%; }
.col-2-11 { width: 18.18%; }
.col-3-11 { width: 27.27%; }
.col-4-11 { width: 36.36%; }
.col-5-11 { width: 45.45%; }
.col-6-11 { width: 54.55%; }
.col-7-11 { width: 63.64%; }
.col-8-11 { width: 72.73%; }
.col-9-11 { width: 81.82%; }
.col-10-11 { width: 90.91%; }
.col-1-12 { width: 8.33%; }
.col-5-12 { width: 41.67%; }
.col-7-12 { width: 58.33%; }
.col-11-12 { width: 91.67%; }
{% endhighlight %}

这样，最基本的网格系统已经制作完成了。

一些 html 例子：

{% highlight html %}
<div class="grid">
  <div class="col-1-3">
    <h1>col-1-3</h1>
  </div>
  <div class="col-1-3">
    <h1>col-1-3</h1>
  </div>
  <div class="col-1-3">
    <h1>col-1-3</h1>
  </div>
</div>

<div class="grid">
  <div class="col-4-6">
    <h1>col-4-6</h1>
  </div>
  <div class="col-2-6">
    <h1>col-2-6</h1>
  </div>
</div>
{% endhighlight %}

## 链接

[查看自制的网格系统 CSS 源文件](http://dushunfan.qiniudn.com/css/grid-system.css)

## 参考

* [ThisIsDallas/Simple-Grid](https://github.com/ThisIsDallas/Simple-Grid)
