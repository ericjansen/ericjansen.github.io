---
layout: post
title: My Experiment Using Lambda Expression in C++
categories: blog
excerpt: "Trial and error Lambda Function"
date: 2013-07-08T13:34:10+08:00
search_omit: true_
---

Lambda function under ``C++11`` is very helpful. Following I am not going to explain about it in details. You can find the reference about it from <a href="http://en.cppreference.com/w/cpp/language/lambda">this source</a>.

Conceptually, lambda function is like the following:
{% highlight c++ %}
[ captures ]( parameters )->returnTypesDeclaration { lambdaStatement; }
{% endhighlight %}

Example 1 - Sum of 2 parameters
Traditionally, the following ``sum`` function looks like:
{% highlight c++ %}
int sum(int iB,int iC) {
    return iB + iC;
}
{% endhighlight %}
With lambda, we can translate it directly as inline function:
{% highlight c++ %}
auto iA = [](int iB, int iC){ return iB + iC; };
{% endhighlight %}
Until this point, I do not capture in the ``[]`` any parameter to process. So let me show you the next example.

Example 2 - Area of a circle
{% highlight c++ %}
double circ(const double& lfR) {
    float fPI = 3.141592653;
    return fPI * lfR * lfR;
}
{% endhighlight %}
As I need to capture ``fPI`` value in the process, therefore, it goes like this:
{% highlight c++ %}
float fPI = 3.141592653;
auto circ = [&fPI](const double& iR)->double{ return fPI * iR * iR; };
{% endhighlight %}
or you can also make it as simple as below, since both are correct in returning ``double`` value.
{% highlight c++ %}
float fPI = 3.141592653;
auto circ = [&fPI](const double& lfR){ return fPI * lfR * lfR; };
{% endhighlight %}
Either you want to catch it as reference or value, it is up to your logical manner.

Let me show you again the other example below with returning value.
Example 3 - Counting lower case in a string
{% highlight c++ %}
int main()
{
    std::string strWord;
    std::cout << "Input: ";
    std::getline(std::cin,strWord);
    int iCount = 0;
    std::for_each(strWord.begin(),strWord.end(),[&iCount](const char& c)->int{ 
		if (islower(c)) ++iCount; return iCount; });
    std::cout << "Number of lower-case found: " << iCount << " characters\n"; 

    return 0;
}
{% endhighlight %}
As in console, I type any string and it will count the amount of lower-case letters.
{% highlight bash %}
Input: Test Hello World. It is an amazing day
Number of lower-case found: 26 characters
{% endhighlight %}

Example 4 - Adding one to each element of vector
{% highlight c++ %}
int main() {
    std::vector<int> viNum = { 3,4,6,8,100 };
    std::cout << "Before : ";
    for (auto const& n : viNum) std::cout << ' ' << n;
    std::cout << '\n';
	
    std::for_each(viNum.begin(),viNum.end(),[](int& iNum){ ++iNum; });
	
    std::cout << "After : ";
    for (auto const& n : viNum) std::cout << ' ' << n;
    std::cout << '\n';
    return 0;
}
{% endhighlight %}
Result as shown below:
{% highlight c++ %}
Before :  3 4 6 8 100
After :  4 5 7 9 101
{% endhighlight %}

I hope this simple tutorial can be helpful to understand about lambda function.