---
layout: post
title: Exportando/importando dados no PostgreSQL com COPY
---
A forma mais comum de exportar e importar dados para um banco PostgreSQL é
através dos comandos `SELECT` e `INSERT`. Para a maioria dos casos, esses dois
comandos são suficientes. Porém, quando surge a necessidade de manipular milhões
de registros ao mesmo tempo, esses comandos não são mais tão eficientes. A
[solução do PostgreSQL][1] para esse tipo de problema é o comando `COPY`.

O `COPY` pode ser usado tanto para importar como para exportar dados do
banco. Ele suporta arquivos nos formatos texto, CSV e binário.

## Exemplo de exportação

Para exportar todos os registros de uma tabela para um arquivo `.csv`, podemos
executar uma query da seguinte forma:

{% highlight sql %}
COPY users TO '/tmp/file.csv' CSV;
{% endhighlight %}

Isso vai copiar todos os registros da tabela `users` para um arquivo no formato
CSV. Também é possível passar um `SELECT` como parâmetro:

{% highlight sql %}
COPY (SELECT id, name FROM users) TO '/tmp/file.csv' CSV;
{% endhighlight %}

No exemplo acima apenas as colunas `id` e `name` da tabela `users` serão
inseridas no CSV.

## Exemplo de importação

Para importar os registros de um arquivo CSV para o PostgreSQL, podemos executar
uma query dessa forma:

{% highlight sql %}
COPY users FROM '/tmp/file.csv' CSV;
{% endhighlight %}

Um erro bastante comum no uso do comando `COPY` é passar um caminho de arquivo
local, esperando que o PostgreSQL vá escrever nesse arquivo, quando na verdade o
arquivo precisa ser acessível pelo servidor e não pelo cliente.

Para contornar essa limitação é possível enviar e receber os dados através dos
canais de IO padrão (STDIN e STDOUT).

## COPY no ActiveRecord

O ActiveRecord do Rails não possui nenhum suporte ao comando `COPY`. Felizmente
a gem pg, que é a interface Ruby para o PostgreSQL, implementa [alguns
métodos][2] que acabam facilitando o uso desse comando no Rails.

Para copiar dados através do STDOUT e processar no Ruby, podemos fazer isso:

{% highlight ruby %}
raw = ActiveRecord::Base.connection.raw_connection
raw.copy_data('COPY users TO STDOUT CSV') do
  while line = raw.get_copy_data
    CSV.parse(line) do |row|
      puts row
    end
  end
end
{% endhighlight %}

Para cada registro retornado, o bloco `while` será chamado. Isso é útil para
fazer algum processamento do registro, como convertê-lo para um formato
específico ou mesmo salvá-lo num arquivo local.

Já para inserir dados através do STDIN:

{% highlight ruby %}
raw = ActiveRecord::Base.connection.raw_connection
raw.copy_data('COPY users FROM STDIN CSV') do
  raw.put_copy_data(%w(1 foo).to_csv)
  raw.put_copy_data(%w(2 bar).to_csv)
  raw.put_copy_data(%w(3 baz).to_csv)
end
{% endhighlight %}

O método `put_copy_data` envia os dados através do STDIN da conexão com o
PostgreSQL e pode ser usado para, por exemplo, inserir todas as linhas de um
arquivo CSV local no banco.

-----

## Referências

* [PostgreSQL 9.5.0 Documentation: COPY][1]
* [Documentation for pg][2]

[1]: http://www.postgresql.org/docs/current/static/sql-copy.html
[2]: http://www.rubydoc.info/gems/pg/PG/Connection%3Acopy_data
