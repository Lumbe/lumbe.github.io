---
title: Ruby on Rails 5.0. Тесты с RSpec, Capybara, Shoulda-Matchers, Database Cleaner, Factory Girl and Faker
category: ruby on rails
tags: [ruby on rails, specs, rspec, capybara, shoulda-matchers]
---
Список используемых гемов
-----

- [rspec rails](https://github.com/rspec/rspec-rails){:target="_blank"} - фреймворк для тестирования
- [capybara](https://github.com/jnicklas/capybara){:target="_blank"} - имитация действий реального пользователя
- [factory girl](https://github.com/thoughtbot/factory_girl){:target="_blank"}, [factory_girl_rails](https://github.com/thoughtbot/factory_girl_rails) - библиотека для создания "фабрик"(ruby объектов в качестве тестовых данных)
- [faker](https://github.com/stympy/faker){:target="_blank"} - библиотека для генерации рандомных данных(имена, телефоны, электронная почта)
- [shoulda-matchers](https://github.com/thoughtbot/shoulda-matchers){:target="_blank"} - добавляет однострочные матчеры(matchers) к RSpec. Без них тесты были
бы намного длиннее, сложнее и предрасположены к ошибкам
- [database cleaner](https://github.com/DatabaseCleaner/database_cleaner){:target="_blank"} - набор стратегий для очистки базы данных, что бы гарантировать чистое состояние БД по время тестов

Создание фабрик с Factory Girl и Faker
-----

Каркас генерируется автоматически при генерации модели в rails:
{% highlight ruby %}
# generate model, spec and factory
rails generate model Lead
# generate only spec and factory
rails generate rspec:model Lead
{% endhighlight %}

Определение фабрики:
{% highlight ruby %}
# spec/factories/leads.rb
FactoryGirl.define do
  factory :lead do
    name              { Faker::Name.name }
    phone             { Faker::PhoneNumber.phone_number }
    email             { Faker::Internet.email }
    location          { Faker::Address.city }
    project           { Faker::Space.galaxy }
    square            { Faker::Number.between(17, 333) }
    question          { Faker::Lorem.sentence }
    created_at        { Faker::Date.between(3.days.ago, Date.today) }
    region            { Faker::Address.state }
    source            { Faker::Address.ad_source }
    online_request    { Faker::Boolean.boolean }
    come_in_office    { Faker::Boolean.boolean }
    phone_call        { Faker::Boolean.boolean }
    status            { Lead.statuses.keys.sample }
    user                            # association with :user factory
    assignee                        # association with aliased :user factory
    department                      # association with :department factory
  end
end
{% endhighlight %}

Использование фабрики в тестах:

{% highlight ruby %}
# Returns an User instance that's not saved
user = FactoryGirl.build(:user)

# Returns a saved User instance
user = FactoryGirl.create(:user)

# Returns a hash f attributes that can be used to build an User instance
attrs = FactoryGirl.attributes_for(:user)

# Returns an object with all defined attributes stubbed out
stub = FactoryGirl.build_stubbed(:user)

# Passing a block to any of the methods above will yield the return object
FactoryGirl.create(:user) do |user|
  user.posts.create(attributes_for(:post))
end

# Overriding attributes of a factory
user = FactoryGirl.build(:user, first_name: "Joe")
user.first_name
# => "Joe"

# No matter which build strategy is used to override attributes
user = FactoryGirl.create(:user, first_name: "Joe")
user.first_name
# => "Joe"
{% endhighlight %}

Более подробно в [**Factory Girl шпаргалке**](https://github.com/brennovich/cheat-ruby-sheets/blob/master/factory_girl.md){:target="_blank"}