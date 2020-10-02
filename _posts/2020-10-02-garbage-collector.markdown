---
layout: post
title:  "Java's Garbage Collection for Beginners"
date:   2020-10-02 07:00:55 -0300
image:  garbage_collector.png
tags:   java garbage-collection memory
---
Object-Oriented Programming relies heavily on objects (hence the name). We create and use objects in Java all the time and yet we don’t really pay much attention to it. But each one of those objects take up space in the memory. And as we know, memory is finite.
In theory, after we create so many objects the memory would be full and no objects would be created anymore. But that doesn’t happen. Why?

# Garbage Collection

Garbage Collection is an automatic memory manager. It is the process used by Java to free up unused memory. It does that by cleaning the [Heap Memory](https://stackoverflow.com/a/2308762/5491371) of unreferenced/unreachable objects and reclaiming the space back to be used by another object in the future.
In languages like C++, for example, you have to manually allocate memory (`malloc()` or `calloc()`) and then, later, deallocate it (`free()`). 

But what happens if the programmer forgets to free up the memory after there’s no reference to the object? A Memory Leak. That happens when the memory used by the object is not released after it has no reference (the opposite would be [Dangling Pointer](https://en.wikipedia.org/wiki/Dangling_pointer)).

An example of unreachable object that would cause Memory Leak is:

{% highlight java %}
Integer num = new Integer(5); // 1 
num = null; // 2
{% endhighlight %}

1 - The Integer object is added to the Heap Memory, making it a live object. Also, the variable “num” is added to the [Stack Memory](https://www.baeldung.com/java-stack-heap#stack-memory-in-java) with a pointer to the Integer’s address in Heap Memory.

2 - Now the object Integer has no reference which means it’s unreachable. It’s a dead object. If an unreachable object remains in the Heap Memory it might prevent JVM from allocating new objects in it. When this happens Java raises `java.lang.OutOfMemoryError`.

# Tracing Strategy
The GC work can be divided into two tasks: 
1. How it finds dead objects
2. What it does to them.

The most common Garbage Collection strategy to find dead objects is Tracing. In this strategy the Garbage Collector is responsible for tracing all objects branching from GC roots. Those roots are objects outside Heap Memory, like System classes, Threads or method variables.

This strategy uses these algorithms to deal with dead objects: Copying Algorithm, Mark-and-sweep Algorithm and Mark-and-Compact Algorithm.

## Mark-and-sweep Algorithm
This algorithm is divided into two processes: marking the dead objects and then reclaiming their spaces.
This is the most common and simple algorithm. But its weakness is in its simplicity. 

Whenever the GC cycle runs it traverses all objects and marks the dead ones, later they will be swept leaving empty spaces in the memory. Those spaces could be small or big and depending on the size of the new object it might not fit on any of those spaces. This whole process leaves the memory fragmented.

![]({{ site.baseurl }}/images/mark-and-sweep.png)
*Source: [How Does Garbage Collection Work in Java? | by Alibaba Tech](https://medium.com/@alitech_2017/how-does-garbage-collection-work-in-java-cf4e31343e43)*

## Copying Algorithm

In this algorithm, the Heap Memory is divided in two spaces, called From and To. All new objects are created in the From space. When it’s full, the GC copies the live objects into the To space and reclaims the From’s space used by dead objects.

The Copying algorithm deals with Mark-and-sweep’s fragmentation problem, since it copies the live objects and puts them side-by-side in the To space.
However, by solving that problem, a new one arises: the Heap Memory is divided in two. That causes the GC cycle to run more often than it would if all the Heap Memory were being used for new objects.

![]({{ site.baseurl }}/images/copying.png)
*Source: [How Does Garbage Collection Work in Java? | by Alibaba Tech](https://medium.com/@alitech_2017/how-does-garbage-collection-work-in-java-cf4e31343e43)*

## Mark-and-Compact Algorithm
This algorithm is an extension of Mark-and-sweep. 

It doesn’t leave the memory fragmented when the space is reclaimed. Instead, it sorts the memory by moving all living objects to one side and then it reclaims the marked space. Mark-and-Compact also solves the problem raised by Copying Algorithm, because it uses the whole Heap Memory.

Even if it solves both of the problems created by the preceding algorithms, it comes with a price. Everytime the GC cycle runs it has to sort out every reference address. That makes this algorithm less efficient than the others.

![]({{ site.baseurl }}/images/mark-and-compact.png)
*Source: [How Does Garbage Collection Work in Java? | by Alibaba Tech](https://medium.com/@alitech_2017/how-does-garbage-collection-work-in-java-cf4e31343e43)*

## Generational Collection Algorithm

This algorithm combines the above algorithms and applies them in the scenario they best fit.

Every object created will have an “age”. They get “older” every time a Garbage Collection Cycle happens. Java separates them in two parts in the Memory Heap:

* **Young Generation**, divided in:
    * Eden Space
    * Survivor Space

* **Old Generation**, divided in:
    * Tenured Space

### Eden Space

Newly created objects are added to the Eden Space, that’s why the Mark-and-sweep Algorithm is used in this scenario. 

Copying Algorithm wouldn’t be the best fit there because it divides the memory in two and the minor Garbage Collection Cycle would run often. Minor GC cycles are quicker than Major ones and run in Young Generations. Mark-and-compact wouldn’t be the best fit here either, because most objects are short-lived. The large number of dead objects would force the GC to change the reference of the memory’s addresses constantly.

### Survivor Space

After a minor GC cycle, all living objects in Eden Space will be moved to  Survivor Space. It stores objects that have lived for a while, so it doesn’t require as much space as Eden Space does, that’s why the Copying Algorithm suits here.

Objects coming from Eden Space are stored in Survivor’s From space. If From is full, the objects will go directly to Tenured Space.

After another minor GC the living objects from Eden Space and From space (Survivor Space) are moved to the To space.

This last part deserves attention. The living objects from Eden will be moved to the To Space, not the From Space. This happens because the minor GC will also reclaim dead objects from the From Space, leaving it fragmented. So it might not have enough space for the objects coming from Eden. In the next cycle the objects will be moved to the From Space, including the living objects from To Space until they reach a certain threshold (15 by default) and are moved to the Old Generation.

It’s worth saying that every Garbage Collection is a “Stop-the-World” event. This means all threads are stopped until the Garbage Collection completes.

### Tenured Space
After multiple Minor Garbage Collection Cycles, the objects from Young Generation will be moved to Tenured Space (Old Generation).

Since those objects are long-lived and their spaces will hardly be reclaimed, the Mark-and-Compact algorithm will be applied here. 

When the Tenured Space is filled up a Major GC will be triggered.

# Conclusion
Memory management is tough (ask the C++ people), but we forget about that because the work is done for us automatically. However, every automation comes with a price.

Depending on the application’s size the GC might have a major impact on its performance, due to the Stop-the-World event. It’s up to your team to tweak the GC to fit your needs and achieve the performance expected.

# Where can you go from here?
Here are some good articles to learn more about Garbage Collection:
* [Ebook: Java Garbage Collection Handbook](https://plumbr.io/landing/handbook-java-garbage-collection)
* [Java Garbage Collection Basics by Oracle](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)
* [How Does Garbage Collection Work in Java?](https://medium.com/@alitech_2017/how-does-garbage-collection-work-in-java-cf4e31343e43)
* [How Java Garbage Collection Works](https://www.dynatrace.com/resources/ebooks/javabook/how-garbage-collection-works/)
* [JVM performance optimization, Part 3: Garbage collection](https://www.infoworld.com/article/2078645/jvm-performance-optimization-part-3-garbage-collection.html)
* [GC impact on throughput and latency](https://plumbr.io/blog/garbage-collection/gc-impact-on-throughput-and-latency)
