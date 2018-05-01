---
layout: post
title:      "(Object) Relationships Can Be Complicated"
date:       2018-04-30 05:54:03 -0400
permalink:  explaining_object-oriented_programming_to_your_mom
---


Often, the best way to confirm that you've grasped an abstract concept is to try explaining it in simplified form. Not only do you gain assurance that you're not just regurgitating the explanation that you learned from, you also--and more importantly-- practice generalizing and re-exemplifying the idea as you solidify your understanding on a higher semantic level. Really grasping something is more than just rehashing instances and some descriptive phrasing you remember. 

So, having made a few steps forward in my journey learning code, I thought it would be a good idea to try explaining Ruby Object Oriented Programming and Object Relationships, very simply, to my mom. My mom once asked me what time Amazon closes. So, again, the emphasis here is on ***simple***. 

**Me:**

In programming, we're faced with two basic categories of stuff: Data (the stuff the program knows and works with) and Behaviors (the stuff the program does and how it does it). We can try and group them together in some way or keep them apart. Functional Programming is a school of thought that says Data and Behavior are separate entities and should be kept apart for clarity. Object-Oriented Programming says we should organize our thinking around entities called Objects which have Data in the form of attributes and Behavior in the form of methods. A flexible language like Ruby supports both approaches, but really shines when we structure our thinking around Objects. 

**Mom:**

So Data is like math stuff and Behavior is like Google?

**Me:**

It can be. Data can be any kind of information that we have access to in our program. If we were making a program that's like a math function, it might be really simple and have numbers, the Data, go into our expression, our Behavior, and come out as different Data on the other end. 

**Mom**

Like x+2 or whatever. Algebra.

**Me:**

Sure. But what about something like animals or professions or whatever. Like how a cow has you know, four legs, larger than dog-- 

**Mom**

--Makes milk and moos...

**Me:**

Yeah. And those are behaviors. So that cow has both attributes, our Data, and these actions it can perform, our Behavior.

**Mom:**

So the cow's the program?

**Me:**

Not quite. And that's kind of where the Functional Programming people are coming from. The cow's an Object. And that Object is starting to blur the line between what our data is and what are behavior is. But it's important to note that Object Oriented doesn't directly mean not Functional or vice-versa. It's just two different approaches to the world of programming that start to veer off in different direction as they tend to embrace Imperative or Declarative Code. 

**Mom:**

Woah. Hold on a second...What the heck is that?

**Me:"

It's like when we say a sentence is Imperative: you're telling someone what to do. Declarative is just saying what you want done. I can say I want this trash bag tied off, transported outside, and to end up in the bin by the street. And it's up to you to do whatever you do to accomplish that. Or I can tell you how to do it. 

**Mom:**

That reminds me... After we're done here, take out the trash...

**Me:**

And if you spelled out how I should do that, that'd be like writing some Imperative code. Let's take a look at a really, really basic example in the Ruby language. 

**Mom:**

Eh...I don't want to look at all that number stuff. 

**Me:**

It's not as much of that as you think. But don't worry about the jargon, let me just walk you through it. 

```
i = 0
while i < array.length do
  puts array[i]
	i+=1
```

Here we're spelling out what's going to happen, not just what we want done. Let's say we've got a list of three things. That letter "i" is just the place: first place, second, or third. We're just detailing exactly what's going to happen. We're going to go place by place, 1st, 2nd, 3rd, and keep going until we get to that last place, and for every place we're going to say or "put" that thing in that place. So for [1,2,3] we're going to say "1" then we'll go to the next position and say "2" and then finally "3."

**Mom:**

Okay...

**Me:**

Yeah, we're really spelling it out. Versus what we'd do with Declarative Code. Again, Ruby can do both. And here's an example of doing the same thing in a different way:

```
array.each do |element|
  puts element
```

**Mom:**

Seems a lot simpler.

**Me:**

Yeah. Because we're not spelling it out. We're just telling Ruby to go do something. It's up to Ruby to implement that something. And what's interesting is that Ruby is so expressive that it's easy to see what it's doing here. If that "array" thing is just the [1,2,3] list from before, it's just "doing" to "each" that "put"-ing thing and saying the number. For each number, it's "put"-ing it. 

**Mom**

So, why bother spelling it out?

**Me:**

Right. You sound a bit like a Functional Programmer who'd say let's focus on results. And there's something to say for a direct approach. At it's root, Functional Programming is just the belief that the output of a Behavior should depend only on it's input. So everything's easy to follow and there's little room for error or unintended side-effects when things get complicated. They usually go with Declarative Code because the emphasis is on clean, crisp, and effective. On getting things done.

**Mom:**

Makes sense. Keep it simple. 

**Me:**

Depends on what you mean by "simple." Once things get complicated, Functional Programming is this really elegant machine where there's a controlled flow of data from input to output, and every little thing's gotta add up perfectly along the way. Like let's think of a Thanksgiving Feast program: ingredients are our input, they flow through a set of processes, and the result is the feast, our output. 

**Mom:**

Seems simple enough...

**Me**

Yeah. Declarative Programming can be really elegant and accomplish a lot more with less code than Imperative Programming. That's what the Functional Programming approach is all about. But what if we think about programming as an effort to represent reality? And what if we're really concerned with being able to describe abstract concepts in our code?

**Mom**

What do you mean? We're just making a recipe method or whatever. It's in one end, out the other, right?

**Me**

That's probably the most efficient way to tell a language like Ruby, "Ingredients are added together into a Thanksgiving Feast." But is that how you you think about it? If you're picturing the process, you're mixing this and that together into something and then working with those new things--"

**Mom:**

--Like the stuffing...

**Me:**

Right. Like the stuffing and the batter. So when we're Functional Programming and we're being all Declarative, we're not really concerned with the stuffing and the batter. We're concerned with the ingredients most efficiently being combined into the feast. But that's not really that intuitive. And that's what Object-Oriented Programming is saying. That when we're thinking about making a Thanksgiving Dinner, we're thinking about ingredients and processes that go together. We're thinking about the batter and the stuffing. And there's no reason those can't be Objects in our programming. When we start talking about a program like a video game or a complex scheduling application with all these users that can do different things, it's a much more intuitive approach. It's easier to represent reality in a video game as these Objects that interact with each other than a really complicated function within a function within a function...

**Mom**

Okay. So this Object-Oriented stuff is easier?

**Me:**

It's easier to see how it models reality. And it's easier to start working with abstract concepts. It's kind of hard to go about describing what a "vehicle" is or an "intern" is with a clean crisp function. Real life is messy. So with Object-Oriented Programming we're giving things, these Objects, Attributes and Behaviors and building out how they interact. 

**Mom:**

Wait...Can't we just define those things. Like can't I just look in the dictionary and know what "vehicle" or "intern" is?

**Me:**

To some extend sure. But let's say we're deciding what gets to drive on roads and we've got a piece of plywood with two wheels attached and a bedsheet as a sail to catch the wind. Is that a "vehicle"?

**Mom:**

It's not a car. But it moves. It's a vehicle. 

**Me:**

These categories can be kind of abstract though. It's not just because it moves, right. Otherwise is a rocket a vehicle? Only if it has people in it? And what about the "intern"? Is an "intern" an "employee?" With an Object-Oriented approach we'd start building out the Attributes and Behavior of our "Intern" Class and, much as in the real world, whether the "Intern" is an "Employee" might change based on the attributes and behaviors of all these different Objects that are interacting to form all this complex behavior. 

**Mom:**

So now it seems like even though it's more like the real world, this approach is really complicated...

**Me:**

Well, it's just shifting the complexity from writing these Attributes and Behaviors to the way in which they interact. With Object-Oriented Programming, we're trying to create simple objects with simple behaviors. The complexity is in how they interact. Which is why, while O.O.P embraces imperative code to really spell things out, it makes full use of mutable variables and states to enable a lot of complex functionality.

**Mom**

What do you mean? Mutable variables and states...

**Me:**

Well, let's put it this way: Functional Programming is going to say that while we're telling the language what to do, not how to do it, we should keep things contained and our functions or methods shouldn't be affected by things outside of them. Take a look at the following two functions, or methods, of incrementing "a" by 1:

```
def increment(a)
      a += 1
end

```

And

```
$a = 0
def increment()
      $a += 1
end
```

**Mom:**

Woah...

**Me:**

Don't worry about the "code" jargon here. Just think about the simple idea that in the first approach everything we need is contained in the function: input determines output. Our second function depends on an input that's outside the function. It's the global variable `$a`, and if it changes then the result of our increment method changes as well. Obviously the second example is kind of silly. But the idea carries over to some really complex code. Functional Programming says that all of these mutable values and states maks it harder to predict the behavior of a program and can have a lot of unintended side effects. An Object-Oriented approach, which overwhelmingly makes use of mutable variables and states, is basically saying it's worth the trade-off. Sure, we might be exposed to more side-effects and sure, our code isn't as perfectly straightforward, direct, and predictable. But we get a lot of flexibility because we get to build pieces of code that we can reuse in other places and it's a lot easier to make changes when you can leverage this network of Object Relationships. 

**Mom:**

What do you mean?

**Me:**

Well, a language like Ruby has things called Classes. They're like blueprints. And we can make a Class, or blueprint, for one thing, like "Animal" and then when we make a blueprint for another thing like "Dog" we can just have the latter Inherit Attributes and Behavior from the former.

**Mom:**

Right. Like "Man" is a "Person," so the those characteristics follow from one to another. 

**Me:**

Or "Car" is a "Vehicle." Or a more practical application for programming, a "Buyer" on an ecommerce site Inherits from "User." And So does "Seller" They both Inherit from "User" So we've got his hierarchy going. That's Inheritance. And it's an example of the result of methods and functions depending on things outside of them. Take the following for example:

```
class Employee
      def paid
			      puts "Get's compensated"
			end
end

class Supervisor < Employee 
      def paid 
			      puts super + " with lots of real money."
			 end
end

class Intern < Employee
      def paid
			      puts super + " with exposure and the lifelong experience of unpaid labor."
			end
end
```

**Mom:** 

Haha. Good One. 

**Me:** 

It's a really simple idea of how these Objects "Supervisor" and "Intern" are inheriting this Behavior "Paid" from the superclass "Employee". And then that can be further modified within the other Class. So this method "Paid" is going to give us different results depending on where in the program it's called. We've got a lot of flexibility. And if we change "Paid" within the superclass "Employee" because Employees are no longer compensated, "Supervisor" and "Intern" Inherit those changes. But no worries, subclasses can always override the methods that were passed down to them. So, if we change "Paid" in "Employee" to say "Does not get compensated," the "Supervisor" class can just add "unless you're the supervisor" to the "Paid" behavior it inherits.

**Mom:**

So, when you have a lot of these hierarchies, you can change things way down the line by changing something up at the top.

**Me:**

Right. Inheritance means an IS-A relationship. Using it can save us from writing a lot of code when we have something like an online listing site with category hierarchies and want to make it easy to implement big changes, like widespread listing requirements. And it's usually a really good relationship to use for simple specializations. Like let's say we've got a high-school scheduling application. And there's a Class for sports game tables. Those specifications might just be tweaked a little depending on the sport. Maybe there's more spaces to populate for basketball games than football games or something. But overall, all those highschool sports table subclasses are inheriting from the sports game table superclass. I'm not sure, though, for our "Intern" example that it's best to use inheritance. 

**Mom:**

What do you mean?

**Me:**

Well. You've got something a little different going on there. Technically, while we're abiding by the fiction that both "Intern" and "Employee" share in getting compensated in some way, it's not really that an "Intern" is just a specialization of "Employee." There's a big difference between how exhibiting the "Paid" Behavior through "Getting compensated with Money" and "Getting compensated with exposure and the lifelong experience of unpaid labor."

**Mom:**

That's for sure...

**Me:**

So, we might be better off making a whole new Class, "Being Compensated", and giving it Behaviors like "Drawing a Salary" or "Just a Psychological Paycheck..."

**Mom:**

--Whatever that means...

**Me:**

Yup... All those different things that we can categorize as "Being Compensated" by any stretch of the imagination can go right into that Class. And then we can kind of side-step this whole relationship between "Employee"  and "Intern," and instead focus on how they access the "Being Compensated" Class.

```
class BeingCompensated
      def drawing_a_salary
			      puts "Here's your paycheck!"
			end
			
			def just_a_psychological_paycheck
			      puts "Good job!"
			end
end

class Supervisor 
      def initialize
			      @compensation = BeingCompensated.new
			end
			
			def compensated 
			      @compensation.drawing_a_salary
			end
end

class Intern
            def initialize
			      @compensation = BeingCompensated.new
			end
			
			def compensated 
			      @compensation.just_a_psychological_paycheck
			end
end

Supervisor.compensated #=> "Here's your Paycheck!"
Intern.compensated #=> "Good Job!"
```

**Mom:**

Okay. So now "Supervisor" and "Intern" are not getting their Behavior from "Employee"?

**Me:**

I mean, it might be appropriate for them to Inherit other Attributes and Behaviors from the "Employee" Class, like maybe parking permission, an email address, login to the servers, whatever. We're just saying that getting a psychological paycheck and getting paid are distinct functonalities. "Intern" HAS-A "Getting Compensated." It's just really distinct from how "Supervisor" HAS-A "Getting Compensated." And we can imagine that may be very different from how "Consultant" or "Managing Partner" HAS-A "Getting Compensated," though it probably involves something a lot more fair.

**Mom:**

So, it's just a different way of looking at it?

**Me:**

Yeah. Kind of. An Object-Oriented approach that uses Imperative Code and Mutable Variables and States is just a way of conceptualizing a programming approach. It's a paradigm that favors putting the functionality in the relationships between otherwise simple Objects and simple Behaviors. When we used Inheritance before, we were passing down Behavior. Here we're referencing other Objects with Composition. Classes "Intern" and "Supervisor" are using "BeingCompensated" to provide some of their functionality. And if somehow the company decides to pay their Interns, they can just go ahead and give "Intern" access to the "drawing_a_salary"  method within the "BeingCompensated" Class. 

**Mom:**

That'll be the day...

**Me:**

Yup. But what we're getting at is that these relationships like Inheritance and Composition are just abstract ways of working with Data and Behavior. Behind all of this there's just information like long lists of numbers and logs...

**Mom:**

--Spreadsheets and stuff...

**Me:**

Data of any kind... and the way that Data can be arranged, rearranged, added to, subtracted from.... Instead of looking at an application that manages say all this information on employees as a bunch of distinct functions for one thing or the other, we can think of a universe of Relationships between Objects with Attributes, Behaviors. 

**Mom:**

"Universe" seems like a bit much...

**Me:**

Okay. Program. And that's a good point. Sometimes it's a bit much in approaching a straightforward problem. Functional Programming can often get things done a lot more efficiently. But when we have these big projects with a lot of people collaborating, Object-Oriented approaches can actually be really practical. 

**Mom:**

Creating "universes" is practical?

**Me:**

Well... Looking at the application in a modular way is practical. If we build out some Objects with Attributes and Behavior in one place, we can use those Object Relationships to extend functionality elsewhere really easily. We can reconfigure our Objects like pieces in a puzzle.

**Mom:**

More like Legos...

**Me:**

Exactly.  By mapping out the complex interactions of our Objects we demonstrate what is actually happening in our code and make it a lot easier to make changes. As programmers, we're always concerned with how easy it will be for our future selves, our coworkers, and anyone who has to work with our program to understand what we wrote. So it helps that our code has high cohesion and low coupling. 

**Mom:**

What? That seems like the same thing. 

**Me:**

Well, we're trying to balance readability and relatedness against things being so dependant that it's hard to make a change. We want our code to be interrelated enough that it's easy to make big changes with minimal effort, but we want to safeguard against unintended consequences or, to put it another way, a web so tangled we don't even know where to begin. So Object-Oriented Programming is a good organization method that compartmentalizes and associates things in a way that's really useful to work with.

**Mom:**

And now it'd be really useful for you to take out the trash. 





