---
layout: post
title: "Custom ActiveRecord Queries in Rails"
date: 2013-03-09 11:23
comments: true
categories: 
published: false
---

I've started to think of good design like a rainforest. The most abstract layers are the most foundational to the application - the roots, if you will - whereas the least abstract layers are the items that you actually see - the treetops.

Sandi Metz, "Practical Object Oriented Design in Ruby"

<strong>Notable Quotes:</strong>

"Depend on things that change less often than you do."

"A dependency on a private method of an external framework is a form of technical debt. Avoid these dependencies."

"create public methods that allow senders to get what they want without knowing how your class implements its behavior."

"Code should be transparent, reasonable, usable and exemplary."

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

These are the rules of inheritance; break them at your peril. For inheritance to work, two things must always be true. First, the objects that you are modeling must truly have a generalization–specialization relationship. Second, you must use the cor- rect coding techniques. - p.142

A lot of being a more experienced developer seems to revolve around a spidey sense about coding design patterns. Avi demonstrates this regularly when he'll spend just a few minutes flying between levels of abstraction that take us beginners hours to implement ourselves. In fact, experience in most any profession or skillset can be reflected in the "sixth sense" someone has about recognizing patterns. An experienced trader can front-run the market because he or she "senses" that prices will move in a certain direction. A branding expert can quickly conceptualize a marketing campaign because he or she can immediately grasp the underlying nature of the message a client wants to convey. In the same way, experience as a developer means that you get better at spotting patterns in code. POODR aims to provide beginners with a jumpstart in finding those patterns.
