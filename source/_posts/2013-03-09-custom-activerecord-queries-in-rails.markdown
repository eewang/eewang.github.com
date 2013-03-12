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

