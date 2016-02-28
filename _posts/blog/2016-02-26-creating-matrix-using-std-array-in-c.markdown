---
layout: post
title: Creating Matrix Using std::array in C++
categories: blog
excerpt: "Defining 2D and 3D Matrices"
date: 2015-03-14T10:48:22+08:00
search_omit: true_
share: true
---

Most of C++ developers agree that the complexities of C++ comprise of pointer utilization, string, and matrix topics. Matrix is a crucial part of programming, so I'd like to share from my standing point in using ```std::array```. Since ``C++11``, ``std::array`` is one of its feature of STL.
``std::array`` is a container that encapsulates fixed size arrays. 
It is very useful for mathematical calculation, especially related to 2-dimensional and 3-dimensional calculation. ``std::array`` is defined in header ```<array>```.
More information about the container ``std::array``, please refer to <a href="http://en.cppreference.com/w/cpp/container/array">this link</a>.

##### Difference between ```std::array``` and ```std::vector```
I copied someone's answer from  [stackoverflow](http://stackoverflow.com/questions/4424579/stdvector-versus-stdarray-in-c). I think it might be useful to identify which one you should use.

```std::vector``` is a template class that encapsulate a dynamic array, stored in the heap, that grows and shrinks automatically if elements are added or removed. It provides all the hooks (```begin()```, ```end()```, iterators, etc) that make it work fine with the rest of the STL. It also has several useful methods that let you perform operations that on a normal array would be cumbersome, like e.g. inserting elements in the middle of a vector (it handles all the work of moving the following elements behind the scenes). Since it stores the elements in memory allocated on the heap, it has some overhead in respect to static arrays.

```std::array``` is a template class that encapsulate a statically-sized array, stored inside the object itself, which means that, if you instantiate the class on the stack, the array itself will be on the stack. Its size has to be known at compile time (it's passed as a template parameter), and it cannot grow or shrink.

It's more limited than ```std::vector```, but it's often *more efficient*, especially for small sizes, because in practice it's mostly a lightweight wrapper around a C-style array. However, it's more secure, since the implicit conversion to pointer is disabled, and it provides much of the STL-related functionality of ```std::vector``` and of the other containers, so you can use it easily with STL algorithms & co. Anyhow, for the very limitation of fixed size it's much less flexible than ```std::vector```.

##### 2-Dimensional ```std::array``` Example
2-dimensional matrix as mentioned in this [link](http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2013/n3526.html), should be defined as follow:
{% highlight c++ %}
std::array<std::array<int,3>,3> aTest2D = { { {1,-2,0},{-3,7,-8},{9,-4,1} } }; 
{% endhighlight %}
Following is the method to call the value of ``aTest2D``:
{% highlight c++ %}
for (const auto& a0 : aTest2D) { 
  for (const auto& a1 : a0)
    std::cout << a1 << ' ';
  std::cout << '\n';
}
{% endhighlight %}

##### 3-Dimensional ```std::array``` Example
Please note the number of the brackets in the declaration of 3-dimensional matrix. It would yield errors if you do not define the exact amount of brackets.
{% highlight c++ %}
std::array<std::array<std::array<int,3>,3>,3> aTest3D = { { { { { 1,-2, 0},{-3,7,-8},{ 9,-4,1} } },
                                                            { { {-7,12,-4},{ 5,5, 0},{-3, 7,6} } },
                                                            { { { 8, 2,-4},{ 3,2,-1},{ 3,-4,7} } } } };
{% endhighlight %}
Following is the method to call the value of ``aTest3D``:
{% highlight c++ %}
for (const auto& a0 : aTest3D) {
    for (const auto& a1 : a0) {
        for (const auto& a2 : a1)
            std::cout << a2 << ' ';
        std::cout << '\n';
    }
    std::cout << '\n';
}
{% endhighlight %}
