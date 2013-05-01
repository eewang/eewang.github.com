---
layout: post
title: "Metaprogramming Ruby and Rails Antipatterns (Part 1 of 2)"
date: 2013-04-29 15:03
comments: true
categories: 
published: false
---

I recently finished reading two programming books in parallel - Metaprogramming Ruby and Rails Antipatterns. As Ruby/Rails books go, the two could not be further apart. Metaprogramming Ruby focuses on explaining the Ruby object model and how core Ruby is constructed in order to enable you as a Ruby programmer to unlock functionality that isn't immediately obvious. Conversely, Rails Antipatterns is more about focusing on the bad coding habits most new Rails developers experience and simple, tangible ways to avoid those  pitfalls. One book explains core Ruby (although also touches some on Rails), while the other applies specifically to Rails. One book goes as deep as possible into Ruby's construction as a language, while the other aims to hit developers in their day-to-day programming habits. Arguably, Rails Antipatterns is more "practical" and Metaprogramming Ruby is more "theoretical," as its generally easier to find more daily code applications of Rails Antipatterns than Metaprogramming Ruby. This post will focus on Metaprogramming Ruby and the next will focus on Rails Antipatterns.

<!--more-->

Reading the two books in tandem, however, has forced me to move quickly between Rails and Ruby. Its oftentimes easy to forget that Rails is just a Ruby library at its core and is constructed using many of the metaprogramming techniques described in Metaprogramming Ruby. While the two books are pretty different in their construction, they've helped me better understand how programming works generally along all levels of abstraction - from the Ruby core object model to the abstract class macros provided in Rails. In many ways, Metaprogramming Ruby picks up where Rails Antipatterns ends up. Reading the latter might inspire you to begin using the <a href="http://apidock.com/rails/Module/delegate" target="_blank">"delegate ... to"</a> pattern in your Rails applications as a way to avoid long method chains, but Antipatterns won't go into the nitty gritty of how Rails implements that class macro. Metaprogramming, however, will go pretty deep into how a macro like "delegate ... to" is implemented and how it uses dynamic method definition to create methods in the class in which the "delegate" method is called.

Metaprogramming is a great way to act on the mantra that good developers take complexity and deliver simplicity. A simple definition for metaprogramming is code that writes code. An application that actively uses metaprogramming seems simple on the outside (like magic!) but is quite complex on the inside. Arguably the best example for this in the Ruby community is the eponymous Ruby on Rails. Rails presents simplicity to users; for example, whenever you create model and corresponding table and call a finder method (e.g., #find_by_name()), these finder methods are generated dynamically by Rails. The internal source code for these finder methods use liberal doses of metaprogramming and thus are tough to decipher, but the benefit of designing the application in that way means that users can build quickly using a huge library of "free" methods provided by Rails (specifically ActiveRecord in this example) without thinking too much about the method implementation.

I would definitely recommend reading Metaprogramming Ruby if you feel like you have a decent grasp of the language and would like to accelerate your learning. If you feel like you don't fully understand the concepts the first time you read it, don't worry, just read it again. Then read it a third time. Metaprogramming Ruby is one of those books that you could read it multiple times and keep learning new things from it each time, since the material is dense and the opportunities to apply the concepts are not always obvious in every Ruby script or application. Here's a quote from the book that I think explains the value of metaprogramming and tradeoffs to consider when applying the technique.

<blockquote>
If you use it sparingly, metaprogramming can make your code more readable for humans—but it will probably make it less readable for programs such as refactoring engines or code analysis tools. Ruby’s extremely dynamic nature makes life very hard for such tools, and that’s why some IDE features that work great for static languages (such as finding all the calls to a method, renaming a variable, or jumping from a method usage to its definition) are difficult to implement well in Ruby. Add metaprogramming to the mix, and your poor IDE will be even more confused. That’s one of the fundamental trade-offs of metaprogramming (and, to a point, of dynamic languages). You have the freedom to write expressive, terse code, but to read that code, you need a human brain.
</blockquote>

I try to explain some of the topics included in Metaprogramming Ruby below, but its difficult to take each of these independent of the other. The most valuable part of the book for me was the in-depth explanation of the overall structure of the Ruby language and how objects and methods work. I'm someone who likes to have an understanding of the broad, high-level context in which I work, and Metaprogramming Ruby provides that context so that whenever I'm looking at a piece of code, I feel like I have a better understanding now of what's going on (e.g., What variables are in scope? What is 'self'? What methods can be accessed?).

<strong>Procs vs. Lambdas</strong>

At a high level, procs and lambdas define blocks of code that can be executed when called. They are somewhat analogous to anonymous functions in Javascript. Procs and lambdas are generally used for the same purpose, but they have subtle differences between them:

1) Procs are more flexible when evaluating arguments vs. lambdas. For example, if you define 2 arguments for a proc and only call the proc with 1, the other will be considered nil, whereas for a lambda, the call will return an ArgumentError

2) The return value for a proc comes from the scope in which the proc itself was defined, whereas the return value for a lambda comes from the lambda block.

Lambdas tend to be the first choice for Rubyists because they're more similar to methods in that they are strict about the number of methods they accept (the 'arity' of a method) and exit when return is called. Both lambdas and Procs are part of the Proc class.

<strong>Dynamic Methods</strong>

Dynamic methods are a set of methods that can create or call methods when the actual name of the method is not known. I've listed out a few of the methods used to either create or call methods dynamically and apply them to the following example:

{% codeblock lang:ruby %}

class Dog

  def initialize(name, toy)
    @name = name
    @favorite_toy = toy
  end

  def self.colors(*args)
    args.each do |arg|
      define_method arg do
        "I am #{arg.to_s}"
      end
    end
  end

  def method_missing(method_call)
    puts "Sorry, we don't recognize the color #{method_call}"
  end

  colors(:green, :blue, :red)

  private

  def private_method
    "You shouldn't be able to see me!"
  end

end

fido = Dog.new("Fido", "Ball")

{% endcodeblock %}

<em>#send</em><br>

The #send method is useful for whenever you want to call a method dynamically and are not sure of the method name at runtime. In the example above, a user can input a color among the three available options, and the #send method will figure out which method to call in the Dog class. Another use case for #send is to call private methods:

{% codeblock lang:ruby %}
puts "What color do you want your dog?"
color = gets.chomp # => blue
puts fido.send(color) # => I am blue
{% endcodeblock %}

{% codeblock lang:ruby %}
puts fido.send("private_method") # => "You shouldn't be able to see me!"
{% endcodeblock %}

<em>#define_method and #define_singleton_method</em><br>

These two methods are used to define instance methods and class methods, respectively. They are methods that, when executed, create other methods that can then be called on either an instance or class object. In the code above, I call a class method called "colors" at runtime. This class method takes in an arbitrary number of arguments (in this instance, 3 colors) and creates a method for each argument. The methods that get created then return the name of the method. Admittedly, this example is a bit contrived as the same behavior could be achieved by just passing a color argument into a method that returns the value of the color. However, using #define_method and #define_singleton_method can help keep code DRY if you find a certain pattern used repeatedly in a number of different methods in your code. For me, I've used #define_method and #define_singleton_method whenever I notice that I'm repeating code in methods where the only difference is a reference to another object or column. 

<em>#method_missing</em><br>

"#method_missing" acts as a catch-all method for intercepting method calls that an object doesn't know how to interpret. If a method is called on an object that doesn't know how to respond to the method after Ruby goes through the method lookup chain (i.e., first going into the immediate class, then upward into modules, parent classes, etc.), Ruby will call the #method_missing method that's part of the Kernal module and mixed into every Ruby object. By creating a #method_missing method, you can hijack Ruby's call stack and redefine what gets executed or returned whenever an unknown method is called on an object.

In my example, I used #method_missing to serve as a catch-all for any colors that are not recognized by the Dog class. However, #method_missing can be used to define attributes dynamically:

{% codeblock lang:ruby %}

class Dog

  def initialize
    @attributes = {}
  end

  def method_missing(name, *args)
    attribute = name.to_s
    if attribute =~ /=$/
      @attributes[attribute.chop] = args[0]
    else
      @attributes[attribute]
    end
  end

end

{% endcodeblock %}

This allows me to create any attribute of a dog that I want on the fly and #method_missing will create both a setter and getter for the method. This is possible because in the below example, "fido.legs = 4" is equivalent to fido.legs=(4), where "legs=" is the name of the method called on "fido" and "4" is the sole argument. When this method is first called, the Dog class doesn't know how to respond to the method since there is no method called "legs". Thus, the method call is directed to #missing_method, which then sets the attribute to the name of the method and sets the value to the first argument that is passed in. 

{% codeblock lang:ruby %}

fido = Dog.new("Fido", "Ball")

fido.legs = 4
fido.legs # => 4

fido.bark = "BARK BARK"
fido.bark # => "BARK BARK"

{% endcodeblock %}

<strong>Binding Module</strong>

One of my favorite gems in the Ruby universe is the <a href="https://github.com/pry/pry" target="_blank">Pry</a> gem. Pry is a debugging tool that allows you to enter into the local universe when code is run. Including "binding.pry" in a set of code will pry open the code at runtime and enable you to poke around and see how certain variables are defined or used. Its probably the gem I use the most - whenever I'm writing code, be it a full-blown Rails app or a simple Ruby script, Pry tends to be the first gem I include.

But even though I use the gem quite often, I didn't exactly realize how it worked. Metaprogramming Ruby explains that "binding" is actually a method in Ruby that converts a scope into an object that can then be called at a later moment. This can be an incredible tool, as the use of the "binding" method in a block of code makes that scope portable for future use, even to the point where "self" can change based on the binding context.

{% codeblock lang:ruby %}
class Dog

  def initialize(name, toy)
    @name = name
    @favorite_toy = toy
  end

  def get_binding
    return binding
  end

end

fido = Dog.new("Fido", "Ball")
fido_scope = fido.get_binding
snuffles = Dog.new("Snuffles", "Stick")
snuffles_scope = snuffles.get_binding

eval("puts @name; puts @favorite_toy", fido_scope) # => Fido Ball
eval("puts @name; puts @favorite_toy", snuffles_scope) # => Snuffles Stick
{% endcodeblock %}

Using the "binding" is helpful for accessing a scope in an object-oriented manner. The binding method is defined within the Kernal module, which is mixed into Ruby's Object class, from which all objects inherit. This is why you can use the binding method in any class, since all classes inherit the Kernal module functionality. Pry essentially adds functionality to the binding method and literally prys open the scope and creates an IRB-like environment out of a scope, which makes testing and debugging considerably easier than if there was not a binding method.

<strong>Flat Scope</strong>

Flat scope is used to enable the use of variables and methods defined outside of a traditional scope gate (e.g., a method definition, class or module). Variables defined outside of a block can be accessed within a block, so a nifty technique is to define a class via a block rather than a using the "class" keyword, as in the following:

{% codeblock lang:ruby %}
dog = "Fido"

Dog = Class.new do

  define_method "name" do
    puts dog
  end

end

myDog = Dog.new
myDog.name # => Fido
{% endcodeblock %}

This is in contrast to the "normal" way of defining a class, which acts as a scope gate and prevents previously defined variables from being accessed within the class scope.

{% codeblock lang:ruby %}
dog = "Fido"

class Dog

  def name
    puts dog
  end

end

myDog = Dog.new
myDog.name # => ERROR: undefined local variable or method "name"
{% endcodeblock %}

<strong>Using alias_method</strong>

The #alias method is a helpful way of changing method names while still maintaining the functionality of the old method name. It can also be used to modify programmer behavior; for example, #alias is used for deprecating functionality by providing warnings and advice on what other method to use instead of the called method, while still allowing users to call the old method. 

{% codeblock lang:ruby %}
class Array
  alias :size :length

  def length 
    puts "Please use #size to determine array element count from now on."
    self.size
  end 

end
{% endcodeblock %}

<strong>Metaprogramming in Rails</strong>

Rails makes heavy use of metaprogramming techniques to provide a huge amount of functionality out of the box whenever a new application is created. For example, one of the benefits of using Rails and ActiveRecord specifically is that ActiveRecord creates an array of attribute accessors and dynamic finder methods that match the columns in the database tables. If you have a User model that includes columns for name, date_of_birth and phone_number, ActiveRecord will create methods like User#find_by_name, User#find_all_by_date_of_birth and User#find_or_create_by_phone_number dynamically. How does ActiveRecord know what finder methods to create? Through metaprogramming, and specifically through the #method_missing method.

Whenever you first make a call to a dynamic finder method in Rails, #method_missing will get triggered. If you look at the source code for ActiveRecord::Base, you'll find a class method called #method_missing, which hijacks the method call chain for Ruby and create finder methods if the method you are calling follows the find_by pattern. The finder methods are based on the table columns in the database. In the first call to a finder method, #method_missing will create the necessary finder methods in your application, so that the second time you call that finder method, it will execute an actual block of code that corresponds with that method rather than make a call to a ghost method within #method_missing. This pattern seems to be a theme within the construction of Rails - active use of #method_missing and ghost methods to create the potential for a lot of functionality, but to not create that functionality until its actually called as adding all possible functionality at runtime could slow down the application. Rails wants to make sure that you're going to use a dynamic finder method before it actually adds the code to your codebase. Thus, the first call to a dynamic finder generates the actual method via #method_missing. 

Its interesting to think about the design decisions that went into the construction of ActiveRecord. The first call to a dynamic finder method is to a ghost method within #method_missing. However, this first call is enough for Rails to infer that you probably want to use the ghost method again. At this point, Rails creates an actual method for you to use in the future. Accessing a real method is generally faster than accessing a ghost method. However, adding a ton of real methods can also make your application bloated, especially if you never use most of them. Knowing some more about metaprogramming has made me appreciate even more the construction of Rails and how it enables rapid application development.

I particularly like this excerpt from Metaprogramming Ruby, as I think it explains well design decision for ActiveRecord as it relates to creating dynamic methods.

<blockquote>
If you look at the current optimized source code [for Rails], you can try to guess some of the motivations behind it. Calling a real method is faster than calling a Ghost Method, so Rails chooses to define real methods to access attributes. On the other hand, defining a method also takes time, so Rails doesn’t do that until it’s sure that you really want to access at least one attribute on your ActiveRecord objects. Also, define_ method() is known to be slower than def on some Ruby implementations (but not on others!), and that might be one reason why the authors of Rails opted to use Strings of Code to define accessors.

None of these considerations is obvious, however. As a rule, you should never optimize your code prematurely—whether it’s metaprogramming code or not.
The source code behind dynamic attributes proves that metaprogramming can either impair your system’s performance or help you optimize it. For example, if you find out that you’re slowing down your system by calling too many Ghost Methods, you can get some performance back by defining Dynamic Methods. So, when it comes to metaprogramming, performance generally is no more of an issue than it is with any other code.
</blockquote>

Overall, I'd recommend cracking open Metaprogramming Ruby if you have a decent grasp of Ruby and want to really delve into the inner workings. It's probably not the most practical book, in that you may not use many of the metaprogramming recipes on a day-to-day basis, but its great for providing an overall understanding of how the Ruby method lookup chain works, how different classes and modules interact, etc. That said, its a dense book, and it took me a while to get through. I'll likely try and read it again at some point, as it feels like one of those books where you can read over and over again and keep discovering new and interesting nuggets of wisdom.