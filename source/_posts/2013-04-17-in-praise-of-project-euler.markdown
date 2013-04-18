---
layout: post
title: "In Praise of Project Euler"
date: 2013-04-17 16:39
comments: true
categories: 
published: true
---

On the suggestion of my Flatiron classmates <a href="http://chickenriceplatter.github.io/" target="_blank">David</a> and <a href="http://andrewcallahan.github.io" target="_blank">Andrew</a>, I started trying out <a href="http://projecteuler.net/" target="_blank">Project Euler</a> recently. For the uninitiated, Project Euler is a series of math problems aptly named after the famed mathemetician Leonhard Euler, known for such learned things like fluid dynamics, number theory and calculus. Project Euler is effectively a database of math problems that are intended to be solved using code (any language can be used; I'm using Ruby). The questions test your logical thinking skills, and while they're named after a mathemetician, the actual math necessary is more along the lines of high school math (e.g., prime numbers, factoring, etc.). This is not to diminish the difficulty of these problems - some of them are quite challenging - but rather to distinguish between the problem solving skills, which Project Euler helps build, versus pure math skill, which is more the province of a graduate school program.

<!--more-->

I've only completed about 5 problems, but I'm already an advocate of trying them out. I like Project Euler because the problems are challenging enough where they really force you to build a clear, logical problem solving process yet not so obscure that you need to break out your AP Calculus book. Don't be turned away from the problems because of the math - the biggest challenge is taking the large problem and breaking it down small-step-by-small-step.

Here's an example problem: 

{% codeblock %}
If we list all the natural numbers below 10 that are multiples of 3 or 5, 
we get 3, 5, 6 and 9. The sum of these multiples is 23. 
Find the sum of all the multiples of 3 or 5 below 1000.
{% endcodeblock %}

As you can see, the problems are challenging but manageable. The prompt typically has an example pattern and then asks you to extrapolate that pattern to some large number (in this case, 1000). Here are the main reasons why I think its great to try a few Project Euler problems:

<strong>1) Great mental exercise</strong>

I'm trying to average a problem a day as a form of mental exercise. While doing Project Euler problems probably won't directly make you a better Rails programmer, it will boost your overall problem solving process. And what else is coding at its core but implementing a clear process to solving problems via technology? Just as going for a 6-mile run today may not keep you from getting a cold tomorrow, doing Project Euler problems won't help you solve that nasty bug in your code today, but having a consistent habit of problem solving exercise will make your mind healthier in the long run.

<strong>2) Learning core Ruby</strong>

Having to solve Project Euler problems has forced me back into the core Ruby documentation to try and find creative solutions. For example, I don't typically use Ruby's 'inject' method in my day-to-day coding. Inject is a pretty high-level Ruby function that allows you to specify an initial value and apply a code block to an Enumerable (e.g., an array), the result of which becomes the initial value in the following iteration. Most of the code I've written rely on the less abstract 'collect' or 'map' methods in Ruby. However, I've rediscovered the utility of the inject method since Project Euler problems tend to include some sort of iterative sum or product. I won't go into the details of the inject method here, but you can find more information about it in <a href="http://blog.jayfields.com/2008/03/ruby-inject.html" target="_blank">this blog post</a> if you're interested.

<strong>3) Refactoring practice</strong>

Refactoring is the near-constant process of changing code without affecting what the user sees. Its the middle part of the "make it work, make it right, make it fast" adage. Recognizing patterns and understanding the tradeoffs included in applying certain methods or approaching a problem in a certain way are critical to skillful refactoring. Project Euler problems really help hammer in the practice of always looking over your code and finding ways to refactor it. 

I've started taking a three-pronged approach to solving Project Euler problems. First, I'll try and solve the problem for a smaller number (e.g., less than 100) with whatever code works. Second, I'll solve the problem for the large number stated in the Project Euler prompt. Finally, I'll refactor the code into an object-oriented manner, either by creating my own class or monkey-patching an existing Ruby class (often, the Integer class). This three-step process has helped me practice raw problem-solving and get into the habit of breaking down complex problems into small, simple tasks. Refactoring is a universal coding skill, so I'm hopeful that Project Euler will help me develop a better sense for patterns and make me a better problem solver.

<strong>4) Creative problem solving</strong>

This reason falls in line with the refactoring practice Project Euler provides. As the cliche goes, there's often more than one way to skin a cat, and problem solving is no different. An example of this is the code below (warning: spoiler alert) that I wrote to solve Project Euler problem 8. The problem provides a 1000-digit number and you are required to find the greatest product of any five consecutive digits. 

When I was thinking about this problem, I could see a few solutions. One way it could be done would be to go through the number in 5-digit chunks, basically creating sub-arrays out of the number, storing the value into a products array, then returning the maximum value. Another way to solve the problem would be to find the product of 5-digit chunks, store the result in a variable, then replace the value of that variable with subsequent products only if such product is larger. Yet another way to solve the problem would be to calculate the product of a 5-digit chunk, then iteratively check whether the next number is larger than the first number in the 5-digit array (i.e., the next product calculation). If so, then calculate the product. Otherwise, move onto the next number to add. 

I think this last process is the most efficient, because each product that is actually calculated is guaranteed to be larger than the previous max product, since all else equal, replacing one number in a 5-digit product with a larger number will result in a larger number. This saves computation time and moves the determination of whether to calculate the product to a less computationally intensive task (comparing two numbers versus multiplication. At least, I think this is the case. I'm not super well versed in algorithm efficiency, but this makes sense to me at least. 

The code I ended up implementing takes the most from the middle process. I could have extended the code further to make it more efficient, but I focused instead on making the code more object-oriented. My below code shows the three-step process I'm taking toward these problems - first, make it work with a smaller number, then make it work with the actual number, then refactor into an object.

{% codeblock lang:ruby %}
# PROBLEM NUMBER 8

# PROBLEM NAME
# Largest product in a series

# PROBLEM DESCRIPTION

# Find the greatest product of five consecutive digits in the 1000-digit number.
# 73167176531330624919225119674426574742355349194934
# 96983520312774506326239578318016984801869478851843
# 85861560789112949495459501737958331952853208805511
# 12540698747158523863050715693290963295227443043557
# 66896648950445244523161731856403098711121722383113
# 62229893423380308135336276614282806444486645238749
# 30358907296290491560440772390713810515859307960866
# 70172427121883998797908792274921901699720888093776
# 65727333001053367881220235421809751254540594752243
# 52584907711670556013604839586446706324415722155397
# 53697817977846174064955149290862569321978468622482
# 83972241375657056057490261407972968652414535100474
# 82166370484403199890008895243450658541227588666881
# 16427171479924442928230863465674813919123162824586
# 17866458359124566529476545682848912883142607690042
# 24219022671055626321111109370544217506941658960408
# 07198403850962455444362981230987879927244284909188
# 84580156166097919133875499200524063689912560717606
# 05886116467109405077541002256983155200055935729725
# 71636269561882670428252483600823257530420752963450

# PROCESS
# => Convert Fixnum to String to Array
# => Go through first five numbers and find the product
# => Save the result in a "result" variable
# => Remove the first number (1) (unshift) and add the 6th number (push)
# => Find the product of the second set of 5 numbers
# => If the result is larger than the value of "result", replace the result value. Otherwise, discard the latest result
# => Repeat steps 3 to 5 until the end

# => With each number that is removed and added, check if the number being added is greater than the number being removed. If so, calculate the product.

# ANSWER

# => 40824

require 'pry'

# => METHOD 1

# num = 4938294829384

# num_array = num.to_s.split("").collect { |i| i.to_i }
# iterations = num_array.length - 5

# start = 0
# finish = 4
# product_array = []
# max_product = 0

# (0..iterations).each do |i|
#   sub_array = (start..finish).collect { |n| num_array[n] }
#   product = sub_array.inject(1) { |result, num| result * num }
#   product_array << product
#   if product > max_product
#     max_product = product
#   end
#   start += 1
#   finish += 1
# end

# => METHOD 2

# num = "73167176531330624919225119674426574742355349194934
# 96983520312774506326239578318016984801869478851843
# 85861560789112949495459501737958331952853208805511
# 12540698747158523863050715693290963295227443043557
# 66896648950445244523161731856403098711121722383113
# 62229893423380308135336276614282806444486645238749
# 30358907296290491560440772390713810515859307960866
# 70172427121883998797908792274921901699720888093776
# 65727333001053367881220235421809751254540594752243
# 52584907711670556013604839586446706324415722155397
# 53697817977846174064955149290862569321978468622482
# 83972241375657056057490261407972968652414535100474
# 82166370484403199890008895243450658541227588666881
# 16427171479924442928230863465674813919123162824586
# 17866458359124566529476545682848912883142607690042
# 24219022671055626321111109370544217506941658960408
# 07198403850962455444362981230987879927244284909188
# 84580156166097919133875499200524063689912560717606
# 05886116467109405077541002256983155200055935729725
# 71636269561882670428252483600823257530420752963450"

# num_array = num.split("").collect { |i| i.to_i }
# iterations = num_array.length - 5

# start = 0
# finish = 4
# product_array = []
# max_product = 0

# (0..iterations).each do |i|
#   sub_array = (start..finish).collect { |n| num_array[n] }
#   product = sub_array.inject(1) { |result, num| result * num }
#   product_array << product
#   if product > max_product
#     max_product = product
#   end
#   start += 1
#   finish += 1
# end

# => METHOD 3 (Object Orientation)

num = "73167176531330624919225119674426574742355349194934
96983520312774506326239578318016984801869478851843
85861560789112949495459501737958331952853208805511
12540698747158523863050715693290963295227443043557
66896648950445244523161731856403098711121722383113
62229893423380308135336276614282806444486645238749
30358907296290491560440772390713810515859307960866
70172427121883998797908792274921901699720888093776
65727333001053367881220235421809751254540594752243
52584907711670556013604839586446706324415722155397
53697817977846174064955149290862569321978468622482
83972241375657056057490261407972968652414535100474
82166370484403199890008895243450658541227588666881
16427171479924442928230863465674813919123162824586
17866458359124566529476545682848912883142607690042
24219022671055626321111109370544217506941658960408
07198403850962455444362981230987879927244284909188
84580156166097919133875499200524063689912560717606
05886116467109405077541002256983155200055935729725
71636269561882670428252483600823257530420752963450"

class Product
  attr_accessor :number, :set, :start, :finish, :max_product, :product_array

  def initialize(set)
    @set = set
    @start = 0
    @finish = set - 1
    @max_product = 0
    @product_array = []
  end

  def create_array
    self.number.split("").collect { |i| i.to_i }
  end

  def iteration_count
    self.create_array.length - self.set
  end

  def find_max_product
    (0..self.iteration_count).each do |i|
      sub_array = (self.start..self.finish).collect { |n| self.create_array[n] }
      product = sub_array.inject(1) { |result, num| result * num }
      self.max_product = product if self.max_product_compare(product)
      self.start += 1
      self.finish += 1
    end
    max_product
  end

  def max_product_compare(num)
    true if num > self.max_product
  end

end

p = Product.new(5)
p.number = num
p.find_max_product

binding.pry
{% endcodeblock %}

As I mentioned above, don't be intimidated by the math parts of Project Euler. In retrospect, I wish I had started doing these problems earlier on in the semester, but its never too late to get some good mental exercise in.
