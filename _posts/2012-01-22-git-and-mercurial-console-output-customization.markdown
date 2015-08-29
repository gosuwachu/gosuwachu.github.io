---
layout: post
title: Git and Mercurial console output customization
date: 2012-01-22 00:41:12
---
<p>Recently with a lot of help from guys from Polidea, we have created few useful output customization for our version control systems(for those who use terminal). So let's start with mercurial, here is part of the .hgrc file (Mercurial.ini on Windows, however some commands might not work):</p>

{% highlight bash %}
# extension for paging mercurial output
[pager]
pager = LESS='FSRX' less
attend = annotate, cat, diff, export, glog, log, qdiff, hist

# extension do display log in graphical way
[extensions]
graphlog =
pager =

# alias for pretty printing mercurial log
[alias]
hist = glog --template "
33[31m{node|short}
33[0m
33[1;34m<{author|person}>
33[0m
33[32m({date|age})
33[0m \n\t\t{desc}\n"
{% endhighlight %}

<p>We first enable and configure <strong>pager</strong> extension which basically allows you to scroll through the command output, it works pretty much like hg &lt;your command&gt; | less. Then we enable <strong>graphlog</strong> extension, that makes <em>hg log</em> to print nicely formated graph of branches, merges, commits, etc. Finally to additionally color log output we do some magic, and define our own <em>hg log</em> with custom template. Final result of running hg hist looks like this:</p>
<p><center><a href="/assets/githist1.png"><img class="aligncenter size-full wp-image-711" title="githist" alt="" src="/assets/githist1.png" width="398" height="221" /></a></center></p>
<p>The same can be done for git. Here is equivalent .gitconfig (thanks to Wojciech Erbetowski for this):</p>


{% highlight bash %}
[alias]
hist = log --pretty=format:'%Cred%h%Creset %C(bold blue)<%an>%Creset%C(yellow)%d%Creset %Cgreen(%cr)%Creset%n%w(80,8,8)%s' --graph
{% endhighlight %}
