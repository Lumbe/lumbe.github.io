---
title: Ruby шпаргалка - наиболее часто используемые функции.
category: ruby
tags: [ruby, cheatsheet]
---
**В Ruby все объект**
-----
Включая числа, строки и даже nil — они все наследуются от класса Object.
Можно у чего угодно вызывать методы вроде nil?, class, methods и respond_to?:
{% highlight ruby %}
'Hello world'.class # String
nil.class # NilClass
String.class # Class
String.ancestors # [String, Comparable, Object, Kernel, BasicObject]; массив
                 # предков класса
nil.nil? # true
Object.new.methods # вернет методы объекта класса Object; тут же видно как
                   # создается новый объект - тоже вызовам метода
nil.respond_to?('nil?') # true
{% endhighlight %}
По поводу последнего: это важно для [утиной типизации](https://ru.wikipedia.org/wiki/%D0%A3%D1%82%D0%B8%D0%BD%D0%B0%D1%8F_%D1%82%D0%B8%D0%BF%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F).

**Синтаксис и пудра**
-----
Ruby сильно приправлен «синтаксическим сахаром», благодаря которому зачастую не
нужно городить кашу из различных скобок, точек с запятыми и прочего. А под ним
— прост и логичен, и сохраняет парадигму «все — объект».

{% highlight ruby %}
a == b # то же, что и
a.==(b) # то есть .==() - это метод
{% endhighlight %}

В вызовах методов, в if можно не ставить скобки, если нет неоднозначности

{% highlight ruby %}
nil.respond_to?('nil?') # true
nil.respond_to? 'nil?' # true
# все однозначно:
if nil.respond_to? 'nil?'
  puts 'ok'
end
# тоже
if 10.between? 1, 5
  puts 'ok'
end
# а вот так получится не тот приоритет вызовов, поэтому нужны скобки
if 10.between?(1, 50) && 20.between?(1, 50)
  puts 'ok'
end
{% endhighlight %}

А еще — есть символы. По сути, символы — это неизменяемые строки.
Например, они часто используются как ключи в хэшах.

{% highlight ruby %}
a = :nil? # символ
b = 'nil?' # строка
nil.respond_to? a # true
nil.respond_to? b # true
# поиграемся
a == b # false
a.to_s == b && a == b.to_sym # true; символ и строка преобразуются друг в друга
{% endhighlight %}

С ними связано еще немного пудры:

{% highlight ruby %}
a = {:key1 => 'value1', :key2 => 'value2'} # создаем хэш (ассоциативный массив)
a = {key1: 'value1', key2: 'value2'} # если ключи - символы, то можно так (Ruby >= 1.9.3)
{% endhighlight %}

Раз уж тема зашла:

{% highlight ruby %}
a = ['value1', 'value2'] # это массив
s = 'String'
s = "Double-quoted #{s}" # "Double-quoted String" - это, думаю, ясно
{% endhighlight %}

И добьем тему припудривания немного уродливым, но зато удобным способом записи:

{% highlight ruby %}
%w[value1 value2] # ["value1", "value2"] - тот же массив, что и выше
%i[value1 value2] # [:value1, :value2] - массив символов, (Ruby >= 2.0)
s = %q(String)
s = %Q(Double-quoted #{s})
%x('ls') # команда консоли
`ls` # то же самое
%r(.*) == /.*/ # true; два способа создать регулярное выражение
{% endhighlight %}

Кстати, в Ruby есть многим до боли знакомая точка с запятой — пригодиться она
может чтобы записать несколько выражений в одну строку

{% highlight ruby %}
a = 10; puts a # выведет в консоль 10
if nil.respond_to? 'nil?'; puts 'ok'; end # чтобы было понятно - так тоже можно
{% endhighlight %}

Но однострочные if лучше писать так

{% highlight ruby %}
puts 'ok' if nil.respond_to? 'nil?'
{% endhighlight %}