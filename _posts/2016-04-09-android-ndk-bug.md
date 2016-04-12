---
layout: post
title: "Android NDK: exceptions crash on ARM 32bit when compiled with clang and c++_shared"
date: 2016-04-09 15:15:20 
comments: true
---
At work we use `clang` and STL standard library from `c++_shared` included in the NDK.
This week we have got nasty surprise when we were trying to run some code that was
throwing an exception in our async library. This seemed to happend only on ARM 32bit devices. 
After a bit of digging, we have found [bug report][1] that describes more or less our situation.

We have built small test program that let us test few scenarios and that this is
really a bug in STL library shipped with Android NDK:

{% highlight c++ %}
// jni/hello.cpp
#include <stdexcept>
#include <iostream>
int main() {
  std::cerr << "1\n";
  try {
    throw std::runtime_error("A"); // crash! but only on arm 32bit, and when compiled with clang
  } catch (std::exception& e) {}
  std::cerr << "2\n";
}
{% endhighlight %}

{% highlight makefile %}
## jni/Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)
LOCAL_MODULE := hello
LOCAL_SRC_FILES := hello.cpp
LOCAL_CPPFLAGS := -frtti -fexceptions -fPIE -pie
LOCAL_LDLIBS := -llog -pie
include $(BUILD_EXECUTABLE)

## jni/Application.mk
APP_STL:=c++_shared
APP_ABI:=armeabi-v7a
NDK_TOOLCHAIN_VERSION:=clang

{% endhighlight %}

To fix it, we had to switch from `c++_shared` to `c++_static`. However, because we had multiple shared libraries, we had to change the way we build our code, so that we don't break [ODR][2]. Instead of building multiple shared libraries, we now build all libraries statically and link them together into one shared library.

This bug was recently fix, and should be available in the next Android NDK release (r12?).

[1]: https://code.google.com/p/android/issues/detail?id=179410
[2]: https://en.wikipedia.org/wiki/One_Definition_Rule
