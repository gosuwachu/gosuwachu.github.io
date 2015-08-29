---
layout: post
title: "Xcode 5: Bug in C++ standard library (std::bind)"
date: 2014-01-15 21:43:26
categories: blog
tags: bug c++ xcode
---
While working on cross platform C++ library, I have found that this code crashes if compiled with Xcode 5:

{% highlight c++ %}
#include <iostream>
#include <functional>

int main(int argc, const char * argv[])
{
    std::function<void(int)> function = [](int){};
    auto binding = std::bind(function, 10);
    // crashes here with EXC_BAD_ACCESS
    std::function<void()> jobFunctor = binding;
    return 0;
}
{% endhighlight %}

When converting the result of the bind to `jobFunctor` there is an infinite stack recursion in a `std::function` constructor.

clang version:
{% highlight bash %}
$ clang++ --version
Apple LLVM version 5.0 (clang-500.2.79) (based on LLVM 3.3svn)
Target: x86_64-apple-darwin12.5.0
Thread model: posix
{% endhighlight %}

Turns out there is official bug report about it: [http://llvm.org/bugs/show_bug.cgi?id=18282](http://llvm.org/bugs/show_bug.cgi?id=18282)

Above example works fine in Xcode 5.1.
