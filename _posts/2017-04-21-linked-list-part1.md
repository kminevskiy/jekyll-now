---
layout: post
title: Linked lists in Ruby, part 1
categories: ruby adt linked-list
excerpt_separator: <!--excerpt-->
---

I've seen quite a few people thinking that linked lists are very tricky to understand and then to perform operations on them. What about data and pointer components? Why do we even need this data structure? As with many things, it might be scary to dive into an unknown concept. In order to understand more advanced variants of linked lists (like stacks and queues, which we'll talk about in our future articles), we'll have to take a look at the basics of their parent - please welcome a linked list!
<!--excerpt-->

### Linked list basics

What's the definition of a linked list? It's an abstract, dynamic data structure that consists of linked nodes. Each node's payload includes actual data (anything you can think of really) and a pointer to the next node. In this article we'll look at singly-linked lists.

![Linked list structure]({{ site.url }}/images/ll.png)

Unlike arrays, the linked list has no "connections" between each element other than pointers. and unless we maintain these links at all times, there is no way to get to these "disconnected" nodes. Since we don't maintain references to these nodes in our program, they get garbage collected by the Ruby interpreter.

OK, how about some actual code?

{% highlight ruby %}
class Node
  attr_accessor :value, :next

  def initialize(value)
    @value = value
  end
end

class LinkedList
  attr_accessor :head

  def initialize(value)
    @head = value.is_a?(Node) ? value : Node.new(value)
  end
end

node1 = Node.new(1)
linked_list = LinkedList.new(node1)
{% endhighlight %}

Let's take a look at each class.

First, we define our `Node` class. Each node has 2 attributes - its `value` (actual "thing" you want to store inside this node) and a pointer (this one will reference the `next` node). Initially, each newly initialized node points to `nil` (nowhere). Why? That's because we don't know if we may want to add any new nodes in the future.
Our linked list class has 1 public attribute and that is its `head`. Don't think about this particular attribute as of something special - it simply represents our current list of nodes. In our case, it's a list of only 1 node.

Linked list with one node? Strange and probably useless, so let's add some more nodes. For that we'll have to define a new instance method on the `LinkedList` class:

{% highlight ruby %}
# previous code skipped for brevity

def unshift(value)
  new_node = Node.new(value)
  new_node.next = head
  self.head = new_node
end
{% endhighlight%}

Inside our `unshift` instance method we create a new node. Then we point our new node's `next` pointer to the `head` element, that is, to the previous list of linked nodes. We then redefine our `head` as the one which includes the new node.
PIC

This is all it takes to add a new node at the front of our linked list. This operation has several advantages over the same operation performed on array:

1. Ruby doesn't have to re-index your linked list every time you add a new element to its front. This is not a major concern if we just playing in irb and unshifting small arrays. But what if our array has 1 million elements in it? Each element will have to be shifted to the right and re-indexed. That's much more work than we have to do with our linked list push operation.

2. This point is more relevant to more lower-level (relatively to Ruby) languages like C/C++ where you have to define the size of your array at the time of initialization. That is, if you initialized an array of size n and later you have n + 1 element that you want to add to it, you will have to create a new array of size n + 1 (or larger), copy your previous array to it and add your new element. Again, this involves more operations than we have to do with our linked lists - we create an empty linked list and then we add any number of elements to it at any time. Each of these elements is located somewhere (not necessarily adjacent memory locations) in our computer's memory and it's not a problem to add more elements - all we have to do is to point some other element in our linked list to this new element. That's it!

What if we want to remove an element from the "top" of our list? Not a problem!

{% highlight ruby %}
# previous code skipped for brevity

def shift
  self.head = head.next
end
{% endhighlight %}

That's it! All we have to do is to dereference (cut all ties to the top element) the top node. As soon as Ruby interpreter notices that no other node points to that top element, it gets garbage collected and the memory it occupied becomes available again.

How about inserting an element at the end of the list?

{% highlight ruby %}
# previous code skipped for brevity

def push(value)
  node = head
  while node.next
    node = node.next
  end
  node.next = Node.new(value)
end
{% endhighlight %}

Here we use a temporary variable `node` to store the list. Then, as long as the next node is not empty (the end of the list), we "jump" from node to node. At last, when we locate the last node in the list, we set its pointer to the new node.

That's it for our introduction to linked lists in Ruby. As you can see, there is nothing magical about linked list and they tend to be useful in some cases, especially when you are working with data that has to be organized in a certain fashion. 

In the next post we'll discuss several more advanced operations on linked list: insertion at position, deletion at position / by value and linked list reversal. Stay tuned!
