---
layout: post
title:  Captura de exceções no Ruby e a pegadinha do malandro
date:   2011-12-10 22:35:07
---
Há algumas semanas tive uma pequena surpresa ao tentar capturar uma exceção no
Ruby:

{% highlight ruby %}
class MyException < Exception
end

begin
    raise MyException.new
rescue
    puts "Exception capturada"
end
{% endhighlight %}

Simplesmente a cláusula `rescue` não estava funcionando. Aí eu fui ler a
[documentação do Ruby](http://www.ruby-doc.org/core-1.9.3/StandardError.html)
e...

> The most standard error types are subclasses of StandardError. A rescue clause
> without an explicit Exception class will rescue all StandardErrors (and only
> those).

Traduzindo, uma cláusula `rescue` sem uma Exception explícita irá capturar
apenas erros do tipo `StandardError`. Como `MyException` era uma subclasse de
`Exception`, o `rescue` acabou assumindo esse comportamento.

Shame on me. Rá!
