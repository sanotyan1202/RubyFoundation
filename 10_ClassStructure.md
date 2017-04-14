# 10章 Rubyのクラスの仕組み
この章ではRubyのクラスの仕組みを説明します。

## 10-1 インスタンスとクラス
Rubyのクラスは全て、Classクラスのインスタンスです。
classメソッドを使って確認することができます。
```ruby
"Hello".class   #String
String.class    #Class
Fixnum.class    #Class
Object.class    #Class
```
ClassもClassクラスのインスタンスです。
```ruby
Class.class     #Class
```
図で書くと以下の様に表せます。  
![クラス図](img/ClassStructure.png)

Classにインスタンスメソッドを定義することで、全てのクラスでそのメソッドを使うことができます。
```ruby
class Class
  def say_hello
    p "hello"
  end
end

String.say_hello  #hello
```
たとえば、newメソッドなどが最初からClassに定義されています。非常にシンプルで、オブジェクト指向的な構成であると言えます。
```ruby
Class.instance_methods(false)
#[:allocate, :new, :superclass]
```

## 10-2 Moduleとクラス
RubyのModuleはClassの親クラスです。
```ruby
Class.superclass  #Module
Class.ancestors   #[Class, Module, Object, Kernel, BasicObject]
```
Classクラスで定義されているメソッドは、以下の通りでした。
```ruby
Class.instance_methods(false)
#[:allocate, :new, :superclass]
```
このことから、[:allocate, :new, :superclass]が定義されていないだけでClassとModuleは同じものと言えます。
```ruby
module Greetable
  @@message = "Hello "  # クラス変数が宣言できる
  attr_accessor:name    # インスタンス変数が宣言できる
  def initialize(name)  # initializeが宣言できる
    @name = name        # メンバ変数
  end

  def greet
    p @@message + @name
  end
end

class Human
  include Greetable
end

hum = Human.new("World!")
hum.greet   # Hello World!
```

## 10-3 特異メソッドとクラス
Rubyではインスタンスには特異メソッドを定義することができました。
全てのクラスはインスタンスでもあるので、特異メソッドが定義できます。
```ruby
def String.say_hello
  p "hello"
end

String.say_hello #hello
```
クラスの特異メソッドはクラスメソッドと同義です。
クラスメソッドの書き方を覚えているでしょうか。
```Ruby
class String
  def self.say_bye
    p "bye"
  end
end
```
selfをStringに置き換えても同様の結果になります。

## 10-4 Mainクラス
Rubyのselfは、現在のオブジェクトを示します。
```ruby
class String
  p self
end
# String

class String
  def instance_method
    p self
  end
end
# :instance_method

Proc.new do
  p self
end
# <Proc:0x007f91dd095850@>  
```
では、「何も無い場所」でselfを指定したら何が表示されるでしょうか。
```ruby
self
# main
```
Rubyのソースは全てmainクラスの中にあることがわかります。
何も無い場所でメソッドを呼び出すと、mainクラスのメソッドを呼び出すことになります。
```ruby
self.methods
#[:to_s, :inspect, :nil?, :===, :=~, :!~, :eql?, :hash, :<=>, :class, :singleton_class, :clone, :dup, :itself, :taint, :tainted?, :untaint, :untrust, :untrusted?, :trust, :freeze, :frozen?, :methods, :singleton_methods, :protected_methods, :private_methods, :public_methods, :instance_variables, :instance_variable_get, :instance_variable_set, :instance_variable_defined?, :remove_instance_variable, :instance_of?, :kind_of?, :is_a?, :tap, :send, :public_send, :respond_to?, :extend, :display, :method, :public_method, :singleton_method, :define_singleton_method, :object_id, :to_enum, :enum_for, :==, :equal?, :!, :!=, :instance_eval, :instance_exec, :__send__, :__id__]

to_s
# main
```
mainクラスもClassのインスタンスなので、特異メソッドを定義することができます。
```ruby
def self.say_hello
  p "hello"
end

say_hello   #hello
```
mainに特異メソッドを定義することで「何も無い場所」で呼び出すことができます。

Rubyのソース上ではClassもModuleも「何もない場所」も、全てがオブジェクトであることがわかります。
