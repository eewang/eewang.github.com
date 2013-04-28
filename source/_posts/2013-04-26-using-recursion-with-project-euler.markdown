---
layout: post
title: "Using Recursion With Project Euler"
date: 2013-04-28 08:00 AM
comments: true
categories: 
published: true
---

I previously set a goal for myself to try and do one Project Euler problem every day. In retrospect, I think that goal was a little ambitious, what with all the coding, reading and blogging that I'm trying to do on a day-to-day basis. However, I was able to work in a problem earlier this week that helped me get a better grasp of using recursion in Ruby.

<!--more-->

For those who don't know, recursion is a computer science concept that seems simple on its face, but can mask considerable complexity. A recursive function is basically a function that calls itself. You know how when you go to a barbershop and your barber puts a mirror up to the back of your head after the haircut? The resulting mirror-on-mirror effect that looks like a never-ending pyramid of the same image in increasingly smaller size is an example of recursion. Applying recursion in a Ruby script can be a powerful and succinct way of moving programmatically applying a pattern to a given problem until the solution is found. 

However, using recursion can also lead to "call stack too deep" errors the same way that using a while loop without providing for some incrementor will lead to an infinite loop. Recursion can also be difficult to track mentally since each call to method is made within the execution of such method, just one "layer" above. This means that when the executing code finally reaches its terminating condition, it has to go "back" through all the previous calls to the same method. At school, we had explored recursion early on in the semester to solve the Tower of Hanoi problem, but I'll admit that it was difficult for me to grasp and follow the logic. Thankfully, as I went through Project Euler problem 7 earlier this week, I was able to apply the principles of recursion to solve it and gain a better understanding of using recursion in the process.

Problem 7 asks you to find the 10,001st prime number. My strategy for solving this problem was to take a starting number, pass in prime index that I would like to solve for, then move iteratively toward that index based on the starting index (i.e. the prime index of the closest prime number less than or equal to the starting number). To do this, I created an Integer#prime? method to check if a number is prime and metaprogrammed an Integer#next_prime and Integer#previous_prime method to find the closest prime number greater than and less than, respectively, the given number. I then created an Integer#find_prime method that would recursively call itself via the Integer#move_to_next_prime method that would eventually return the value of the prime_index that was initally passed to the #find_prime method.

I think the code that I ended up with is okay, but I could have made other changes to make the search process of finding the n-th prime faster. For example, instead of having the script move incrementally to find the value of the n-th prime given a starting number, I could have written it so that based on the delta between the prime index of the starting number and the target prime index, the script could make a someone intelligent guess as to how much to add or subtract to the starting number to get closer to the target value. The difference between the value of the starting prime index of 10 (i.e., the 10th prime) and the 15th prime will be smaller than the difference between the 10th and 20th prime. This fact could be baked into the recursive function in order to find a better "ballpark" estimate of the target prime index value rather than just incrementing by one prime number each time the function is called.

{% codeblock lang:ruby %}
# PROBLEM NUMBER 7

# PROBLEM NAME
# 10001st prime

# PROBLEM DESCRIPTION
# By listing the first six prime numbers: 2, 3, 5, 7, 11, and 13, we can see that the 6th prime is 13.

# What is the 10 001st prime number?

# PROCESS
# => I want to be able to determine a prime number by passing in the index for it within the prime number universe
# => Find the pattern of determining prime numbers
  # => Look at factors for determining a prime number

# ANSWER

# => 104743

require 'pry' 

class Integer

  def prime?
    prime = true
    if self.even?
      prime = self.even_prime_check
    else
      prime = self.odd_prime_check
    end
    prime
  end

  def odd_prime_check
    prime = true
    divisor = 3
    while prime == true && divisor <= (self**0.5).round(0)
      if self % divisor == 0
        prime = false
      else
        divisor += 1
      end
    end
    prime
  end

  def even_prime_check
    self == 2 ? true : false
  end

  def check_for_primes
    primes = []
    (2..self).each do |i|
      if i.prime?
        primes << i
      end
    end
    primes
  end

  def sum_all_primes
    self.check_for_primes.inject(0) { |result, num| result + num }
  end

  def which_prime?
    self.check_for_primes.size
  end

  def self.prime_search(*searches)
    searches.each do |search|
      define_method "#{search}_prime" do
        prime_test = self
        case search
        when :next
          prime_test += 1 if self.prime?
          prime_test += 1 until prime_test.prime?
        when :previous
          prime_test -= 1 if self.prime?
          prime_test -= 1 until prime_test.prime?
        end
        prime_test
      end
    end 
  end

  prime_search :next, :previous

  def find_prime(index)
    if self.prime?
      starting_prime = self
    else
      starting_prime = self.previous_prime
    end
    puts "#{starting_prime.which_prime?} prime: #{starting_prime}"
    starting_index = starting_prime.which_prime?
    move_to_next_prime(starting_prime, starting_index, index)
  end

  def move_to_next_prime(start_prime, start_index, target_index)
    case 
    when start_index == target_index
      start_prime
    when start_index < target_index
      next_num = start_prime.next_prime
      next_num.find_prime(target_index)
    when start_index > target_index
      next_num = start_prime.previous_prime
      next_num.find_prime(target_index)
    end
  end

end

binding.pry
{% endcodeblock %}

As I've mentioned before, working on Project Euler problems has forced me to dust off the Ruby core documentation and practice applying concepts that I learned before but may have forgotten over time. Also, given that we've focused so much on the abstractions of Rails over the past two months, its great to have to go back to working with just Ruby. Its not easy to make time to do Project Euler problems, but I definitely recommend trying to work on at least a few of them (or similar logic problems) just to get more practice at writing Ruby scripts.

Finally, I'll leave you with some other good resources on recursion in Ruby in case you're interested in learning more: 

<a href="http://natashatherobot.com/recursion-factorials-fibonacci-ruby/" target="_blank">Recursion and Fibonacci in Ruby</a>

<a href="http://kaibun.net/blog/2010/03/17/simple-thought-about-recursion-in-ruby/" target="_blank">Recursion in Ruby</a>

