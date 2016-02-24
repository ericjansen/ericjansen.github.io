---
layout: post
title: How do I Learn Pointers in C/C++
categories: articles
excerpt: "Description about pointers in C/C++?"
date: 2016-02-24T13:25:55+08:00
search_omit: true_
---

First, lose any mysticism you might have gathered about pointers. If you've heard that pointers are hard to grasp, just forget that and relax. Memory is an array and variables are things kept in memory, with a value of some sort written at some memory position.

Second, think of pointers just as integer addresses - each pointer is an index into contiguous memory. If you have a variable called ```x```, then its address is found as ```ptr = &x;``` Given such a pointer ```ptr```, you can see what is stored at the address using ````*ptr```` . These two operations allow you to pass a pointer to a function ```int func(MyClass *p);``` instead of passing a much larger value, thus saving time and also allowing to read and modify the original value. If you don't need to modify the value, use ```int func(const MyClass *p);```

When you dereference a pointer by ````*ptr````, in addition to data ( ````(*ptr).data```` or ```ptr->data```) , you just might find another pointer ```ptr->next;``` This way you can pick more data, and more pointers, until ```ptr->next``` turns to ```0```. Such a data structure is called a linked list. In a doubly-linked list, each element has ```next``` and ```prev``` pointers, so you can traverse it in both directions. But you can get away with a single pointer and still traverse the list in both directions, as long as you store the exclusive OR (XOR) of the would-be prev and next pointers. Then, having a ```prev``` pointer and the XOR value, you can get the ```next``` pointer by XORing the two. And if you have next and XOR value, you can get the prev value the same way (your traversal needs to maintain two running pointers instead of just one). If you don't know what XOR is, think of the sum operator instead (and subtract from the result to get pointers). And if you do, tell how XOR is better than sum in this case.

Another thing you can do with a pointer is to skip the memory slot it points to and check the next slot ````*(++ptr)```` or just *++ptr For a data lookup, you can use ````++ptr->data````. Then the next slot, and so on. Instead of repeating ````++ptr```` many times, use ```ptr+k``` to skip ```k``` steps. A shortcut for ````*(ptr+k)```` is ```ptr[k]```. The most common way to think about it is that ```ptr``` is actually a __data array__, and you are taking the k-th element of this array. Of course, you need to know the size of the array not to run over. If the size is not given explicitly, sometimes the last valid element is marked with the ```0``` value. This is how C strings work - they are just zero-terminated character (char) arrays, which are defined by single pointers. For example, if I have C strings ```s1``` and ```s2``` of equal size with memory allocated to them, I can overwrite ```s2``` with ```s1``` using
```while(*s2++ = *s1++);``` This loop copies the character ````*s1```` over the character ````*s2````, advances both pointers, and continues if the copied character did not have code ```0```.
 
There are different ways to allocate memory, a common technique being 
{% highlight c++ %}
ptr = new int[20];
{% endhighlight %}
to create an array of 20 integers (this assumes you declared ```ptr``` with ```int* ptr;``` earlier). Just remember to call
{% highlight c++ %}
delete[] ptr;
{% endhighlight %}
before you lose the ptr value.
If you write
{% highlight c++ %}
int ptr[20];
{% endhighlight %}
then the memory will be released when ptr goes out of scope. But this makes it risky to pass ```ptr``` to a function.
You can also write :
{% highlight c++ %}
ptr = new int;
{% endhighlight %}
to allocate a single int, and this needs to be matched with
{% highlight c++ %}
delete ptr;
{% endhighlight %}
This is rarely useful for ints but can be useful for structs and classes. For example, you can repeat this step in a loop to construct an entire linked list, element by element. And then carefully destruct it. This example shows that pointers are typed - a pointer knows what kind of object it points to.


