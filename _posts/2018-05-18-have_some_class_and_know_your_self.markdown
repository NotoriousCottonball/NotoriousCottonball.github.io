---
layout: post
title:      "Have Some CLASS and Know (your)SELF"
date:       2018-05-18 23:53:35 -0400
permalink:  have_some_class_and_know_your_self
---

**I.** When I started learning Object-Oriented Ruby, I quickly glossed  over Class and Self and moved right on. The basic idea of more specific things being instances of more general things didn't seem all that difficult or confusing. Neither did using the term Self within the scope of a Class to indicate a Class Method instead of an Instance Method. 

```
class Dog
  @@all = []
  
	attr_accessor :name
  
	def initialize(name) 
    puts "bark"
    @name = name
    @@all << self.name
  end
    
  def self.all
    @@all
  end
end



fido = Dog.new("fido")
#=> #<Dog:0x0000557c9e9f0360>
Dog.all
#=> ["fido"]
```

Simple enough. Here we've got an Instance Variable, @name, a Class Variable @@all, an Instance Method, `initialize`, and a Class Method `all`. When we define a method inside a class, we define an Instance Method, and when we define a method as self.method, we define a Class Method. `initialize` is called on object instances of class `Dog` and `all` is called on the class `Dog`. 

**II.**

**Or is it?**

Turns out, Class Methods are called on Objects. Like all Methods. That's why we think of Classes like blueprints. The blueprint model is very intutive when it comes to our instance methods, like `initialize` above, but not when it comes to a class method like `all`.  `fido` is an instance of the class `Dog` and inherits the methods associated with its parent class. Fair enough. 

But what about the `Dog` class? Does it inherit the `all` method from its parent class like `fido` inherits the `initialize` method? 

Nope. 

```
Dog.class
#=> Class
```

`The class Dog` is of course an object. "Everything in Ruby is an object." So we're told. Not quite: not argument lists or keywords. But we know that whenever something receives a method, it's an object. `Dog` is an instance of the `Class` class and it does inherit methods defined by its class. 

```
Dog.class.methods.sort
#=> [:!, :!=, :!~, :<, :<=, :<=>, :==, :===, :=~, :>, :>=, :__id__, :__send__, :alias_method, :allocate, :ancestors, :attr, :attr_accessor, :attr_reader, :attr_writer, :autoload, :autoload?, :class, :class_eval, :class_exec, :class_variable_defined?, :class_variable_get, :class_variable_set, :class_variables, :clone, :const_defined?, :const_get, :const_missing, :const_set, :constants, :context, :define_method, :define_singleton_method, :deprecate_constant, :describe, :display, :dup, :enum_for, :eql?, :equal?, :example_group, :extend, :fcontext, :fdescribe, :freeze, :frozen?, :hash, :include, :include?, :included_modules, :inspect, :instance_eval, :instance_exec, :instance_method, :instance_methods, :instance_of?, :instance_variable_defined?, :instance_variable_get, :instance_variable_set, :instance_variables, :is_a?, :itself, :json_creatable?, :kind_of?, :method, :method_defined?, :methods, :module_eval, :module_exec, :name, :nesting, :new, :nil?, :object_id, :pp, :prepend, :private_class_method, :private_constant, :private_instance_methods, :private_method_defined?, :private_methods, :protected_instance_methods, :protected_method_defined?, :protected_methods, :public_class_method, :public_constant, :public_instance_method, :public_instance_methods, :public_method, :public_method_defined?, :public_methods, :public_send, :remove_class_variable, :remove_instance_variable, :remove_method, :respond_to?, :send, :shared_context, :shared_examples, :shared_examples_for, :singleton_class, :singleton_class?, :singleton_method, :singleton_methods, :superclass, :taint, :tainted?, :tap, :to_enum, :to_json, :to_s, :trust, :undef_method, :untaint, :untrust, :untrusted?, :used_modules, :xcontext, :xdescribe, :yield_self]

Dog.methods.sort
#=> => [:!, :!=, :!~, :<, :<=, :<=>, :==, :===, :=~, :>, :>=, :__id__, :__send__, :alias_method, :all, :allocate, :ancestors, :attr, :attr_accessor, :attr_reader, :attr_writer, :autoload, :autoload?, :bark, :class, :class_eval, :class_exec, :class_variable_defined?, :class_variable_get, :class_variable_set, :class_variables, :clone, :const_defined?, :const_get, :const_missing, :const_set, :constants, :context, :define_method, :define_singleton_method, :deprecate_constant, :describe, :display, :dup, :enum_for, :eql?, :equal?, :example_group, :extend, :fcontext, :fdescribe, :freeze, :frozen?, :hash, :include, :include?, :included_modules, :inspect, :instance_eval, :instance_exec, :instance_method, :instance_methods, :instance_of?, :instance_variable_defined?, :instance_variable_get, :instance_variable_set, :instance_variables, :is_a?, :itself, :json_creatable?, :kind_of?, :method, :method_defined?, :methods, :module_eval, :module_exec, :name, :new, :nil?, :object_id, :pp, :prepend, :private_class_method, :private_constant, :private_instance_methods, :private_method_defined?, :private_methods, :protected_instance_methods, :protected_method_defined?, :protected_methods, :public_class_method, :public_constant, :public_instance_method, :public_instance_methods, :public_method, :public_method_defined?, :public_methods, :public_send, :remove_class_variable, :remove_instance_variable, :remove_method, :respond_to?, :send, :shared_context, :shared_examples, :shared_examples_for, :singleton_class, :singleton_class?, :singleton_method, :singleton_methods, :superclass, :taint, :tainted?, :tap, :to_enum, :to_json, :to_s, :trust, :undef_method, :untaint, :untrust, :untrusted?, :xcontext, :xdescribe, :yield_self]
```

Checking for methods that can be called on the object `Dog` and the object `Dog.class` reveal that method `all` is not inherited by the Dog class from the Class class. Well, that makes sense. We defined it inside the Dog class after all. 

**III.**

**So, what exactly did we do by stating `def self.all?` **

It helps to consider some of the other ways we could have defined the class method `all`:

```
1. 
 class Dog
  def self.all
    @@all
  end
end

2. 
class Dog
  class << self
    def all
      @@all
    end
  end
end

3. 
class << Dog
  def all
    @@all
  end
end


4.
def Dog.all
  @@all
end
```

We used the first snippet within the Dog class at the beginning of this article. The last snippet works because we can define class methods (as well as instance methods) outside of the class with the syntax `Class.method` or `instance.Method`. 

**IV.**

**But what about the two snippets in the middle?**

Those angle quotes signify that we've opened up the **singleton class** or the **metaclass** or, even more obscurely, the **eigenclass** of our object `Dog`. (The latter is not uniformely accepted in the Ruby Community, but roughly translates to "self," "own," "particular to," "proper/characteristic," and sounds epic and Wagnerian, so I like it.)

Wait a second... `Dog` is an object?

Yup. It's a class. It's an object. Heck, in Ruby, `Class` is an object. It's also the class of `Dog`. Now we're getting close to an the problem of an infinite regress. 

Eigenclasses, Singleton Classes, or Metaclasses, however, are only created when needed. So, yes, we can keep calling `singleton_class` on Dog:

```
Dog.singleton_class.singleton_class.singleton_class
#=> #<Class:#<Class:#<Class:Dog>>>
```

But Ruby won't keep doing that on it's own. 

Note that calling `singleton_class` on `Dog` isn't the same thing as calling `class` on it. 

```
Dog.class
#=> Class

Dog.singleton_class.singleton_class.singleton_class
#=> #<Class:#<Class:#<Class:Dog>>>
```

When we open up that singleton_class, eigenclass, metaclass, whatever, on `Dog`, we're opening up the class where we're storing our methods. 

**V.**

**`Dog` is an instance of the class `Class`. Methods we write into the singleton class of Dog aren't defined by the class of the object `Dog`. **

We could do it that way. Let's define a method inside the class `Class`:

class Class
   def our_awesome_method
    puts "cool"
   end
end

Dog.methods.sort
#=> [:!, :!=, :!~, :<, :<=, :<=>, :==, :===, :=~, :>, :>=, :__id__, :__send__, :alias_method, :all, :allocate, :ancestors, :attr, :attr_accessor, :attr_reader, :attr_writer, :autoload, :autoload?, :bark, :class, :class_eval, :class_exec, :class_variable_defined?, :class_variable_get, :class_variable_set, :class_variables, :clone, :const_defined?, :const_get, :const_missing, :const_set, :constants, :context, :define_method, :define_singleton_method, :deprecate_constant, :describe, :display, :dup, :enum_for, :eql?, :equal?, :example_group, :extend, :fcontext, :fdescribe, :freeze, :frozen?, :hash, :include, :include?, :included_modules, :inspect, :instance_eval, :instance_exec, :instance_method, :instance_methods, :instance_of?, :instance_variable_defined?, :instance_variable_get, :instance_variable_set, :instance_variables, :is_a?, :itself, :json_creatable?, :kind_of?, :method, :method_defined?, :methods, :module_eval, :module_exec, :name, :new, :nil?, :object_id,  :pp, :prepend, :private_class_method, :private_constant, :private_instance_methods, :private_method_defined?, :private_methods, :protected_instance_methods, :protected_method_defined?, :protected_methods, :public_class_method, :public_constant, :public_instance_method, :public_instance_methods, :public_method, :public_method_defined?, :public_methods, :public_send, :remove_class_variable, :remove_instance_variable, :remove_method, :respond_to?, :send, :shared_context, :shared_examples, :shared_examples_for, :singleton_class, :singleton_class?, :singleton_method, :singleton_methods, :superclass, :taint, :tainted?, :tap, :to_enum, :to_json, :to_s, :trust, :undef_method, :untaint, :untrust, :untrusted?, :xcontext, :xdescribe, :yield_self]

Now `Dog` actually inherits method `our_cool_method` from its class `Class`.

**VI.**

**The important thing here is that when we use the variable `self` to define the method `all` as in any of the first 3 snippets from above, or just declare the class method outright with `Dog.all`, we're opening a metaclass and not a hierarchical class. **

In Ruby, methods must be associated with a class of some kind. Our method `all` is really just an instance method of the metaclass associated with the object `Dog`. 

A lot of the confusion  surrounding **self** and **class** stems from the semantic ambiguity of what we mean by **class**.


We're used to defining our instance methods within the class that serves as a blueprint for our instance objects. We could, similarly, define our class methods within the class `Class` that serves as the blueprint for our class objects. 

But we tend to define our class methods as singleton methods. It's worth noting we could do the same for our instance methods. Drawing from the example at the beginning of this article, we could define a method in the singleton class of `fido` by simply opening the singleton class of `fido`:

```
def fido.howl
   puts "awooo"
end
```

Our instance object does not inherit the method from its parent class. Instead, the method is associated with its own metaclass. No other objects (no other instances of Dog) will inherit the method `howl`  unless we define it inside the class Dog. 

`self` is, by itself, quite simple. It's just a variable referring to the current object--the object that is receiving the current message. 

There's always a `self` just like there's always an object. If we fire up an irb or whatever and simply type `puts "Hello World!"` something has to receive our message. Just type `self` and the return indicates `#=> main` 

What the heck is main? 

It's just the top level context or scope of our Ruby program. 

`self.class` returns `#=> Object`

Which brings us to the Ruby Object Model, an endless source of wonder and fascination.


![rubyclasshierarchy](http://www.flickr.com/photos/yugui/3571683843)


Things get complicated when we start to think of `self` within the network of class relationships that power the functionality of the language. Which is kind of the point. The power of Ruby is in the simplicity of the objects and the complexity of their interaction. That's what makes Ruby so well-suites to Metaprogramming, that is, writing code that writes code at runtime. 

In order to leverage the full power of Ruby Metaprogramming, we need to have a solid grasp of the object model. We don't want to get lost in the sometimes mind-boggling abstraction of the Ruby Universe.

So, liike the title says, Have Some Class and Know (your) Self. 





















