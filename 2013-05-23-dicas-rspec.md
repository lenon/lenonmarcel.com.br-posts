---
layout: post
title:  Algumas dicas de RSpec
date:   2013-05-23 19:10:07
---
A DSL do RSpec é muito rica em recursos. Infelizmente nem sempre conhecemos
todos eles. Este post é uma pequena compilação de alguns matchers e sintaxes
alternativas não tão conhecidas, mas que podem auxiliar na árdua tarefa de
escrever testes concisos e expressivos.

## Before com condições

É possível definir condições para um bloco *before*:

```ruby
describe Something do
  before(:each, mycondition: true) do
    puts "hey"
  end

  it "should do something", mycondition: true do
    # code...
  end

  it "should do something else" do
    # code...
  end
end
```

No exemplo acima, o before será executado apenas no primeiro bloco *it*, pois
ele possui o metadado *mycondition* definido como *true*.

## Expect

Desde o RSpec 2.11 uma nova sintaxe de expectativas (!?) está disponível:

```ruby
expect(subject.something).to be_true
```

Apesar do já conhecido *should* ser mais elegante, ele nem sempre é confiável.
Basicamente o RSpec precisa definí-lo em todos os objetos do sistema, o problema
é que o RSpec não conhece todos os objetos do sistema. O *expect* é uma solução
para esse tipo de situação.

Você pode ler mais sobre o *expect* na [documentação do RSpec][1]. Para entender
exatamente o problema do *should*, leia [este excelente post][2].

## Subjects aninhados (nested subjects)

Assim como em métodos normais do Ruby, é possível utilizar a keyword *super*
para fazer overriding nos blocos *subject*, *let* e *let!*:

```ruby
describe MyClass do
  subject { MyClass.new("something") }

  describe ".whatever" do
    subject { super().whatever }
  end
end
```

Uma observação importante é que você deve utilizar os parênteses *()* ao invocar
*super*. Do contrário, você receberá um erro *RuntimeError*:

```text
RuntimeError: implicit argument passing of super from method defined by define_method() is not supported. Specify all arguments explicitly.
```

## Described class

Ao descrever uma classe, o RSpec disponibiliza o método *described_class*, que
retorna a classe que a spec descreve. Você pode utilizá-lo, por exemplo, para
criar novas instâncias dessa mesma classe:

```ruby
describe MyClass do
  describe ".whatever" do
    let(:bla) { described_class.new("something") }
  end
end
```

Isso facilita um futuro refactoring, evitando que você tenha que escrever o nome
da classe por todo lado.

## Specify

O RSpec possui alguns métodos para descrição de specs. Os mais conhecidos são
*it*, *describe* e *context*. Há, além desses, o *specify*:

```ruby
describe MyClass do
  subject { described_class.new("Something") }
  specify { subject.title.should eql("Something") }
end
```

Funcionalmente não há nenhuma diferença entre *it* e *specify*. O último é
apenas um alias para o primeiro e seu uso deve ser feito com o objetivo de
deixar as specs mais expressivas e legíveis.

## Its

Outro método útil para deixar as specs mais expressivas é o *its*. Ele pode ser
utilizado ao descrever objetos de retorno:

```ruby
describe MyApi do
  describe "GET /foo/bar" do
    before { get "/foo/bar" }

    subject { last_response }

    its(:status) { should eql(200) }
    its(:body) { should include "foobarbaz" }
  end
end
```

Ele suporta, ainda, coisas como nested attributes:

```ruby
its("phone_numbers.size") { should eq(2) }
```

E Hashes:

```ruby
subject do
  { key: "value" }
end
its([:key]) { should eq "value" }
```

## Satisfy

O *satisfy* é um dos matchers mais flexíveis disponíveis no RSpec. Ele aceita um
bloco e espera que o retorno seja *true* para um teste executado com sucesso:

```ruby
expect(subject.created_at).to satisfy { |time| (0..5).include? Time.now.to_i - time.to_i }
```

O único problema é que a mensagem de saída é algo como "expected something to
satisfy block", ou seja, não é muito clara.

----------

E você, conhece mais algum truque legal com o RSpec? Compartilhe nos
comentários! :)

[1]: https://www.relishapp.com/rspec/rspec-expectations/docs
[2]: http://myronmars.to/n/dev-blog/2012/06/rspecs-new-expectation-syntax
