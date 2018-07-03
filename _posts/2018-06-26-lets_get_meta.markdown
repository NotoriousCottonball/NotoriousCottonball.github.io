---
layout: post
title:      "Let's Get Meta: Ruby Introspection and Reflection"
date:       2018-06-26 01:23:42 -0400
permalink:  lets_get_meta
---


Metaprogramming, simply put, is writing code that operates on code rather than data. That's an all-encompassing definition that covers the operation of code compilers like bytecode and code generators like the rails gem. With Ruby, we typically mean something more specific when it comes to Metaprogramming: writing code that writes code **Dynamically** and **At Runtime**. 

Ruby is a great language for Dynamic Metaprogramming given two key characteristics of its higher-order functionality: **Introspection** and **Reflection**. Specifically, Type Introspection and Runtime Reflection. 

Type Introspection refers to the ability of a programming language to examine the properties of the program's objects at runtime. Runtime Reflection, meanwhile, goes a step further and allows a program to actually manipulate the properties of an object at runtime. 

Ruby's Introspection and Reflection allow program's to modify their own structure and behavior at runtime. 

Bottom-Line: We can add a ton of functionality with very few lines of code. 

Let's check out some examples: 

**I. Concise Definitions of Numerous Classes and Methods**

A common use case for Ruby Metaprogramming is to dynamically define methods like Dynamic Finders in Rails' ActiveRecord. Finders are based on columns in the database table and it'd be an insane task to predict every possible column name of every framework user. So, we dynamically define the methods so they're created as needed. 

An even more basic example of Dynamically Defined Methods allows us to succinctly create output methods for the colors of the rainbow:

```
class Rainbow
  COLORS = %w(red, orange, yellow, green, blue, indigo, violet)
  COLORS.each do |color|
    define_method color do
      color
    end
  end
end

Rainbow.new.violet
=> "violet"
```

Iterative Metaprogramming doesn't seem like such a big deal with only the seven colors of the rainbow to content with. But imagine we had thousands of colors, the list kept fluctuating, and we just wanted to make sure we had an output instance method for each color. 

**II. Open Classes**

In Ruby, we can just break open an existing class and add to it. That goes for classes we didn't even originally declare, like default classes. 

Let's say we want to add a method to the String class that simply removes any non-alphanumeric characters. With the `gsub` method and a RegExp argument we can easily add the functionality we want: 

```
class String
    def alphanumeric
        gsub(/[^\w\s]/, '')
    end
end  


"G$eo#r*g$e *&P".alphanumeric
=> "George P"
```

Our method `alphanumeric` is now available for use on any instance of the String class. We can check the list of methods available for a random string like `"Hello World!"`:

```
"Hello World!".methods.detect{|method| method == :alphanumeric}
=> :alphanumeric
```

Obviously the immense flexibility of Ruby can be dangerous. We don't want to open up default classes and start messing around with core functionality willy-nilly:

```
class Array
    def replace(original, replacement)
        self.collect {|element| element == original ? replacement : element }
    end
end
 
p ["1", "2", "3"].replace("2", "0")

>>["1", "0", "3"]
=> ["1", "0", "3"]
```

As useful as our new `replace` method is, we've unintentionally overwritten the default `replace` method for class Array, which replaces the entire array with a new array supplied as an argument. 

However, as long as we're careful, we can have a lot of fun with Metaprogramming within default classes like `Array`: 

```
class Array
  def combine(method)
    inject {|result, i|result.send(method, i)}
  end
end

puts [1000.0, 200.0, 50.0,].combine("+")
>> 1250.0 
=> nil 

puts ["H", "E", "L", "L", "O"].combine("concat")
>> HELLO
=> ni
```

Now we can easily combine the elements of any array with any type of object in all kinds of ways. Note the use of `send` in our `combine` method body to invoke a method by name programmatically. 

**III. Define_Method Advanced** 

Using Define_Method to dynamically generate methods when needed is a really powerful metaprogramming technique. 

`define_method` declares an instance method in the receiver and takes a method parameter as either a Proc, Method, or Unbound Method Object. If a block is specified, Ruby evaluates it using `instance_eval`. We can use `send` to dynamically pass `define_method` along with parameters to the designated receiver. 

We can get pretty fancy when we utilize object relationships and scope.

```
class Dog
  def bark
    puts "WOOF!"
  end
  def create_method(name, &block)
    class << self
    self
    end.send(:define_method, name, &block)
  end
  define_method(:sleep) { puts "ZZZ..." }
end

class Puppy < Dog
  define_method(:yap, instance_method(:bark))
end

fido = Puppy.new 
fido.yap
fido.sleep
fido.create_method (:pee_on_carpet) {puts "Oh no! The #{self.class.to_s} peed on the carpet"}


fido.pee_on_carpet

>> WOOF!
>> ZZZ...
>> Oh no! The Puppy peed on the carpet
=> nil
```

We have 3 uses of `define_method`, two static and one dynamic. The method `sleep` defined in class `Dog` is declared by passing the method name as a symbol and the method block to `define_method` directly. The class `Puppy` inherits the resulting `sleep` method, so it's no surprise that `fido` responds to `sleep`. 

`fido.yap` is a little trickier. The method name is again passed as a symbol to `define_method`, but where do we get the method from? The method is coming from `bark`, which is also inherited from the class `Dog`. `bark` is an instance method and needs to be unbound so we can use it with `define_method`, so we pass it to `instance_method`. The resulting unbound method object provides the method body for our method `yap`. 

Finally, `fido` can learn new methods on the fly. `fido` has this ability because it inherits `create_method` from the class `Dog`. 

`create_method` accepts the method name(as a symbol) and block(as a Proc object), and uses `send` to pass those parameters along with `define_method` to our given receiver. In the example above, we opened the singleton-class of the instance object and our new method is therefore only accessible to `fido`. 

```
Puppy.new.pee_on_carpet

=> undefined method `pee_on_carpet' for #<Puppy:0x0000561ec9740fe8>
```

Likewise:

```
Dog.new.pee_on_carpet

=> undefined method `pee_on_carpet' for #<Dog:0x0000562860109468>
```
 
 We can easily change that. Let's modify the scope of  `create_method`:
 
```
def create_method(name, &block)
    self.class.send(:define_method, name, &block)
 end
```

Now, 

```
fido.pee_on_carpet
>> Oh no! The Puppy peed on the carpet
=>nil
```

And,

```
Puppy.new.pee_on_carpet
>> Oh no! The Puppy peed on the carpet
=> nil
```

But, 

```
Dog.new.pee_on_carpet
=>undefined method `pee_on_carpet' for #<Dog:0x00005587bed6e7c8>
```

Hmmm... Well, what if we increase the scope of `create_method` even more?

```
def create_method(name, &block)
    self.class.superclass.send(:define_method, name, &block)
 end
```

Now, 

```
Dog.new.pee_on_carpet
>> Oh no! The Dog peed on the carpet
=> nil
```

And, as expected, 

```
Puppy.new.pee_on_carpet
>> Oh no! The Puppy peed on the carpet
=> nil
```

And of course, 

```
fido.pee_on_carpet
>> Oh no! The Puppy peed on the carpet
=> nil
```

**IV. Method_Missing & Delegation**

Ruby offers some really neat functionality when we redefine sophisticated default methods like `method_missing`. 

Generally, when we call a method on an object, Ruby follows a very specific protocol in looking up the object model for the definition of that method:

1. The object's own singleton-class/instance method definitions.
2. The instance methods defined in the object's class, which are shared by all instances of that class.
3. The included modules of the class.
4. The superclass.
5. The superclass' included modules. 
6. Finally, the `Kernel` module and in the class `Object`.

If Ruby comes up short, it calls `method_missing`, which usually results in an error raised message.

Overriding that default behavior, however, provides some really powerful dynamic behavior. 

We can redefine `method_missing` inside of a `UserDelegator` class that provides the means to delegate all supporting method calls to the object passed into the constructor. 

```
class UserDelegator
  attr_accessor :object

  def initialize(object)
    self.object = object
  end

  def method_missing(m, *args, &block)
    puts "Delegating #{m} to Player Object"
    object.send(m, *args, &block)
  end
end

class User < UserDelegator 

  def full_name
    "#{first_name} #{last_name[0]}"
  end

end 

Player = Struct.new(:first_name, :last_name, :age)
player = Player.new("George", "Pianka", 28)
user = User.new(player) 

puts user.full_name 

>> Delegating first_name to Player Object
>> Delegating last_name to Player Object
>> George P
=> nil
```

Practically, it'd be much simpler to use Ruby's built-in `SimpleDelegator` class. However, defining our own delegation method highights the flexibility and power of Ruby metaprogramming. 

Our `User` class specifies a way to handle the method call `user.full_name`. While the call reveals the entire first-name, we only want it to reveal the first letter of the last-name. We fully delineate that functionality in the `User` class. 

Our `full_name` method, however, delegates the call to `first_name` and `last_name` to whatever object is passed into the constructor, in this case `player`. All through the UserDelegator class. All because we redefined `method_missing`. 

We can easily change the delegation object by passing something else to the constructor:

```
Admin = Struct.new(:position, :avatar, :first_name, :last_name, :age)
admin = Admin.new("Maintenance", "Vulcan","John", "Smith", 34)


user = User.new(admin) 

puts user.full_name 

>> Delegating first_name to Player Object
>> Delegating last_name to Player Object
>> John S
=> nil
```

**V. Defining Respond_To_Missing? after Overriding Method_Missing**

Following our example above:

```
puts user.first_name

>> Delegating first_name to Player Object
>> John
=> nil
```

If we can call the `first_name` method on `user`, we might assume that `user.respond_to?(:first_name)` & `user.method(:first_name)` would work as well.

They do not.

```
user.respond_to?(:first_name)

=> false

user.method(:first_name)

=> undefined method `first_name' for class `User'
```

That's right. While our `User` class can delegate to the object passed on initialization, it doesn't actually contain that method. So, trying to get the method via `method` will fail. 

Simply redefining `respond_to?` does not solve the problem:

```
class UserDelegator
...
def respond_to?(m, include_private = false)
  true
end
...
end

user.respond_to?(:first_name)

=> true

user.method(:first_name)

=> undefined method `first_name' for class `User'
```

Luckily, since Ruby 1.9, we've had access to an easy fix. We can redefine `respond_to_missing?` in our delegator class and delegate the appropriate response: 

```
class UserDelegator
...
def respond_to_missing?(m, include_private = false)
  true
end
...
end

user.respond_to?(:first_name)

=> true

user.method(:first_name)

=> #<Method: User#first_name>
```


 
