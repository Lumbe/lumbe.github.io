---
title: Ruby шпаргалка - наиболее часто используемые функции.
category: ruby
tags: ruby, cheatsheet
---
В Ruby все объект

Включая числа, строки и даже nil — они все наследуются от класса Object. Можно у чего угодно вызывать методы вроде nil?, class, methods и respond_to?:
{% hihglight ruby %}
'Hello world'.class # String
nil.class # NilClass
String.class # Class
String.ancestors # [String, Comparable, Object, Kernel, BasicObject]; массив предков класса
nil.nil? # true
Object.new.methods # вернет методы объекта класса Object; тут же видно как создается новый объект - тоже вызовам метода
nil.respond_to?('nil?') # true
{% endhighlight %}
По поводу последнего: это важно для [утиной типизации](https://ru.wikipedia.org/wiki/%D0%A3%D1%82%D0%B8%D0%BD%D0%B0%D1%8F_%D1%82%D0%B8%D0%BF%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F).