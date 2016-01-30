---
layout: post
title:  RSpec 3 - Verifying doubles
date:   2014-07-25 04:45:37
---
No RSpec 3, especificamente na versão mais recente do projeto **rspec-mocks**,
uma feature muito interessante foi incorporada à ferramenta: a possibilidade
de verificar se *stub methods* estão, de fato, implementados.

Segundo a documentação, os chamados *verifying doubles* são uma alternativa aos
*doubles* normais. Ao utilizá-los, o RSpec executa verificações que garantem que
o método está presente na classe (ou objeto) representado pelo *stub*, fazendo o
teste falhar se um método inválido ou inexistente é chamado. Ele é capaz de
checar, inclusive, se o número de argumentos passado é igual ao esperado.

Essa feature é interessante por dois motivos:

* Ela permite refatorar a interface/protocolo de uma classe sem medo, deixando
essa validação no lado do framework de testes.
* Adiciona um nível muito maior de confiança aos seus testes, com um custo
muito baixo.

Para utilizar os verifying doubles, o RSpec provê alguns métodos e configurações
que estão detalhados abaixo.

## instance_double

Este tipo de verifying double serve para criar stubs de métodos de instância,
verificando se estão presentes numa determinada classe. Essa classe é passada
como primeiro argumento do double.

Por exemplo, ao criar um stub para um método inexistente...

```ruby
comment = instance_double('Comment')
expect(comment).to receive(:risos)
```

... o RSpec falha o teste e exibe a seguinte mensagem:

```text
Comment does not implement: risos
```

Ao criar um stub com argumentos a menos que os esperados...

```ruby
comment = instance_double('Comment')
expect(comment).to receive(:risos).with('a')
```

... o RSpec também falha:

```text
Wrong number of arguments. Expected 2, got 1.
```

Porém, quando um *instance_double* é criado para uma classe inexistente, nada
acontece. Esse problema é facilmente resolvido através da seguinte configuração
do RSpec:

```ruby
RSpec.configure do |config|
  config.mock_with :rspec do |mocks|
    mocks.verify_doubled_constant_names = true
  end
end
```

Assim, ao tentar criar um *instance_double* de uma classe inexistente...

```ruby
comment = instance_double('Coment')
```

... o RSpec falha com a seguinte mensagem:

```text
Coment is not a defined constant. Perhaps you misspelt it?
```

É importante, porém, salientar que o *instance_double* **não sabe verificar
métodos implementados através de method_missing**. Isso é resolvido por outros
tipos de doubles disponíveis.

## class_double

Este tipo de verifying double atua da mesma forma que o *instance_double*,
a diferença é que, neste caso, as verificações são contra métodos de classe.

Por exemplo, ao criar um stub para um método de classe inexistente...

```ruby
comment = class_double('Comment')
expect(comment).to receive(:something)
```

... o RSpec falha da mesma forma:

```text
Comment does not implement: something
```

## object_double

Este tipo de verifying double pode ser utilizado para criar um double a partir
de um objeto existente. Ao contrário do *instance_double*, um *object_double*
sabe verificar métodos que fazem uso de method_missing.

Por exemplo, ao criar um stub para um método inexistente...

```ruby
comment = object_double(Comment.new)
#<Comment:0x00000105eb7908> does not implement: something
```

... o RSpec falha, exatamente como acontece com outros doubles.

## Partial doubles

Um *partial double* é, basicamente, uma extensão de um objeto real. Por exemplo,
quando escrevemos o seguinte código...

```ruby
comment = double('comment')
allow(Comment).to receive(:find) { comment }
```

... estamos criando um partial double para a classe *Comment*. Um problema
comum com esse tipo de double é que, caso a classe/objeto não implemente o
método stubbed, nada acontece (o teste não falha). Até aí nada de novo, já que
os partial doubles estão presentes há um bom tempo no RSpec.

Para resolver esse problema, o RSpec 3 introduziu uma configuração chamada de
*verify\_partial\_doubles*. Quando ativa, as mesmas verificações dos
verifying doubles são executadas para os partial doubles.

Para ativar, basta configurar o RSpec da seguinte forma:

```ruby
RSpec.configure do |config|
  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end
end
```

Assim, quando um stub é criado para um método inexistente...

```ruby
comment = double('comment')
allow(Comment).to receive(:blah) { comment }
```

... o teste falha:

```text
Comment does not implement: blah
```

## Finalizando

Essa feature é uma das melhores novidades introduzidas pelo RSpec 3. Vale
investir um tempo atualizando seu projeto, já que o trabalho é pouco e o ganho
é enorme. :)

A documentação completa dos verifying doubles pode ser lida [aqui](https://relishapp.com/rspec/rspec-mocks/v/3-0/docs/verifying-doubles).
