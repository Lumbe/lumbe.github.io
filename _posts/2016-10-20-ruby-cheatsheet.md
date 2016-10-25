---
title: Ruby шпаргалка - наиболее часто используемые функции.
category: ruby
tags: [ruby, cheatsheet]
---
**В Ruby все объект**
-----
Включая числа, строки и даже nil — они все наследуются от класса Object.
Можно у чего угодно вызывать методы вроде **nil?**, **class**, **methods** и **respond_to?**:
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
— прост и логичен, и сохраняет парадигму «все — объект»:

{% highlight ruby %}
a == b # то же, что и
a.==(b) # то есть .==() - это метод
{% endhighlight %}

В вызовах методов, в **if** можно не ставить скобки, если нет неоднозначности:

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
Например, они часто используются как ключи в хэшах:

{% highlight ruby %}
a = :nil? # символ
b = 'nil?' # строкаs
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

Вводные статьи и доки
-----
Стоит почитать [Ruby за двадцать минут](http://www.ruby-lang.org/ru/documentation/quickstart/), ну а главный друг и помощник [ruby-doc.org](http://ruby-doc.org/).
Лучше сразу посмотреть методы базовых классов (во всех — какие бывают each_ и to_).

- [String](http://ruby-doc.org/core-2.0.0/String.html) (тут — первым делом match, sub)
- [Array](http://ruby-doc.org/core-2.0.0/Array.html) (map, join, include?)
- [Hash](http://ruby-doc.org/core-2.0.0/Hash.html) (has_key?, has_value?, merge)

Классы, модули и метапрограммирование
-----
Классы в Ruby вполне очевидны, но в возможностях работы с ними кроется вся сила и прелесть Ruby.

{% highlight ruby %}
Class Foo
  def bar
    10 # любой метод возвращает значение - результат выполнения последнего выражения
  end
  def baz(a)
    bar + 20
  end
end
puts Foo.new.baz(10) # 30
{% endhighlight %}

Module — к нему можно относиться и как к примеси, и как к пространству имен. Внезапно? По сути — это набор классов, методов и констант, и пользоваться им можно на свое усмотрение.

{% highlight ruby %}
module B # как пространство имен
  class Foo
    # конструктор
    def initialize(k) @k = k end # запишем методы в одну строку
    def bar; @k + 20 end
  end
end
puts B.Foo.new(3).bar # 23
puts B::Foo.new(3).bar # 23; то же самое, но читается лучше

module C # как примесь
  def bar; @k + 25 end
end
class Foo
  include C;
  def initialize(k) @k = k end
end
puts Foo.new(3).bar # 28
{% endhighlight %}

В Ruby классы мутабельны, и их можно патчить после их создания. И тут будьте осторожны: здесь начинаются те самые возможности, при помощи которых можно «выстрелить себе в ногу» — так что, делая что-то подобное нужно всегда отдавать отчет: зачем, что получится, кто виноват и что делать и если что — виноваты сами.

{% highlight ruby %}
class Foo
  def bar; 20 end
end
class Foo # патчим
  def baz; bar + 2 end
end
puts Foo.new.baz # 22
# или лучше - так: сразу понятно что это патч
Foo.class_eval do
  def bazz; bar + 3 end
end
puts Foo.new.bazz # 23
# но если нет на то веской причины - используйте примеси или наследование
class Boo < Foo
  def boo; bar + 2 end
end
puts Boo.new.boo # 22
# или же - можно пропатчить только объект, а не сам класс
a = Foo.new
a.instance_eval do # патчим объект
  def booo; bar + 3 end
end
puts a.booo # 23
puts Foo.new.booo rescue puts 'error' # error; кстати, так перехватываются ошибки
puts a.respond_to? :booo # true
puts Foo.new.respond_to? :booo # false
# или - патчим объект в более легком синтаксисе
def a.booboo
  bar + 4
end
puts a.booboo # 24
{% endhighlight %}

Играться с этим можно вдоволь, главное помните — берегите ноги.

{% highlight ruby %}
class Foo
  def self.add_bar # добавим статический метод, который патчит класс
	self.class_eval do
	  def bar; 'bar' end
	end
  end
end
puts Foo.new.respond_to? :bar # false
class Boo < Foo # отнаследуемся
  add_bar # пропатчим
end
puts Boo.new.bar # bar

# а теперь все то же самое, но пропатчим уже существующий класс
Foo.instance_eval do
  def add_baz
    self.class_eval do
      def baz; 'baz' end
    end	
  end
end
class Baz < Foo
  add_baz
end
puts Baz.new.baz # baz
{% endhighlight %}

Как раз такой подход используется на практике — по сути, получается что-то вроде примеси, в которую можно передавать параметры. Это кажется магией, если не знать, как это делается. Патчить можно и базовые классы, особенно любимы Array и String — но всегда стоит подумать трижды, прежде чем начинать их мучить: одно дело методы вроде .blank? (его добавляет Rails: что-то вроде def blank?; nil? || empty? end), другое — когда код метода специфичен для проекта, тогда логично предположить, что он относится к каким-то классам внутри проекта.

По такому принципу работает, например accessor. Что мы сделаем, чтобы добавить публичный параметр в Ruby-класс?

{% highlight ruby %}
class Foo
  def bar # геттер
    @bar # возвращаем параметр
  end
  def bar=(val) # сеттер
    @bar = val # присваиваем значение параметру
  end
end
{% endhighlight %}

Представляете так писать для десятка-другого параметров? В Ruby много кода по накатанному — смертный грех: [DRY](http://ru.wikipedia.org/wiki/Don%E2%80%99t_repeat_yourself). Так что, можно сделать короче.

{% highlight ruby %}
class Foo
  attr_accessor :bar, :baz # добавим сразу парочку атрибутов
  attr_reader :boo # только геттер
  attr_writer :booo # только сеттер
end
{% endhighlight %}

Готовы дальше? Тогда:

- [Вникаем в метаклассы Ruby](http://habrahabr.ru/post/143990/)
- [Metaprogramming patterns — про monkey patching](http://habrahabr.ru/post/50819/), [Reuse в малом — bang!](http://habrahabr.ru/post/50169/), [eval](http://habrahabr.ru/post/49951/)
- [Вникаем в include и extend](http://habrahabr.ru/post/143483/)