---
layout: post
title:  Cobertura de código Go
date:   2014-05-31 21:53:21
---
Uma das métricas que pode ser utilizada na missão de escrever código com
qualidade é a cobertura de código, que fornece informações sobre quais partes de
um programa são exercitadas por uma determinada suite de testes.

Para gerar a cobertura de um código escrito em [Go][1], é possível utilizar a
ferramenta *go test* da seguinte forma:

{% highlight bash %}
go test -coverprofile=cover.out
{% endhighlight %}

Com a flag *-coverprofile* o comando gera um arquivo de saída com todos os dados
de cobertura de código.

Há uma ferramenta disponível no repositório [go.tools][2] que recebe este
arquivo e gera um HTML com todo o código Go anotado, identificando quais trechos
estão sem cobertura.

Para instalar a ferramenta, basta usar *go get*:

{% highlight bash %}
go get code.google.com/p/go.tools/cmd/cover
{% endhighlight %}

O arquivo HTML é gerado com o seguinte comando:

{% highlight bash %}
go tool cover -html=cover.out -o cover.html
{% endhighlight %}

É possível omitir a flag *-o*, assim o comando abre o HTML gerado diretamente no
navegador.

## tl;dr

Instale a ferramenta de cobertura através do *go get*:

{% highlight bash %}
go get code.google.com/p/go.tools/cmd/cover
{% endhighlight %}

Utilize o seguinte shell script para gerar o arquivo *profile* e o HTML da
cobertura:

{% highlight bash %}
cover () {
    profile="$(mktemp -dt $$)/cover.out"
    go test -coverprofile="$profile" $@ && go tool cover -html="$profile"
}
{% endhighlight %}

Execute no diretório do seu código:

{% highlight bash %}
cover
{% endhighlight %}

O HTML da cobertura será aberto no seu navegador.

[1]: http://golang.org/
[2]: https://code.google.com/p/go.tools
