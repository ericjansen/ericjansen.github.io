---
layout: post
title: When Using Pointers in C++
categories: articles
excerpt: "When do you use pointers while programming?"
date: 2014-10-16T11:47:02+08:00
search_omit: true_
---

<h5>1. When functions have outputs other than their return value.</h5> 
{% highlight c++ %}
Status CopyFile(const std::string &from, const std::string &to, int64_t *bytesCopied);
{% endhighlight %}
so it's more obvious to people reading the code that an argument is for output and its contents may change
{% highlight c++ %}
int64_t length;
Status result(CopyFile("src", "dest", &length));
{% endhighlight%}
<h5>2. When using C++ with 'C' libraries or assembly code.</h5>
<h5>3. When accessing hardware directly or doing other low level programming involving things like  shared memory.</h5>
<h5>4. When writing C not C++ and dynamic allocation is needed.</h5>
In C++ you're almost always better off using the boost/C++11 ```shared_ptr```, ```shared_array```, ```scoped_ptr```, ```scoped_array```, and ```unique_ptr``` since the objects they refer to will be destroyed when the reference count hits zero whether that's through a normal code path, function return, or because an exception was thrown. Use ```weak_ptr``` for circular references.
<h5>5. When writing C not C++ and you need to access a structure as an input parameter.</h5>
In C++ constant references for inputs are more resilient to programming issues (they can't be reassigned) and make call inputs visually different from outputs when pointers are used for those.
In C
{% highlight c++ %}
struct foo;
void examine_foo(struct foo f);
{% endhighlight %}
is legal but creates a copy of ```f```, you need to change it to
{% highlight c++ %}
void examine_foo(const struct foo *f);
{% endhighlight %}
instead.
<h5>6. When writing C not C++ and manipulating arrays including strings</h5>
Pointers are more terse than indexes and don't cost you anything in terms of safety because the ````[]```` operator does no bounds checking.
{% highlight c++ %}
static char* reverse(char *str, char sep) {
    char *end, *left, *right, tmp;
 
    for (end = str; *end && *end != sep; ++end) {
    }
 
    for (left = str, right = end - 1; left < right; ++left, --right) {
        tmp = *left;
        *left = *right;
        *right = tmp;
    }
 
    return end;
}
{% endhighlight %}
In C++ you use container iterators instead, and might use at instead of ```operator[]``` to get bounds checking.
<h5>7. Where you need to change logic at run-time or apply generic code to different situations</h5>
In C you use pointers to functions for this
{% highlight c++ %}
void qsort(void *base, size_t nmemb, size_t size,
           int(*compar)(const void *, const void *));
{% endhighlight %}
The same thing is possible in C++, although due to type safety pointers to member functions are better when the functionality is constrained to a class and function objects (often by way of ```boost::bind```) work better in generic situations like ordering STL containers (for instance I have a comparison function object which sorts paths with directories after their descendants).
