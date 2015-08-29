---
layout: post
title: "iOS logger: CocoaLumberjack"
date: 2012-01-21 01:21:47
---
![img](/assets/images.jpg)
<p>I had the opportunity to integrate and work with CocoaLumberjack in my latest project. It is very nice logging library, with few useful features that makes it worth using:</p>
<ul>
<li>multiple outputs, you can direct your logs to be printed in the XCode console, system console or whatever other place you want by implementing one basic protocol (that's actually what we did to have <a title="Apphance" href="http://www.apphance.com/" target="_blank">apphance</a> logging in production builds only)</li>
<li>ability to define log level: error, warning, info, debug, verbose. It is even possible to define logging level, not only for the entire applications, but also for selected files in your project</li>
<li>it is faster than NSLog in most cases</li>
</ul>

{% highlight obj-c %}
// Add custom logging outputs:
[DDLog addLogger:[DDASLLogger sharedInstance]];
[DDLog addLogger:[DDTTYLogger sharedInstance]];

// Convert from this:
NSLog(@"Broken sprocket detected!");
NSLog(@"User selected file:%@ withSize:%u", filePath, fileSize);

// To this:
DDLogError(@"Broken sprocket detected!");
DDLogVerbose(@"User selected file:%@ withSize:%u", filePath, fileSize);
{% endhighlight %}

<p>Library definitely worth recommendation. Check it's repository on GitHub for more info: <a href="https://github.com/robbiehanson/CocoaLumberjack">https://github.com/robbiehanson/CocoaLumberjack</a></p>
