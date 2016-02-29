---
layout: post
title: Type Conversions in C++
categories: articles
excerpt: "Regular cast, static_cast, dynamic_cast, reinterpret_cast, or const_cast?"
date: 2014-02-20T15:56:34+08:00
search_omit: true_
share: true
---

##### Regular Cast
These casts are also called C-style cast. A C-style cast is basically identical to trying out a range of sequences of C++ casts, and taking the first C++ cast that works, without ever considering ``dynamic_cast<T>``. Needless to say, this is much more powerful as it combines all of ``const_cast<T>``, ``static_cast<T>`` and ``reinterpret_cast<T>``, but it's also unsafe, because it does not use ``dynamic_cast<T>``.

In addition, C-style casts not only allow you to do this, but they also allow you to safely cast to a private base-class, while the "equivalent" ``static_cast<T>`` sequence would give you a compile-time error for that.

Some people prefer C-style casts because of their brevity. I use them for numeric casts only, and use the appropriate C++ casts when user defined types are involved, as they provide stricter checking.

##### static_cast<T>
``static_cast<T>`` is used for cases where you basically want to reverse an implicit conversion, with a few restrictions and additions. ``static_cast<T>`` performs no runtime checks. This should be used if you know that you refer to an object of a specific type, and thus a check would be unnecessary. Example:
{% highlight c++ %}
void func(void *data) {
  // Conversion from MyClass* -> void* is implicit
  MyClass *c = static_cast<MyClass*>(data);
  ...
}

int main() {
  MyClass c;
  start_thread(&func, &c)  // func(&c) will be called
      .join();
}
{% endhighlight %}
In this example, you know that you passed a ``MyClass`` object, and thus there isn't any need for a runtime check to ensure this.

##### dynamic_cast<T>
``dynamic_cast<T>`` is used for cases where you don't know what the dynamic type of the object is. You cannot use ``dynamic_cast<T>`` if you downcast and the argument type is not polymorphic. An example:
{% highlight c++ %}
if(JumpStm *j = dynamic_cast<JumpStm*>(&stm)) {
  ...
} else if(ExprStm *e = dynamic_cast<ExprStm*>(&stm)) {
  ...
}
{% endhighlight %}
``dynamic_cast<T>`` returns a null pointer if the object referred to doesn't contain the type casted to as a base class (when you cast to a reference, a ``bad_cast`` exception is thrown in that case).

The following code is not valid, because Base is not polymorphic (it doesn't contain a virtual function):
{% highlight c++ %}
struct Base { };
struct Derived : Base { };
int main() {
  Derived d; Base *b = &d;
  dynamic_cast<Derived*>(b); // Invalid
}
{% endhighlight %}
An "up-cast" is always valid with both ``static_cast<T>`` and ``dynamic_cast<T>``, and also without any cast, as an "up-cast" is an implicit conversion.

##### reinterpret_cast<T>
``reinterpret_cast<T>`` disregards all kind of type safety, allowing you to try to cast anything to anything else. It allows the underlying bit pattern of the source type to be reinterpreted as the destination type. This is done by casting to a reference or pointer variable then 'interpreting' the latter.
{% highlight c++ %}
struct MyClass { std::uint8_t char a, b, c, d };
std::uint32_t i = 12345;
MyClass &p = reinterpret_cast<MyClass &> i;
std::uint8_t u = p.c; // Read 1 byte from 'within' i (N.B.: endianness not guaranteed)
{% endhighlight %}
``reinterpret_cast`` can be very dangerous unless you know what you are doing. Showing the potential danger of old C-style casts, in some situations, a C-style cast will 'fall back' to ``reinterpret_cast``ing if it cannot find a safer option:
{% highlight c++ %}
int i = 0;
void *v = 0;
int c = (int)v; // is valid
int d = static_cast<int>(v); // is not valid, different types
int e = reinterpret_cast<int>(v); // is valid, but very dangerous
{% endhighlight %}

##### const_cast<T>
Lastly, we have ``const_cast<T>``, which removes the constness of a variable, thus allowing the result to be passed to a non-const function, etc. Like ``reinterpret_cast<T>``, a ``dynamic_cast<T>`` must be done via a reference or pointer.