---
layout: post
title:  MongoDB no Rails com MongoID
date:   2011-09-29 04:17:42
---
[MongoID](http://mongoid.org/) é um ODM (_Object-Document-Mapper_) para MongoDB, escrito em Ruby. Para usá-lo na sua aplicação Rails, basta adicionar no Gemfile:

{% highlight ruby %}
gem "mongoid", "~> 2.2"
gem "bson_ext", "~> 1.3"
{% endhighlight %}

Rodar o Bundle install:

{% highlight sh %}
bundle install
{% endhighlight %}

E, então, rodar o generator:

{% highlight sh %}
rails g mongoid:config
{% endhighlight %}
