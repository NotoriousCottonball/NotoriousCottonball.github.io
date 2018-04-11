---
layout: post
title:      "Balancing Ruby Syntax: Concise, Expressive, and Flexible"
date:       2018-04-11 11:41:52 -0400
permalink:  balancing_ruby_syntax_concise_expressive_and_flexible
---


Completing the Intro to Ruby Module of the Flat Iron Web Dev Course got me thinking about the balancing act inherent in writing good code. Ruby is a language that lends itself well to short-cuts and clever ways of getting things done. Ruby is also a very expressive language. In Ruby, everything is an object and the language serves to express the intention of the code and not just the implementation of the data and procedure. Finally, Ruby is intuitive and flexible, allowing us to break down the behavior of our programs into the smallest possible constituents and rearranging those simple building blocks to meet new goals and problems as they arise. Ruby showcases the three pillars of great code: Concise (direct and without wasted space), Expressive (good readibility and demonstrates intent), and Flexible (easily altered, reconfigured, and redefined). These three dimensions often converge in examples of well-written code. They are also sometimes at odds and require careful "give-and-take" optimization. 

Concise code cuts to the heart of the matter and wastes little space. Directness and brevity are virtues of clearly written code with focused intent. However, when compactness becomes obfuscation, a program's efficiency can get in the way of its readibility. There's a difference between clean code and cramped code. Take the Ruby ternary operator, for example. At its best, the ternary operator tightens things up, cutting through syntactic noise and delivering direct expression:
```
guest_name == "Bob" ? "Hello, Bob!" : "I'm sorry, what is your name again?"
```

At its worst, overuse of the ternary operator can lead to major problems with readibility and conveying intent:
```
int == 2 ? "Prime Number" : int % 2 == 0 ? "Not Prime Number" : int == 3 ? "Prime Number" : int % 3 == 0 ? "Not Prime Number" : "May Be A Prime Number"
```

Very Confusing. Putting Parentheses around each nested ternary operator expression helps illustrate the intent:
```
(int == 2 ? "Prime Number" : (int % 2 == 0 ? "Not Prime Number" : (int == 3 ? "Prime Number" : (int % 3 == 0 ? "Not Prime Number" : "May Be A Prime Number"))))
```
But, it's pretty obvious this is not a great approach. Readibility is awful. Syntax is confusing. The choice of nesting ternary operators, while covering a lot of ground in very little space, does little to communicate the intent of the programmer, nor is it very easy to go in and edit the expression. In short, this code says a lot but expresses very little. While compact, it is not expressive, nor is it flexible. 

In real life, code is read more than it is written. That is, it's important for a professional developer to understand code written by others and to ensure their contributions are, in turn, understood by other collaborators. Well-written code is not only efficient and comprehensive, but also clearly communicates intent. Great code is expressive. Take for example, the eloquence and clarity of defining an email class that takes three arguments in Ruby:
```
class Email
   def initialize(subject, date, headers)
   end
end
```
This code is concise, expressive, and easy to work with. If, in the interests of compactness, we were to abbreviate the names of the arguments accepted (i.e., "sub." for "subject), we'd lose immediate readibility. If, in the interests of expressiveness, we expanded the argument names to subject_string & date_string, we'd be adding a lot of noise. If, in the interestes of flexibility, we changed the argument names to var_1, var_2, & var_3, we'd be gaining very little by indicating a wider range of accepted arguments in exchange for losing immediate clarity.  This is a great example of how favoring expressiveness in Ruby code often brings with it an acceptable level of brevity and flexibility. 

However, Ruby's expressiveness can sometimes cause problems. Ruby allows for "english" operators `and`, `or`, and `not`. They seem like just another bit of sweet syntactic sugar, but their behavior can be a good deal more sour than anticipated. While they seem like more expressive substitutes for operators `&&`, `||`, and `!`, they carry lower levels of precedence and their use is often problematic and imprecise. 
```
x = true
y = false
truthiness = x and y
```
You'd think truthiness would equal false. After all, we know y equals false and for truthiness to equal true, both x and y have to equal true. 
Except that's not the case. It turns out that `=` has greater precedence than `and`, so truthiness has the value true. Parentheses make the point clearer:
```
(truthiness = x) and y
```
While the use of parentheses works to get the correct value, so does the use of the higher precedence `&&` operator. The latter is also more concise, and the code is a great deal more flexible without depending on parentheses. 

The ability to redefine and rearrange constituents of a program is the key to adaptable code. Well-written code, in addition to being concise and expressive, must be well-suited to face new circumstances. Great code is not afraid of change. Luckily, Ruby's flexibility allows for a wide range of options to accomplish routine tasks. In Ruby, you can just go ahead and do:
```
10.times do
puts "Here We Go..."
end
```
Just like that, you can print something to the screen ten times. You don't have to store the number 10 in a variable to do anything with it, like you would in JavaScript. In Ruby everything is an object. You can just dive right in. 

Sometimes, though, that flexibility can be scary. Consider:
```
class Fixnum
    def +(other)
	  self - other
    end
end

puts 5 + 3

#=> 2
```
Redefining the plus operator with such ease is certainly flexible. Maybe too flexible. 

Ruby lends itself to abstraction, and most of the time abstraction is a good thing. It allows our code to capture very precisely our intent on a higher, and more widely applicable, semantic level. Flexibility through abstraction often makes for better code. But, as usual, it can get out of hand. 

While every programmer strives to write perfectly abstracted code, the danger in single-mindedly pursuing abstraction is  that it becomes that much harder to find problems when they inevitably happen. It's not necessarily too much abstraction that's the problem. It's bad abstration: the kind of prematurely generalized code that anticipates way too much need for flexibility while sacrificing major readibility. The cost of abstraction is indirection. Fundamental to object-oriented design is the tension between concrete code that's easy to understand and abstract code that's easy to change.

Writing a FizzBuzz program in Ruby illustrates the importance of finding a suitable level of abstraction for the task at hand and the danger in prematurely generalizing. A basic #fizzbuzz method might look like this:
```
def fizzbuzz(i)
   if i % 3 == 0 && i % 5 == 0
    puts "FizzBuzz"
  elsif i % 3 == 0 
    puts "Fizz"
  elsif i % 5 == 0 
    puts "Buzz"
  end
end
```
In the interests of further abstraction, we might want to encapsulate some of the data to reduce repetition and create a more modular method with the use of a case statement:
```
def fizzbuzz(i)
  fizz = (i % 3 == 0)
  buzz = (i % 5 == 0)
  puts case
            when fizz && buzz then "FizzBuzz"
            when fizz then "Fizz"
           when buzz then "Buzz"
          end
end
```
So far so good. Not only does the code look cleaner and more expressive, but it's a lot more flexible. If we want, we can add to the `case` statement or replace `puts` with another method or easily redefine the variables `fizz` and `buzz`. 

In the interests of abstraction, we could try to generalize `fizz` and `buzz` by creating a #`divisible_by?` method and then encapsulating the "Fizz", "Buzz", and "FizzBuzz" strings by expressing them as combinations of constants:
```
def divisible_by?(num, den)
   num % den == 0
end
def fizzbuzz(i)
   F = "Fizz"
   B = "Buzz"
   fizz = divisible_by?(i, 3)
   buzz = divisible_by?(i, 5)
   puts case
          when fizz && buzz then F + B
					when fizz then F
					when buzz then B
					end
end
```
While this version of code is technically more abstract, it's not much more of an improvement. Without a good reason for generalizing with our creation of the # `divisible_by?` method, the `F` variable, and the `B` variable, we somewhat obfuscated the code and did extra work for no reason in particular. We didn't actually gain that much flexibility. I guess if we wanted to change the way in which we test for divisibility by 3 or 5, we've got that covered. We've also nixed our reliance on the modulo operator `%`, though we had to define a new method to do it. 

It's pretty obvious that if we continue this trajectory of purposeless abstraction we'll end up with some obscure and ugly looking code. If our goal is to create the most generalized and fundamentally encapsulated nugget of an abstracted # `fizzbuzz` method, then maybe we're willing to write less expressive and concise code. Absent an overriding reason for the single-minded pursuit of ultimate abstraction, however, we should aim for balance. 

Ruby is a powerful, descriptive, and adaptable language that illuminates the full potential of well-written code. Of course, with a plethora of shortcuts come many pitfalls. Ruby makes complex functionality accessible and uncovers the fundamental tension between the three major dimensions of great code. Successful programmers have to be concise, expressive, and flexible. Moreover, they must know when to compromise in favor of the quality most suited to the purpose at hand.




