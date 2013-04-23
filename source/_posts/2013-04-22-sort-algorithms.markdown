---
layout: post
title: "Not All Sort Algorithms Are Created Equal"
date: 2013-04-22 11:20
comments: true
categories: 
---

Yesterday we explored a few computer science topics, such as sort algorithms, Big O notation, linked lists and binary search nodes. I was tasked with learning more about sort algorithms, which is something I typically take for granted when coding, since Ruby has a built in #sort method (which, incidentally, uses the quick sort algorithm that I'll go into later). At a high level, a sort algorithm is a set of instructions to follow to sort a collection (e.g., an array) based on one or multiple criterion. 

<!--more-->

A quick <a href="http://en.wikipedia.org/wiki/Sort_algorithms" target="_blank">search</a> on Wikipedia indicates that there are quite a few sort algorithms out there to choose from. Each of them have pros and cons depending on the context in which they are used. Applying a bubble sort algorithm to sort an array of numbers performs differently (in terms of computation speed) depending on how large the array is (e.g., 2 numbers or 2000 numbers?) or how organized the array is (e.g., is it entirely randomized or mostly sorted already?). For large, randomized data sets, an insertion sort may perform better than a bubble sort, but a bubble sort may perform better than an insertion sort for small sets.

Sort algorithms can be categorized among several parameters, including efficiency, stability and computational complexity. Efficiency is a proxy for speed, or how quickly it takes for a given set to be sorted by the algorithm in question. This can vary considerably by the complexity and order of the input set. 

Stability is a concept that considers the original placement of a value within a set. For example, in the array [1, 2, 3, 4, 2], a correctly sorted list will come out to [1, 2, 2, 3, 4]. A sort algorithm is stable if the first '2' in the original array is in the first '2' in the sorted array and the second '2' in the original array is the second '2' in the sorted array. This example of stability is a bit contrived in this example, but is more relevant for sorting a set of objects that have more than one attribute. For example, say I have a list of NBA point guards and various stats (e.g., points per game, assists per game, etc.). If two point guards have the exact same PPG and I run a sort algorithm that does not lead to a stable result, then each time I run that sort algorithm I could have a different order of players, even if by PPG, the list is correctly sorted.

Computational complexity relates to what operations are used to actually sort the list and determine relative size. For example, a comparison sorting algorithm, like a bubble sort or insertion sort, uses relational operators (e.g., <=, >=, !=) to determine where a given value should be placed in a sorted list. Conversely, an adaptive sort uses the existing sort order of a set to determine how the sort performs, with the basic understanding that the better sorted an initial input is, the faster the sort algorithm should perform.

Here's a quick description on the sort algorithms that seemed to come up most frequently in my research on the topic. I tried to understand the most common search algorithms and implement them in Ruby. I also benchmarked the performance of the algorithms in question for an already sorted array, a reverse sorted array and a randomized array. Benchmarking the performance of the sort algorithms was helpful for me to better understand when you might want to use a bubble sort over a selection sort. You can see my full code below (note that the code is far from perfect - if you have any comments on anything that I may have missed or done inefficiently, please let me know!).

<strong>Bubble Sort</strong>

To sort an array of numbers (the simplest example of a set) a bubble sort algorithm goes through each index in the array and compares the value with the following value. If the first value is larger than the second value, then the two values will be switched (assuming the sort is done in ascending order). This pattern is repeated until all the numbers are sorted. On the first iteration, the largest number will "bubble" to the end of the array (i.e., index of n-1). On the second iteration, the second largest number will "bubble" to the n-2 position in the array. And so on until the array is sorted.

The bubble sort algorithm performs decently well on small sets, but performance degrades as sets get larger. The downside of the bubble sort is that small numbers toward the end of a set will slow down the sort as the algorithm has to move them one-by-one all the way to the front of the set. A modified version of the bubble sort is the comb sort, which helps address the  small number issue that the bubble sort presents.

{% codeblock lang:ruby %}
class Array
  # ...
  def bubble_sort
    length = self.size 
    until self.sorted?
      # puts self.join("")
      for i in 0..length-2 do 
        compare = [self[i], self[i+1]]
        if compare[0] > compare[1]
          switch_num = self[i]
          self[i] = self[i + 1]
          self[i + 1] = switch_num
        end
      end
    end
    self
  end
  # ...
end
{% endcodeblock %}

The bubble sort takes an array and goes through each value, switching that value with the next value if the next value is smaller than the first value. Each time the sort goes through the array, it checks if the array is sorted. If it is, then it stops, otherwise it continues to sort the array.

{% codeblock lang:ruby %}

pry(main)> 58927384.array.bubble_sort

58927384
58273849
52738489
25374889
23547889

=> [2, 3, 4, 5, 7, 8, 8, 9]

{% endcodeblock %}

<strong>Selection Sort</strong>

A selection sort starts by taking the first element in a list of numbers. Then, the algorithm goes through all of the remaining numbers and finds the minimum value, which it then swaps positions with the first number. After this first iteration, the smallest number in the array is now in the correct (i.e., first) position. This process continues until n - 1 iterations have been made and all numbers are sorted.

{% codeblock lang:ruby %}
class Array
  # ...
  def selection_sort
    length = self.size
    sorted = self
    for i in 0..length-1 do 
      # puts sorted.join("")
      search_array = sorted[i..-1]
      current_num = sorted[i]
      min_num = search_array.min
      switch_index = i + search_array.index(min_num)
      sorted[i] = min_num
      sorted[switch_index] = current_num
    end
    sorted
  end
  # ...
end
{% endcodeblock %}

This sort algorithm will go through an array and for each value it checks, it will find the minimum value for the sub-set of the array following the value it is checking. It will then replace the current value with the minimum value that it finds. This way, the smallest number of a successively smaller sub-set of the original array is placed just before such sub-set. By the time the loop gets to the end of the array, the array has been sorted.

{% codeblock lang:ruby %}

pry(main)> 58927384.array.selection_sort

58927384
28957384
23957884
23457889
23457889
23457889
23457889
23457889

=> [2, 3, 4, 5, 7, 8, 8, 9]

{% endcodeblock %}

<strong>Insertion Sort</strong>

An insertion sort starts by taking the last two numbers in an array. The algorithm then takes each number in the original array and puts that number in the correct place in the new sorted array, and continues that pattern until all of the original array is now in a sorted new array.

{% codeblock lang:ruby %}
class Array
  # ...
  def insertion_sort
    length = self.size
    sorted = []
    sorted.unshift(self[length-2..length-1].max).unshift(self[length-2..length-1].min)
    for i in 0...length-2 do
      # puts sorted.join("")
      num = self[i]
      insert_at = sorted.max < num ? sorted.length : sorted.collect { |int| int - num }.index { |x| x >= 0 }
      sorted.insert(insert_at, num)
    end
    sorted
  end
  # ...
end
{% endcodeblock %}

This sort code creates a second array of two numbers, then goes back through the original array and puts each number in the correct place in the second array. The "insert_at" variable determines where in the second array the value from the original array should be included.

{% codeblock lang:ruby %}

pry(main)> 58927384.array.insertion_sort

48
458
4588
45889
245889
2457889

=> [2, 3, 4, 5, 7, 8, 8, 9]

{% endcodeblock %}

<strong>Merge Sort</strong>

Merge sort is an algorithm that recursively breaks down a set until each item is in its own array. Then, the algorithm recreates the set by merging successively larger sub-sets in an orderly fashion until the original set is sorted. This was a challenging one for me to conceptualize, and I'll admit that the code below was mostly taken from the Phil Crissman blog I link to below. The speed of the merge sort comes from the fact that its easier to a sorted list with another already sorted list. The algorithm breaks down a large array until it can reconstruct the set by merging sorted sub-sets.

<strong>Quick Sort</strong>

I didn't implement a quick sort algorithm, but the basic notion behind the quick sort algorithm is that it selects a element in the set called the pivot (typically, the median value), moves all values less than the pivot to before the pivot and all values greater than the pivot to after the pivot. After this first iteration, the pivot value is in the correct place in the set, even if the values before and after the pivot are not in the correct order. Then, this process is repeated for the sub-sets smaller and larger than the pivot value until the entire set is sorted.

The quick sort seems to be a pretty fast and efficient algorithm, and its used in Ruby for the array#sort algorithm. However, it doesn't seem to be the most stable algorithm, as the original set order is not preserved in the event of a perfect match between two values. Conceptually, I find this sort algorithm the most interesting, because it uses contextual clues to sort more efficiently. The Wikipedia entry for <a href="https://en.wikipedia.org/wiki/Quicksort" target="_blank">quick sort</a> has a great graphic illustrating how the quick sort works. I might try and implement my own quick sort at some point; if I do, I'll definitely update this post!

<strong>Benchmarking</strong>

For each of these tests, I created a random number and converted that number into an array using the number#array function in the code at the bottom. For the quick sort, I used the Ruby array#sort method. I wanted to see how the sort algorithms I chose to examine varied based on the size of the array and the sort level of the input array. I tested each algorithm on an already sorted array, a reverse sorted array and a randomly sorted array.

Let me just say upfront that I think the merge sort implementation seems off here. Based on my reading, the merge sort should be a relatively efficient and fast sort algorithm (falling below a quick sort but above the other sort algorithms), but for some reason the merge sort I examined doesn't perform that well. I suspect that its because the method is making an unnecessary number of recursive loops. It would be interesting to examine more closely, but I'm afraid that this may be a rabbit hole that I don't have the expertise (or time) yet to delve fully into.

{% codeblock %}
number = 58927384

Sort Algorithm (input type)     Sort Time (ms)
----------------------------------------------
Bubble Sort (sorted)            0.013
Insertion Sort (sorted)         0.035
Selection Sort (sorted)         0.038
Merge Sort (sorted)             0.093
Quick Sort (sorted)             0.011
  
Bubble Sort (reverse)           0.055
Insertion Sort (reverse)        0.028
Selection Sort (reverse)        0.027
Merge Sort (reverse)            0.066
Quick Sort (reverse)            0.007
  
Bubble Sort (random)            0.045
Insertion Sort (random)         0.027
Selection Sort (random)         0.027
Merge Sort (random)             0.067
Quick Sort (random)             0.006

{% endcodeblock %}

{% codeblock %}
number = 52398471342

Sort Algorithm (input type)     Sort Time (ms)
----------------------------------------------
Bubble Sort (sorted)            0.012
Insertion Sort (sorted)         0.041
Selection Sort (sorted)         0.038
Merge Sort (sorted)             0.105
Quick Sort (sorted)             0.008
  
Bubble Sort (reverse)           0.094
Insertion Sort (reverse)        0.041
Selection Sort (reverse)        0.035
Merge Sort (reverse)            0.095
Quick Sort (reverse)            0.007
  
Bubble Sort (random)            0.083
Insertion Sort (random)         0.04
Selection Sort (random)         0.034
Merge Sort (random)             0.096
Quick Sort (random)             0.005
{% endcodeblock %}

For relatively small arrays, there doesn't seem to be a huge difference in performance, but its clear that the quick sort is the most efficient method, which should come as no surprise since its the actual Ruby array#sort method. My sort algorithm implementations are no match for the Ruby sort algorithm.

{% codeblock %}
number = 2984579834759824759082347509823475098274509827409582745980274359872490
8572890457209384572908345790284570981374908274598027450923845092846738945729834
579873465934758923745897459083745890273490587246897234985739485634596394

Sort Algorithm (input type)     Sort Time (ms)
----------------------------------------------
Bubble Sort (sorted)            0.075
Insertion Sort (sorted)         5.182
Selection Sort (sorted)         5.079
Merge Sort (sorted)             5.528
Quick Sort (sorted)             0.01
  
Bubble Sort (reverse)           22.589
Insertion Sort (reverse)        3.836
Selection Sort (reverse)        1.37
Merge Sort (reverse)            11.13
Quick Sort (reverse)            0.014
  
Bubble Sort (random)            17.069
Insertion Sort (random)         4.37
Selection Sort (random)         1.344
Merge Sort (random)             8.258
Quick Sort (random)             0.007

{% endcodeblock %}

For larger sets, the efficiency loss becomes much more evident for the non quick sort algorithms. The bubble sort, for example, performs very well for an already sorted list, but falls way behind for lists that are not well sorted (randomized or in reverse sort order). The insertion and selection sorts seem to perform relatively similarly, although it appears that the selection sort seems to be consistely better than the insertion sort. This further supports the notion that which sort algorithm is "best" is context-dependent and varies based on the complexity and size of the set to be sorted. That said, there are certain sort algorithms that are generally worse than others (e.g., the bubble sort is probably sub-optimal in most all cases).

The main lesson I learned throughout trying to implement my own sort algorithms is to not try and implement my own sort algorithms. Ruby seems to have the sort method down pretty well, although I've read that there are some edge cases where the array#sort method lags behind custom sort algorithms. At this point in my programming career though, I can probably rely on array#sort for most all of my sorting needs.

Needless to say though, implementing your own sort algorithm is a pretty interesting intellectual exercise. It forces you to use less abstract Ruby methods (I tried to just use for loops in my implementation), which helps you develop a better understanding and appreciation for more abstract Ruby methods like array#collect or array#inject. 

<strong>Good Resources:</strong>

Here are a few good web resources about sorting algorithms. I actually found the Wikipedia entry about sort algorithms to be remarkably helpful, as well as a few of the links included within the page.

<a href="https://en.wikipedia.org/wiki/Sort_algorithm" target="_blank">https://en.wikipedia.org/wiki/Sort_algorithm</a>

<a href="http://www.sorting-algorithms.com/" target="_blank" >http://www.sorting-algorithms.com/</a>

<a href="http://atschool.eduweb.co.uk/mbaker/sorts.html" target="_blank">http://atschool.eduweb.co.uk/mbaker/sorts.html</a>

<a href="http://blog.8thlight.com/will-warner/2013/03/26/stable-sorting-in-ruby.html" target="_blank">http://blog.8thlight.com/will-warner/2013/03/26/stable-sorting-in-ruby.html</a>

<a href="http://philcrissman.com/2010/07/18/how-not-to-write-sorting-algorithms-in-ruby/" target="_blank">http://philcrissman.com/2010/07/18/how-not-to-write-sorting-algorithms-in-ruby/</a>

***

{% codeblock lang:ruby %}
require 'pry'
require 'benchmark'

class Fixnum

  def array
    array = self.to_s.split("").collect { |i| i.to_i }
    array
  end

end

class Bignum

  def array
    array = self.to_s.split("").collect { |i| i.to_i }
    array
  end

end

class Array

  def reverse_array
    reversed_array = []
    size = self.length
    (0..size-1).each do |i|
      reversed_array << self[size - 1 - i]
    end
    reversed_array
  end

  def sorted?
    sorted = true
    i = 0
    while sorted == true && i < (self.size-1)
      if self[i] > self[i+1]
        sorted = false
      end
      i += 1
    end
    sorted
  end

  def bubble_sort
    length = self.size 
    until self.sorted?
      # puts self.join("")
      for i in 0..length-2 do 
        compare = [self[i], self[i+1]]
        if compare[0] > compare[1]
          switch_num = self[i]
          self[i] = self[i + 1]
          self[i + 1] = switch_num
        end
      end
    end
    self
  end

  def selection_sort
    length = self.size
    sorted = self
    for i in 0..length-1 do 
      # puts sorted.join("")
      search_array = sorted[i..-1]
      current_num = sorted[i]
      min_num = search_array.min
      switch_index = i + search_array.index(min_num)
      sorted[i] = min_num
      sorted[switch_index] = current_num
    end
    sorted
  end

  def insertion_sort
    length = self.size
    sorted = []
    sorted.unshift(self[length-2..length-1].max).unshift(self[length-2..length-1].min)
    for i in 0...length-2 do
      # puts sorted.join("")
      num = self[i]
      insert_at = sorted.max < num ? sorted.length : sorted.collect { |int| int - num }.index { |x| x >= 0 }
      sorted.insert(insert_at, num)
    end
    sorted
  end

  def merge_sort(first, last)
    # puts "starting merge_sort"
    if first < last
      mid = (first + last) / 2
      # puts "starting first recursive loop: self: #{self}; first: #{first}; mid: #{mid}; last: #{last};"
      self.merge_sort(first, mid)
      # puts "starting second recursive loop: self: #{self}; first: #{first}, mid: #{mid+1}, last: #{last}"
      self.merge_sort(mid + 1, last)
      # puts "start merge: self: #{self}; first: #{first}; mid: #{mid + 1}; last: #{last}"
      self.merge(first, mid + 1, last)
      # puts "done"
    end
    # puts self[0..-2].join("")
  end

  def quick_sort
  end

  def sort_benchmark
    sorted_order = self.insertion_sort
    sorted_reverse_order = self.insertion_sort.reverse_array
    random_order = self.shuffle

    Benchmark.bm(100) do |x|
      x.report("Bubble Sort (sorted):") { sorted_order.bubble_sort }
      x.report("Insertion Sort (sorted):") { sorted_order.insertion_sort }
      x.report("Selection Sort (sorted):") { sorted_order.selection_sort }
      x.report("Merge Sort (sorted):") { sorted_order.merge_sort(0, self.size) }

      x.report("Bubble Sort (reverse):") { sorted_reverse_order.bubble_sort }
      x.report("Insertion Sort (reverse):") { sorted_reverse_order.insertion_sort }
      x.report("Selection Sort (reverse):") { sorted_reverse_order.selection_sort }
      x.report("Merge Sort (reverse):") { sorted_reverse_order.merge_sort(0, self.size) }

      x.report("Bubble Sort (random):") { random_order.bubble_sort }
      x.report("Insertion Sort (random):") { random_order.insertion_sort }
      x.report("Selection Sort (random):") { random_order.selection_sort }
      x.report("Merge Sort (random):") { random_order.merge_sort(0, self.size) }
    end
  end

  protected

  def merge(first, mid, last)
    left = self[first..mid - 1]
    right = self[mid..last]
    left.push(self.max + 1)
    right.push(self.max + 1)
    i = 0
    j = 0
    # puts "MERGE (before) - self: #{self}; left: #{left}; right: #{right}"
    (first..last).each do |n|
      if left[i] <= right[j] 
        self[n] = left[i]
        i += 1
      else
        self[n] = right[j]
        j += 1
      end
    end
    # puts "MERGE (after) - self: #{self}; left: #{left}; right: #{right}"
    # self[0..-2]
  end
end

binding.pry

{% endcodeblock %}

