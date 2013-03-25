---
layout: post
title: "TRUE Object-Oriented Design"
date: 2013-03-09 11:23
comments: true
categories: 
published: false
---

I've started to think of good design like a mountain range. The most abstract layers are the most foundational to the application - the base of the mountain, if you will - whereas the least abstract layers are the items that you actually see - the peaks. Each peak might represent a different component of your application, yet the base of the mountain represents the shared functionality or code patterns between the peaks. In looking at a mountain range from afar, no one can perceive the geological complexity of the sandstone or granite (or whatever else mountains are made of) that allow the peaks to reach the sky. In the same way, good application design hides the complex inner workings of a program, yet it is this complexity that enables the public interface of an well-built application to work so seamlessly. 

Admittedly, the mountain range metaphor is a bit contrived, but the same effective design pattern can be seen in other spheres, both natural and artificial. A rainforest's beauty is seen from the treetops, but the complexity deep within the roots and vines that create an interlocking and co-dependent ecosystem and enable the treetops to extend high above the earth is hidden from the external observer. A well-functioning corporation requires harmony between its many internal groups in order to provide a service or product to its customers effectively. Software design just takes these common patterns of loose coupling, modularity and composition and applies them in the context of objects, classes and methods.

In "Practical Object Oriented Design in Ruby", Sandi Metz expresses these design patterns in a pithy and effective manner, complete with code examples, comparisons of good and bad design patterns and a common theme of designing a bike shop application that weaves the 9 chapters together. POODR is an eminently readable book, and Ms. Metz does a remarkable job balancing deep dives into code with plain English explanations of good application design. As an aside, Ms. Metz's day job is as an application developer for my alma mater, Duke University, which makes her all the more awesome.

<strong>"Code should be transparent, reasonable, usable and exemplary."</strong>

<strong>"Depend on things that change less often than you do."</strong>

"A dependency on a private method of an external framework is a form of technical debt. Avoid these dependencies."

"create public methods that allow senders to get what they want without knowing how your class implements its behavior."

"[Law of] Demeter is often paraphrased as “only talk to your immediate neighbors” or “use only one dot.”"
  - Ties into the use of join tables in Rails - using song.genres rather than song.song_genres.genres

p. 80 - level of abstraction vs. likelihood of change

Depend on behavior, not data (p. 49)

"If objects were human and could describe their own relationships, in Figure 4.5 Trip would be telling Mechanic: “I know what I want and I know how you do it;” in Figure 4.6: “I know what I want and I know what you do” and in Figure 4.7: “I know what I want and I trust you to do your part.”
This blind trust is a keystone of object-oriented design. It allows objects to collaborate without binding themselves to context and is necessary in any application that expects to grow and change."

"This tension between the costs of concretion and the costs of abstraction is fun- damental to object-oriented design. Concrete code is easy to understand but costly to extend. Abstract code may initially seem more obscure but, once understood, is far easier to change. Use of a duck type moves your code along the scale from more concrete to more abstract, making the code easier to extend but casting a veil over the underlying class of the duck."

"The ability to tolerate ambiguity about the class of an object is the hallmark of a confident designer. Once you begin to treat your objects as if they are defined by their behavior rather than by their class, you enter into a new realm of expressive
flexible design."

"This code contains an if statement that checks an attribute that holds the category of self to determine what message to send to self. This should bring back memories of a pattern discussed in the previous chapter on duck typing, where you saw an if statement that checked the class of an object to determine what message to send to that object.
In both of these patterns an object decides what message to send based on a cate- gory of the receiver. You can think of the class of an object as merely a specific case of an attribute that holds the category of self ; considered this way, these patterns are the same. In each case if the sender could talk it would be saying “I know who you are and because of that I know what you do.” This knowledge is a dependency that raises the cost of change.
Be on the lookout for this pattern. While sometimes innocent and occasionally defensible, its presence might be exposing a costly flaw in your design. Chapter 5, Reducing Costs with Duck Typing, used this pattern to discover a missing duck type; here the pattern indicates a missing subtype, better known as a subclass."" - p. 136

These are the rules of inheritance; break them at your peril. For inheritance to work, two things must always be true. First, the objects that you are modeling must truly have a generalization–specialization relationship. Second, you must use the correct coding techniques. - p.142

A lot of being a more experienced developer seems to revolve around a spidey sense about coding design patterns. Avi demonstrates this regularly when he'll spend just a few minutes flying between levels of abstraction that take us beginners hours to implement ourselves. In fact, experience in most any profession or skillset can be reflected in the "sixth sense" someone has about recognizing patterns. An experienced trader can front-run the market because he or she "senses" that prices will move in a certain direction. A branding expert can quickly conceptualize a marketing campaign because he or she can immediately grasp the underlying nature of the message a client wants to convey. In the same way, experience as a developer means that you get better at spotting patterns in code. POODR aims to provide beginners with a jumpstart in finding those patterns.

P 123, 128,134 pood

"The general rule for refactoring into a new inheritance hierarchy is to arrange code so that you can promote abstractions rather than demote concretions." p. 148

"Explicitly stating that subclasses are required to implement a message provides useful documentation for those who can be relied upon to read it and useful error messages for those who cannot." - p. 153

"Instead of allowing subclasses to know the algorithm and requiring that they send super, superclasses can instead send hook messages, ones that exist solely to provide subclasses a place to con- tribute information by implementing matching methods. This strategy removes knowledge of the algorithm from the subclass and returns control to the superclass." - p. 134

"Inheritance - is-a
Modules/Duck Types - behaves-like
Composition - has-a" - p. 209


