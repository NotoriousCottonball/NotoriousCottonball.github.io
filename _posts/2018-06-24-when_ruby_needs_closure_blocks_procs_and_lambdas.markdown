---
layout: post
title:      "When Ruby Needs Closure: Blocks, Procs, and Lambdas"
date:       2018-06-24 09:32:06 -0400
permalink:  when_ruby_needs_closure_blocks_procs_and_lambdas
---


What the Proc?! 

Voyaging deeper into Ruby's enchanted forest, we can't help but stumble upon the confusing and intriguing use of closures. 

Closures, Blocks, Procs, and Lambdas- Oh my! 

Generally speaking, they're all just ways of grouping code. And, generally speaking, you can usually get by without using Procs and Lambdas, if you don't mind a life bereft of sweet, sweet syntactic sugar. 

But we're a curious sort and Ruby offers much in the way of adventurous constructs. Sometimes they're useful, sometimes only marginally so. But they're always interesting. 

So let's talk closures.


**I. Limitations on Methods and Blocks in Ruby**

In Ruby, we store pieces of code in the form of methods so we can later execute them on demand. 

```
def our_method 
   puts "first thing" 
end

our_method 

# first thing
#=> nil
```

Easy enough. 

However, unlike other languages like JavaScript, Ruby does not allow us to pass methods as arguments to other methods. Additionally, Ruby methods cannot themselves return methods. So, if we're in the habit of passing and returning functions to and from other functions, then, in the words of a certain satirical animated series, "youre gonna have a bad time." 

There's a significant cognitive shift, for example, in going from the deeply nested anonymous functions approach in JavaScript to the One-Method, One-Block functionality of Ruby. One of the hallmarks of well-written object-oriented Ruby programming is simple methods and complex interactions, which might explain the special treatment of blocks as language constructs instead of objects. 

That's right. Unlike pretty much everything else in Ruby, blocks and methods are not objects. Blocks are coupled to methods, which in turn are bound to objects.  Blocks complement the functionality of regular methods and cannot be moved around, referenced, and do not respond to methods. Again, they're not objects. They're chunks of code, one-off, and we can't even reuse them.

As far as methods go, sure, they can be accessed as objects bound to a class or wrapped in an object really easily, so I guess they're *essentially* objects... But, the point here is that we're really limited with our use of blocks in Ruby. Every method can receive just a single *implicit* block, and all it can really do is call it before it falls out of scope. 

So we've got 3 big reasons to check out Procs and Lambdas: 

1.) We can't pass methods as arguments.
2.) We can't return methods from methods. 
3.) We can't pass more than one block to a method/do more than just call it. 

We may never actually require the use of more flexible closures in Ruby. However, if we ever do, we can easily convert blocks into Procs or Lambdas. 

**II. All About Blocks** 

Blocks are not objects. They are chunks of code which only have meaning as part of the syntax of a method call. We can't even store them for reuse. It doesn't matter if we write them multi-line format, between `do` and `end`, or inline between `{` and `}`. 

```
a = {puts "I can't stand alone"}
#syntax error 

a = do 
puts "I can't stand alone"
end 
#syntax error
```

Again, a Block isn't an object, it's *bound* to an object. We can't store it in a variable, reuse it, or pass more than one to a method. 

Why's that?

Well, it has to do with what's going on behind the scenes. 

```
def best_method_ever
   puts "Here we go!"
	 yield
	 puts "And...we're done."
end 

best_method_ever do 
   puts "Now I'm yielding"
end 

#Here we go!
#Now I'm yielding 
#And...we're done.

#=> nil 
``` 

When our method, best_method_ever, reaches the line with yield, the code inside the block is executed. Once the code in the block finishes, the execution of best_method_ever continues. It doesn't matter if we pass the block in multi-line format as above or inline format as below.  

```
def best_method_ever
   puts "Here we go!"
	 yield
	 puts "And...we're done."
end 

best_method_ever {puts "Now I'm yielding"}

#Here we go!
#Now I'm yielding 
#And...we're done.

#=> nil 
```

It's important to note that we can go ahead and pass a block to any function, it just won't necessarily be executed. In order for that to happen, YIELD needs to call it. 

On the other hand, if we do call YIELD in our method, then the block parameter is mandatory. The method will raise an exception if it does not receive a block.

```
def best_method_ever
   puts "Here we go!"
	 yield
	 puts "And...we're done."
end 

best_method_ever

#Here we go!
#=>no block given(yield)
```

The method call only prints the string `Here we go!` before raising an exception. That is, everything goes swimmingly until yield tries to call the nonexistant block. 

Need to make a block an optional parameter? Use method block_given? for control flow.

```
def best_method_ever_with_optional_block
   if block_given?
	  puts "Here we go!"
	  yield
	  puts "And...we're done."
	 else
	  "no block"
	 end
end 

best_method_ever_with_optional_block

#=> "no block"

best_method_ever_with_optional_block {puts "Now I'm yielding"}

#Here we go!
#Now I'm yielding 
#And...we're done.

#=> nil

```

Finally, we can pass parameters to the block through YIELD. The order is important, since the order we use to pass the parameters to YIELD is the order they are received by the Block. 

```
def our_method
   yield("George", "Too Many")
end 

our_method do |name, age|
   puts "#{name} is #{age} years old"
end 

#George is Too Many years old 
#=> nil
   
```

Note that the parameters inside the block are local to the block. 

With our understanding of YIELD we can understand how methods like SELECT work with methods like MATCH to filter all the elements in an array. 

```
week = ["monday", "tuesday", "wednesday", "thursday", "friday"]  
 
 week.select do | item |
     item.match /^t/
 end

#=> ["tuesday", "thursday"]
```

This kind of usage is possible because of an operation like YIELD. Under the hood, SELECT is yielding each `item` in the `week` array to the block with method MATCH. The Block passed to SELECT accepts parameters through a yield-like functionality in the SELECT method. 

We actually use this pattern to initialize objects with default values. The initializer calls yield(self) , which is the object being initialized

```
class Dog
  attr_accessor :breed, :weight

  def initialize
    yield(self)
  end
end

puppy = Dog.new do |new_dog|
  new_dog.breed = "Bichon Frise"
  new_dog.weight = 1
end

puts "My puppy is a #{new_dog.breed} and it weighs #{new_dog.weight} pound(s)."

# My puppy is a Bichon Frise and it weighs 1 pound(s).
#=> nil
```

**III. All About Procs**

So, if we can do all this great stuff with Blocks, why do we need closures? Well, again, in addition to the convenience and expediency of the syntactic sugar they provide, we access a means of passing more than one method as an argument and returning methods from methods. 

First off, while a Block is a one-off chunk of code that's passed along to a method, a Proc is an object. So, we can call methods on it and assign it to variables. 

Which means we can make use of a little shorthand.

```
week = ["monday", "tuesday", "wednesday", "thursday", "friday"]  
 
week.collect(&:capitalize)

#=> ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
```

The ampersand("&") in front of the method indicates we are treating the method as an object. So instead of passing the whole block in multi-line or inline form, we can just pass the method in object form, as a Proc. 

Cute. But what else can we do? 

We can pass multiple Procs as arguments, given they're just objects.

```
def passing_multiple_methods(proc1, proc2)
  proc1.call
  proc2.call
end

a = Proc.new { puts "Result of One Method" }
b = Proc.new { puts "Result of Another Method" }

passing_multiple_methods(a,b)

#Result of One Method
#Result of Another Method
#=> nil
```

Also, instead of using YIELD and having to write out the block passed to the method each and every time, we enjoy greater flexibility and control using Procs. 

When we pass a Proc into a method, we can determine when we call it by storing it as a variable and simply passing it as an argument.

```
def our_method  our_proc
  puts "Here we go!"
  our_proc.call
  puts "And...we're done!"
end

greet= Proc.new do
  puts "Hello, my friend!"
end

dismissive = Proc.new do
  puts "Eh, piss off!"
end

our_method greet

#Here we go!
#Hello, my friend!
#And...we're done!
#=> nil

our_method dismissive
#Here we go!
#Eh, piss off!
#And...we're done!
#=> nil
```


And, we can write methods that determine how many times, or even if, to call a Proc.

```
def fifty_fifty(our_proc)
   if rand(2) == 0
    our_proc.call
  end
end

def winner (our_proc)
  our_proc.call
  puts "Double to Win..."
end

coin_toss = Proc.new do
  puts "HEADS"
end

red_black = Proc.new do
  puts "RED"
end

fifty_fifty(coin_toss)
winner(coin_toss)

fifty_fifty(red_black)
winner(red_black)

#HEADS
#Double to Win...
#RED
#RED
#Double to Win...
#=> nil

```

**IV. So What's a Lambda?**


A Lambda is very similar to a Proc. It's a closure that's a Proc object as well, but with three important distinctions. 

1.) Initializing a Proc vs Lambda requires different notation

```
new_proc = Proc.new { |x| puts x }

new_lam = lambda { |x| puts x }

```

2.) Procs ignore extra arguments or return nil if they require an argument, but none is passed. Meanwhile, Lambdas check the number of arguments and raise Argument Errors if the number of arguments does not match. 

```
new_proc = Proc.new { |x| puts x }
new_proc.call("Monday", "Tuesday", "Wednesday")

#Monday
#=> nil

new_lam = lambda { |x| puts x }
new_lam.call("Monday", "Tuesday", "Wednesday")

#=> ArgumentError: wrong number of arguments (3 for 1)

```

3.) Procs and Lambdas handle RETURN statements very differently. Lambdas exit out of the closure in familiar fashion while Procs return from the method enclosing the Proc. Why? No idea. Only Matz knows...

RETURN inside of a Lambda takes us right outside the Lambda Code. Makes sense...

```
def lambda_method
  new_lambda = lambda { return }
  new_lambda.call
  puts "As expected"
end

lambda_method

# "As expected
#=>nil
```

Meanwhile, RETURN inside of a Proc takes us outside of the WHOLE METHOD enclosing that Proc?!

```
def proc_method
  new_proc = Proc.new { return }
  new_proc.call
  puts "We won't even get here..."
end

proc_method

#=> nil
```

So, a Lambda behaves a lot more like a regular Ruby method than a Proc. It's more familiar, and therefore might be a better option in most cases where we want to use closures. 

**V. Shorthand and Anonymous Functions**

Sometimes it's a lot more convenient to use anonymus functions with Procs or Lambdas rather than define a method. 
 
 Especially since Ruby 1.9, with shorthand notation to create and execute Lambdas. 
 
 Let's create out own higher order function

 
 ```
class Items
      
			@@discounted_sales = []
			
			def self.discounted_sales
			   @@discounted_sales
			end
			
			def initialize(argument)
        @value = argument
      end

      def apply_discount(lambda)
        @@discounted_sales << lambda.(@value)
      end
end

half_off = -> x { x /2 }
Items.new(10).apply_discount half_off

Items.discounted_sales

#=> [5]
```

Note that we're using the shorthand notation to create and execute Lambdas introduced in Ruby 1.9, i.e., `l = -> x{puts "#{x}" }` instead of `l = lambda { |x| puts "#{x}" }` and `l.("foo") #=>foo` instead of `l.call("foo") #=>foo`

Our apply_discount method accepts methods passed as lambdas that themselves accept the instance variable @value set upon initialization. 

We can define however many lambdas for various discounts and pass them to the apply-discount method. 

Kind of cool. 

So, to give us a bit of closure: 

1.) Closures are ways of grouping code to execute later.

2.) We can do a lot with blocks, especially when we understand how YIELD works "under the hood," but we can't pass more than one method, return methods from methods, or store our block for reuse. 

3.) Procs and Lambdas belong to the Proc Class and are Proc Objects, so we can assign variables to them and call methods on them. 

4.) Lambdas check arguments, return like regular methods, and initialize via `lambda{}` vs `Proc.new{}`.

5.) Closures are a big deal because Ruby gives us unrivaled versatility through Blocks, Procs, and Lambdas to abstract our constructs, transpose variables from different scopes, and write more elegant code. 





