---
layout: post
title: "Android NDK: Create shared library from a static library"
date: 2016-04-08 15:15:20 
comments: true
---

Let's assume you want to create one shared library from two static libraries. Here is how to do it:

In your Android.mk

{% highlight make %}
include $(CLEAR_VARS)
LOCAL_MODULE := lib1
LOCAL_SRC_FILES := lib1.cpp
include $(BUILD_STATIC_LIBRARY)

include $(CLEAR_VARS)
LOCAL_MODULE := lib2
LOCAL_SRC_FILES := lib2.cpp
include $(BUILD_STATIC_LIBRARY)

# this shared library will have all symbols from two above libraries
include $(CLEAR_VARS)
LOCAL_MODULE := lib_shared
LOCAL_SRC_FILES := empty.cpp
LOCAL_WHOLE_STATIC_LIBRARIES := lib1 lib2
include $(BUILD_SHARED_LIBRARY)
{% endhighlight %}

Important option to note is `LOCAL_WHOLE_STATIC_LIBRARIES`. If you use regular `LOCAL_STATIC_LIBRARIES`, because you don't use any symbols from `lib1` or `lib2` in `lib_shared`, they will be stripped at link time. To prevent this from happening `LOCAL_WHOLE_STATIC_LIBRARIES` adds following options to link line to make sure symbols are not stripped:

{% highlight bash %}
-Wl,--whole-archive -llib1 -llib2 -Wl,--no-whole-archive
{% endhighlight %}

Here is documentation for `--whole-archive`:

> `--whole-archive`
> 
> For each archive mentioned on the command line after the `--whole-archive` option, include every object file in the archive in the link, rather than searching the archive for the required object files.  This is normally used to turn an archive file into a shared library, forcing every object to be included in the resulting shared library.  This option may be used more than once.
