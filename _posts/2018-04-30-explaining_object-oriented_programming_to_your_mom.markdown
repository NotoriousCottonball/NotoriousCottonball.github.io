---
layout: post
title:      "(Object) Relationships are Complicated"
date:       2018-04-30 05:54:03 -0400
permalink:  explaining_object-oriented_programming_to_your_mom
---


Quite often, the best way to confirm that you've grasped an abstract concept is to try explaining it in simplified form. Not only do you gain assurance that you're not just regurgitating the explanation that you learned from, you also--and more importantly-- practice generalizing and re-exemplifying the idea as you solidify your understanding on a higher semantic level. Really grasping something is more than just rehashing instances and some descriptive phrasing you remember. 

So, having made a few steps forward in my journey learning code, I thought it would be a good idea to try explaining Ruby Object Relationships, very simply, to my mom. I figured that the basic idea behind Inheritance and Composition was so fundamental that I better have a firm conceptual understanding before diving into the nitty gritty. The emphasis, especially given that my mom isn't exactly computer literate, is on ***simple***.

In programming, we're faced with two basic categories: Data(the stuff the program knows and works with) and Behaviors(the stuff the program does and how it does it). When faced with these fundamental categories, we can try and bring them together in some way or keep them apart. Functional programming says that data and behavior are separate entities and should be kept apart for clarity. Object-Oriented Programming says we should organize our thinking around entities called "objects," which have data in the form of attributes and behavior in the form of methods. A flexible language like Ruby supports both approaches, but really shines when we structure our thinking around "objects." A proponent of OOP would say that when we "encapsulate" or bind together data and methods and we make use of the relationships these bundles ( "objects") have to each other-- the "object relationships" we're going to get to in a minute--we're better off because we can easily manage and manipulate data. 

It's worth mentioning that a FP (Functional Programming) proponent would counter by playing up the higher level of abstraction--a kind of mathematical level--that leaves little room for error. And there's definitely something to say for that. That and how Functional Programming approaches problems in a very direct way. Really a lot of the differences between OOP and FP are more the way they conventionally are practiced. That is, the defining characteristic of Functional Programming is simply that the output of a method or behavor, as we're calling it, should only depend on the input, or arguments passed to that function. So, really Functional Programming is saying we should avoid changing states and mutable data. Is it possible to approach something with Object Oriented Programming and avoid changing states and mutable data? Yes. But the way it's practiced, especially with Ruby, makes use of those things to maximize the flexibility and reuse of the code. We build these "objects" so we can rearrange and reuse them in different way. 

In some ways that can overcomplicate, get out of hand, and muddy the waters. So, a Functional Programming advocate would point to the basic idea of Functional Programming: that output depends solely on input. Like this simple example:

```
def increment(a)
      a += 1
end

```

Compare that to the following:

```
$a = 0
def increment()
      $a += 1
end
```

Don't worry about the "code" jargon here. Just think about the simple idea that in the first approach everything we need is contained in the function: input determines output. Our second function depends on an input that's outside the function. It's the global variable `$a`, and if it changes then the result of our increment method changes as well. The first is a really, really simple example of declarative programming, using expressions and declarations to evaluate a function that returns the same outputs when given the same input, no matter what's going on outside of it. The second, again a really really simple example, is a form of imperative programming, where our function depends on what happens outside of it. A change in the state of `$a` will produce a different result for `def increment()`. 

Obviously the second example is kind of silly. But the idea carries over to some really complex code. Functional Programming says that all of these mutable values and states maks it harder to predict the behavior of a program and can have a lot of unintended side effects. An Object-Oriented approach (which, again, can techically still follow functional programming, but overwhelming makes use of mutable variables and states in an imperative mode) is basically saying it's worth the trade-off. Sure, we might be exposed to more side-effects and sure, our code isn't as perfectly straightforward, direct, and predictable. But there's something to be said about approaching our problem solving by defining objects and their interactions.  

When we start creating a "universe"--well, maybe just our humble "program"-- it feels a lot more natural to built entities and think about how they interact. The Functional Programmer is concerned with getting things done. The Object-Oriented Programmer is concerned with mapping out how things work. Think about a complicated recipe for a Thanksgiving dinner. A Functional approach is all about managing the flow of ingredients through all those processes, e.g., whisking, mixing, baking, and efficiently getting from the input of ingredients to the output of the feast. An Object-Oriented approach is all about the different classes of things that are interacting. Some of our ingredients (Data) combine with certain processes to form new things like batter or stuffing ("Objects") and those in turn behave in certain ways with each other. When we map out what "batter" or "stuffing" is and how it interacts, we can apply that in other programs or even in a different way within our recipe program. The Functional approach of modeling the flow of data in-and-out of the recipe function is probably the most direct, efficient, and side-effect free approach. But say you set about building a program that handles all kinds of recipes, and those combinations that form objects keep popping up in different places. It'd be really helpful to start mapping out those objects so you can make use of all the resusable pieces. 

That's really what Object-Oriented Programming is all about. It's a way to more accurately represent real world concepts through the relationships between groupings of data and behavior.  It's hard to describe what a "vehicle" is through Functional Programming. But with OOP, we can start building out the relationships to other "objects" like "car," "sedan," and "engine," as well as behaviors like "locomotion" or "combustion." It's a programming paradigm well suited to working with abstract concepts. 

Object-Oriented Programming also has practical utility in its modular approach. We're encapsulating data and behavior in reusable units. To really get the most out of that approach, we embrace the mutable variables and states of imperative programming. What we end up with is bundles of behavior that respond and communicate through "messages." The complexity isn't in some recursive, function-within-function declaration like in Functional Programming. The goal of OOP is to create simple objects with simple behaviors. The complexity is in how they interact. 

Which brings us to Object Relationships. Utilizing the major relationships of Inheritance and Composition helps make our code more maintainable. By mapping out the complex interactions of our "objects" we demonstrate what is actually happening in our code and make it a lot easier to make changes. As programmers, we're always concerned with how easy it will be for our future selves, our coworkers, and anyone who has to work with our program to understand what we wrote. So it helps that our code has high cohesion and low coupling. We're trying to balance readability and relatedness against things being so dependant that it's hard to make a change. We want our code to be interrelated enough that it's easy to make big changes with minimal effort, but we want to safeguard against unintended consequences or, to put it another way, a web so tangled we don't even know where to begin.

Inheritance and Composition help us write coherent OOP code. Like we said before, our goal is to write simple "objects" and simple behaviors. We get the complex functionality we need from how they interact. 


