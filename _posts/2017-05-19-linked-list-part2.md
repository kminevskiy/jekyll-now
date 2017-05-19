---
layout: post
title: Linked lists in Ruby, part 2
categories: ruby adt linked-list
excerpt_separator: <!--excerpt-->
---

In my previous article, we implemented a linked list data structure with some very basic operations. While those operations are useful in many cases, they won't take us very far. That's is why in this (and subsequent) post we'll talk about more advanced actions we can perform on our linked lists.
<!--excerpt-->

### Reverse a linked list

![Linked list structure]({{ site.url }}/images/ll2.png)

Let's say we have a linked list with elements 1,2,3,4. As you remember, each element points to the next one while the last one points to nil.

Now, how can we reverse a linked list? Let's take a look at one of the iterative solutions:

### Iterative reversal

{% highlight ruby %}
# class definition skipped for brevity

def reverse
  node = head
  previous = nil

  while node
    next_node = node.next
    node.next = previous
    previous = node
    node = next_node
  end

  self.head = previous
end

# For simplicity sake, I will represent the result as an array

list # [1, 2, 3, 4]
list.reverse # [4, 3, 2, 1]
{% endhighlight %}

We have lots of pointers and temporary variables pointing back and forth! Let's figure out what happens on each iteration.

Iteration 1:

We are not at the end of our list (node is [1, 2, 3, 4]). Continue!

{% highlight ruby %}
next_node = [2, 3, 4]
node.next = nil
previous = [1]
node = [2, 3, 4]
{% endhighlight %}

Iteration 2

We are not at the end of our list (node is [2, 3, 4]). Continue!

{% highlight ruby %}
next_node = [3, 4]
node.next = [1]
previous = [2, 1]
node = [3, 4]
{% endhighlight %}

Iteration 3

We are not at the end of our list (node is [3, 4]). Continue!

{% highlight ruby %}
next_node = [4]
node.next = [2, 1]
previous = [3, 2, 1]
node = [4]
{% endhighlight %}

Iteration 4

We are not at the end of our list (node is [4]). Continue!

{% highlight ruby %}
next_node = nil
node.next = [3, 2, 1]
previous = [4, 3, 2, 1]
node = nil
{% endhighlight %}

Iteration 5

We are at the end of our list (node is nil). Break!

We then point `head` to`previous` (which we used to store our reversed list) and it's done!

### Recursive reversal

So now you understand the iterative approach to this problem. How about a recursive one?

It is useful to understand how the call stack works. Please take a look at the following [article](minevskiy.com/call-stack) for details.

In this example, let's assume that `head` points to the linked list with values 1, 2, 3, 4.

{% highlight ruby %}
def reverse(node = head, previous = nil)
  if !node
    return self.head = previous
  else
    next_node = node.next
    node.next = previous
    reverse(next_node, node)
  end
end
{% endhighlight %}

Again, let's decipher what happens on each recursive call to `reverse` method. To make it more transparent, I'll show the arguments of each new recursive call.

Call 1

We are not at the end of our list (node is [1, 2, 3, 4]). Continue!

{% highlight ruby %}
next_node = [2, 3, 4]
node.next = nil
reverse([2, 3, 4], [1])
{% endhighlight %}

Since our `previous` node is set to nil by default, we cut off everything AFTER our list's first element => [1].

Call 2

We are not at the end of our list (node is [2, 3, 4]). Continue!

{% highlight ruby %}
next_node = [3, 4]
node.next = [1]
reverse([3, 4], [2, 1])
{% endhighlight %}

Inside this call, we slice (shift one step to the right) our list and assign it to `next_node`. Then we take our initial (for this call) list ([2, 3, 4]) and set its `next` element to `previous` ([1]). As a result, the second argument for the next recursive call becomes [2, 1]

Call 3

We are not at the end of our list (node is [3, 4]). Continue!

{% highlight ruby %}
next_node = [4]
node.next = [2, 1]
reverse([4], [3, 2, 1])
{% endhighlight %}

The description is the same:

{% highlight ruby %}
initial list - [3, 4]
next_node - [4]
node.next - [2, 1]
node - [3, 2, 1]
{% endhighlight %}

Call 4

We are not at the end of our list (node is [4]). Continue!

{% highlight ruby %}
next_node = nil
node.next = [3, 2, 1]
reverse_rec(nil, [4, 3, 2, 1])
{% endhighlight %}

The description is the same:

{% highlight ruby %}
initial list - [4]
next_node - nil
node.next - [3, 2, 1]
node - [4, 3, 2, 1]
{% endhighlight %}

Call 5

We are at th end of our list (node is nil). Break!

Since our current node is now nil, we'll set the head of our initial linked list to `previous`, which is our list in reverse!
