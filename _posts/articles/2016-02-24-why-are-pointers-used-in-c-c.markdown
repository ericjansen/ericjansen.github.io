---
layout: post
title: Why are Pointers Used in C/C++
categories: articles
excerpt: "Why do you need to use pointers in C/C++?"
date: 2014-10-14T13:26:32+08:00
search_omit: true_
share: true
---
Conceptually pointers are the only actual abstraction showing access to underlying memory and you need them for any kind of controled memory allocation. If you remove them from the language (as it was done in other languages), you are losing something that can't be replaced.

Going to details, most pointer uses can be replaced by references, but there is still a visible few cases where references can't replace them. 

```void``` pointers is such a case. There is some functions expecting the address of something where something is untyped (like ```memcpy```). Accessing hardware from addresses is another such case.

Heap memory allocation (through ```malloc``` or ```new```) implies pointers (even if you can dereference return of new to assign to reference). To remove pointers we would have to change new to something else. References provides no tool for memory allocation. And there is even some cases where underlying implementation is horrible (like allocation of at least one byte for every struct, including empty ones to ensure all references are at different addresses) Also a reference is unique henceforth you can't really change it and this ability is needed for many dynamic data structures. To change that you would have to change both new and delete to something else (for instance the Java way), but doing so you are losing control on the low level.

What could probably be removed is equivalence between pointers and arrays. This is an ongoing process as there is more and more restrictions on the subject, like C++ forbidding to compute some intermediate address out of the array (except the next to last) even if you never dereference it. 

C++ without pointers should look very much like Java (if we ignore other differences like templates). If this is a good  evolution or not is another question. I would say if it evolves this way, C++ won't fit the "portable assembly" historic ecological niche of C language any more. Henceforth we would need another low-level object oriented language to replace it for that purpose.

Let me show you a simple example:
{% highlight c++ %}
int main() {
    int iA;
    int *pB = &iA;
    int *pC = &iA;
    int *pD = pB;

    iA = 100;

    ++(*pC);

    std::cout << "iA = " << iA << "\t*pB = " << *pB << "\t*pC = " << *pC << "\t*pD = " << *pD << '\n';
    std::cout << "sizeof(iA) = " << sizeof(iA) << "\tsizeof(pB) = " << sizeof(pB) << "\tsizeof(pC) = " << 
        sizeof(pC) << "\tsizeof(pD) = " << sizeof(pD) << '\n';
    std::cout << "sizeof(&iA) = " << sizeof(&iA) << '\n';
    std::cout << "address of iA = " << &iA << "\taddress of pB = " << pB << "\taddress of pC = " << pC << "\taddress of pD = " << pD << '\n';

    return 0;
}
{% endhighlight %}
The result will show:
{% highlight c++ %}
iA = 101	*pB = 101	*pC = 101	*pD = 101
sizeof(iA) = 4	sizeof(pB) = 8	sizeof(pC) = 8	sizeof(pD) = 8
sizeof(&iA) = 8
address of iA = 0x7fff56e8c878	address of pB = 0x7fff56e8c878	address of pC = 0x7fff56e8c878	address of pD = 0x7fff56e8c878
{% endhighlight %}

Another example, from Javascript point of view:
{% highlight javascript %}
function foo( bar ) {
 bar++;
}
var x = 5;
foo( x );
console.log( x );
{% endhighlight %}
Now, what is the value of x at then end of this code? 5 or 6?

Even though, in the function, the value of ```x``` gets assigned to bar and then incremented from 5 to 6, the log statement at the end will print 5. Why? because ```x```'s value will be copiedin to the function. In other words, bar won't be pointing at the value of ```x```, even though I wrote ```foo(x)```. It will be pointing at a copy of that value.

Now, let's say like this:
{% highlight javascript %}
function foo2( anArray ) {
 anArray[ 0 ]++;
}
var myArray = [ 10, 20, 30 ];
foo2( myArray );
console.log( myArray );
{% endhighlight %}
In this case, the log will read ````[ 11, 20, 30 ]````. So in the first case, the value was untouched. In this case, it's been changed. Why, because ```foo2``` didn't get passed a copy of a value. Rather, it got passed a pointer—to the same array that ```myArray``` pointed to. So, in the first case, ```x``` and bar pointed to different values, whereas in the second case, myArray and anArray pointe to the same value, a pointer to ````[10, 20, 30 ]````.

Put more simply, this is a variable set to a value ...
{% highlight javascript %}
var a = 10;
{% endhighlight %}
Whereas this is a variable set to a pointer:
{% highlight javascript %}
var a = [ 10 ];
{% endhighlight %}

But since nothing makes this explicit, you just have to learn some weird rules. And since many beginners don't, they get hopelessly muddled. And they wind up accidentally changing values they didn't intend to change and accidentally not-changing values they didintend to change.

Just in case this is unclear, compare this ...
{% highlight javascript %}
function foo( bar ) { bar++; console.log( bar ) };
var x = 5;
foo( x );
console.log( x );
{% endhighlight %}
output:
{% highlight javascript %}
6
5
{% endhighlight %}
with the following:
{% highlight javascript %}
function foo2( bar ) { bar[ 0 ]++; console.log( bar[ 0 ] ); }
var x = [ 5 ];
foo2( x );
console.log( x[ 0 ] );
{% endhighlight %}
output:
{% highlight javascript %}
6
6
{% endhighlight %}

