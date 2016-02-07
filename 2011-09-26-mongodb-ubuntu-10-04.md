---
layout: post
title:  Instalando o MongoDB no Ubuntu 10.04
date:   2011-09-26 02:18:11
---
Primeiro, adicione a chave GPG do repositório do MongoDB:

{% highlight sh %}
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
{% endhighlight %}

Edite o arquivo _/etc/apt/sources.list_ e adicione o repositório do MongoDB:

{% highlight text %}
deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen
{% endhighlight %}

Atualize a lista de pacotes:

{% highlight sh %}
sudo apt-get update
{% endhighlight %}

E instale o MongoDB:

{% highlight sh %}
sudo apt-get install mongodb-10gen
{% endhighlight %}
