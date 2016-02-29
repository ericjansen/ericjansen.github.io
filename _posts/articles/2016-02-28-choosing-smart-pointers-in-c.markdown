---
layout: post
title: Choosing Smart Pointers in C++
categories: articles
excerpt: "auto_ptr, unique_ptr, shared_ptr, or weak_ptr?"
date: 2015-03-07T13:42:24+08:00
search_omit: true_
share: true
---

A "raw" pointer is unmanaged. That is, the following line:
{% highlight c++ %}
SomeKindOfObject *someKindOfObject = new SomeKindOfObject();
{% endhighlight %}
.. will leak memory if an accompanying delete is not executed at the proper time.

#### ``auto_ptr<T>``
In order to minimize these cases, ``std::auto_ptr<T>`` was introduced. Due to the limitations of C++ prior to the 2011 standard, however, it's still very easy for auto_ptr to leak memory. It is sufficient for limited cases, such as this, however:
{% highlight c++ %}
void func() {
    std::auto_ptr<SomeKindOfObject> sKOO_ptr(new SomeKindOfObject());
    // do some work
    // will not leak if you do not copy sKOO_ptr.
}
{% endhighlight %}
One of its weakest use-cases is in containers. This is because if a copy of an ``std::auto_ptr<T>`` is made and the old copy is not carefully reset, then both copies of the auto pointer will attempt to delete the object, resulting in a double-free in the worst case.

#### ``unique_ptr<T>``
As a replacement, C++11 introduced ``std::unique_ptr<T>``:
{% highlight c++ %}
void func2() {
    std::unique_ptr<SomeKindofObject> sKOO_unique(new SomeKindOfObject());

    func3(sKOO_unique); // now func3() owns the pointer and sKOO_unique is no longer valid
}
{% endhighlight %}
Such a ``std::unique_ptr<T>`` will be correctly cleaned up, even when it's passed between functions. It does this by semantically representing "ownership" of the pointer - the "owner" cleans it up. This makes it ideal for use in containers:
{% highlight c++ %}
std::vector<std::unique_ptr<SomeKindofObject>> sKOO_vector();
{% endhighlight %}
Unlike ``std::auto_ptr<T>``, ``std::unique_ptr<T>`` is well-behaved here, and when the vector resizes, none of the objects will be accidentally deleted while the vector copies its backing store.

#### ``shared_ptr<T>`` and ``weak_ptr<T>``
``std::unique_ptr<T>`` is useful, to be sure, but there are cases where you want two parts of your code base to be able to refer to the same object and copy the pointer around, while still being guaranteed proper cleanup. For example, a tree might look like this, when using ``std::shared_ptr<T>``:
{% highlight c++ %}
template<class T>
struct Node {
    T value;
    std::shared_ptr<Node<T>> left;
    std::shared_ptr<Node<T>> right;
};
{% endhighlight %}
In this case, we can even hold on to multiple copies of a root node, and the tree will be properly cleaned up when all copies of the root node are destroyed.

This works because each ``std::shared_ptr<T>`` holds on to not only the pointer to the object, but also a reference count of all of the ``std::shared_ptr<T>`` objects that refer to the same pointer. When a new one is created, the count goes up. When one is destroyed, the count goes down. When the count reaches zero, the pointer is ``delete`` d.

So this introduces a problem: Double-linked structures end up with circular references. Say we want to add a ``parent`` pointer to our tree ``Node``:
{% highlight c++ %}
template<class T>
struct Node {
    T value;
    std::shared_ptr<Node<T>> parent;
    std::shared_ptr<Node<T>> left;
    std::shared_ptr<Node<T>> right;
};
{% endhighlight %}
Now, if we remove a ``Node``, there's a cyclic reference to it. It'll never be ``delete`` d because its reference count will never be zero.

To solve this problem, you use a ``std::weak_ptr<>``. Weak pointers just "observe" the managed object; they don't "keep it alive" or affect its lifetime. Unlike ``shared_ptr<T>``s, when the last ``weak_ptr<T>`` goes out of scope or disappears, the pointed-to object can still exist because
the ``weak_ptr<T>``s do not affect the lifetime of the object - they have no ownership rights. But the ``weak_ptr<T>`` can be used to determine whether the object exists, and to provide a ``shared_ptr<T>`` that can be used to refer to it. 
{% highlight c++ %}
template<class T>
struct Node {
    T value;
    std::weak_ptr<Node<T>> parent;
    std::shared_ptr<Node<T>> left;
    std::shared_ptr<Node<T>> right;
};
{% endhighlight %}
Now, things will work correctly, and removing a node will not leave stuck references to the parent node. It makes walking the tree a little more complicated, however:
{% highlight c++ %}
std::shared_ptr<Node<T>> parent_of_this = node->parent.lock();
{% endhighlight %}
This way, you can lock a reference to the node, and you have a reasonable guarantee it won't disappear while you're working on it, since you're holding on to a ``std::shared_ptr<T>`` of it.

##### ``make_shared<T>()`` and ``make_unique<T>()``
Now, there are some minor problems with ``std::shared_ptr<T>`` and ``std::unique_ptr<T>`` that should be addressed. The following two lines have a problem:
{% highlight c++ %}
foo_unique(std::unique_ptr<SomeKindofObject>(new SomeKindOfObject()), thrower());
foo_shared(std::shared_ptr<SomeKindofObject>(new SomeKindOfObject()), thrower());
{% endhighlight %}
If ``thrower()`` throws an exception, both lines will leak memory. And more than that, ``std::shared_ptr<T>`` holds the reference count far away from the object it points to and this can mean a second allocation. That's not usually desirable.

C++11 provides ``std::make_shared<T>()`` and C++14 provides ``std::make_unique<T>()`` to solve this problem:
{% highlight c++ %}
foo_unique(std::make_unique<SomeKindofObject>(), thrower());
foo_shared(std::make_shared<SomeKindofObject>(), thrower());
{% endhighlight %}
Now, in both cases, even if ``thrower()`` throws an exception, there will not be a leak of memory. As a bonus, ``std::make_shared<T>()`` has the opportunity to create its reference count in the same memory space as its managed object, which can both be faster and can save a few bytes of memory, while giving you an exception safety guarantee!
