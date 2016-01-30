---
layout: post
title:  MongoDB no Rails com MongoID
date:   2011-09-29 04:17:42
---
[MongoID](http://mongoid.org/) é um ODM (_Object-Document-Mapper_) para MongoDB, escrito em Ruby. Para usá-lo na sua aplicação Rails, basta adicionar no Gemfile:

```ruby
gem "mongoid", "~> 2.2"
gem "bson_ext", "~> 1.3"
```

Rodar o Bundle install:

```sh
bundle install
```

E, então, rodar o generator:

```sh
rails g mongoid:config
```
