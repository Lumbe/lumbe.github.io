---
title: Deploy RoR 5 приложения на digitalocean(capistrano).
category: ruby on rails
tags: [ruby on rails, deploy, digitalocean, capistrano]
---
Deploy делаем по гайду на digitalocean [Deploying a Rails App on Ubuntu 14.04 with Capistrano, Nginx, and Pumahttps://www.digitalocean.com/community/tutorials/deploying-a-rails-app-on-ubuntu-14-04-with-capistrano-nginx-and-puma#step-6-—-adding-deployment-configurations-in-the-rails-app](https://www.digitalocean.com/community/tutorials/deploying-a-rails-app-on-ubuntu-14-04-with-capistrano-nginx-and-puma#step-6-—-adding-deployment-configurations-in-the-rails-app)

Гайд не охватывает настройку **secret_key_base** и базы данных. Есть примеры с использованием
environment variables, но мы используем другой метод.

1. Добавляем файлы *secrets.yml* и *database.yml* в .gitignore
{% highlight ruby %}
# /.gitignore в корневой папке проекта
...
config/database.yml
config/secrets.yml
{% endhighlight %}
2. Генерируем secret код
{% highlight ruby %}
$ rake secret
3e8bda57f936e07d15e150fccb7e18cd63f0e22a8e6ac1179780f60d6ee9a4a0fbbe1870580d2f358ef4a8cb77f9b14402a9b151246dca2d1a6dea6c62b68d57
{% endhighlight %}
3. Добавляем его в **secrets.yml** - секция production
{% highlight ruby %}
# config/secrets.yml
...
production:
  secret_key_base: 3e8bda57f936e07d15e150fccb7e18cd63f0e22a8e6ac1179780f60d6ee9a4a0fbbe1870580d2f358ef4a8cb77f9b14402a9b151246dca2d1a6dea6c62b68d57
{% endhighlight %}
4. Добавляем имя БД, пользователя и пароль в production секцию **database.yml**(те что мы создали на digitalocean)
{% highlight ruby %}
# config/database.yml
...
production:
  adapter: postgresql
  encoding: unicode
  database: <database_name>
  pool: 5
  username: <database_user_name>
  password: <database_password>
{% endhighlight %}
5. Конфигурируем **deploy.rb** что бы залить эти файлы в **shared** папку на хостинге(**digitalocean**).
С каждым новым релизом наше приложение будет ссылаться на папку appname/shared/config, где будут
лежать наши конфиги.
{% highlight ruby %}
# config/deploy.rb
set :linked_files, %w{config/database.yml config/secrets.yml}

namespace :deploy do

  desc 'Upload YAML files.'
  task :upload_yml do
    on roles(:app) do
      execute "mkdir #{shared_path}/config -p"
      upload! StringIO.new(File.read("config/database.yml")), "#{shared_path}/config/database.yml"
      upload! StringIO.new(File.read("config/secrets.yml")), "#{shared_path}/config/secrets.yml"
    end
  end

end
{% endhighlight %}
6. Заливаем файлы на сервер
{% highlight ruby %}
$ cap production deploy:upload_yml
{% endhighlight %}

Теперь при деплое приложения каждый новый релиз на **digitalocean** будет ссылаться
на правильные конфиги в папке **appname/shared/config**