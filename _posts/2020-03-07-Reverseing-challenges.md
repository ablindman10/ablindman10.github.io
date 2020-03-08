---
layout: post
title: Linux Reversing easy challenges
---

What's the best way to get better at something? Trial and error, this blog will be dedicated to me improving upon my RE skillset, and to hopefully provide a good guide for others wanting to learn RE.

### Crackme's

Crackme's are a fun way to learn to RE. They are binaries that are made in such a way to teach you something new or to hone your RE skills.
Head on over to the [https://crackmes.one/](https://crackmes.one/) website for a list of crackmes.


### Downloading all the easy Linux challenges

I'm more familiar with Linux RE than I am with anything else. For me this is where I am starting. Instead of downloading each challenge one by one, I'm going to walk through a script I'm going to use to download all the easy Linux crackmes.

Head over to the website and search for all the easy C++ Linux crackmes.
![placeholder](/images/crackme1.PNG)

After you have searched just grab that HTML source code. Notice the pattern in the links. I stored this in a file test, which I will use later.
![placeholder](/images/crackme2.PNG)

Back on the website look at the source code where you can download the link. The general format is https://crackmes.one/static/crackme/5e0fa43b33c5d419aa01351e.zip
![placeholder](/images/crackme3.PNG)


Downloading all these individually would be a pain. Instead I wrote this script to download all of the zips for the easy crackmes.
![placeholder](/images/crackme4.PNG)

{% highlight sh %}
grep -oh 'crackme/.*"' website > test
{% endhighlight %}
This find all the references to crackme/stuff in the html source code in the file test

{% highlight sh %}
sed 's/"//' test > test2
{% endhighlight %}
This will remove the " at the end, that I couldn't figure out how to get rid of with grep

{% highlight sh %}
sed -i -e 's/^/https:\/\/crackmes.one\/static\//' test2
{% endhighlight %}
This adds the format https://crackmes.one/static/ at the beginning of the file test2

{% highlight sh %}
sed -e 's/$/.zip/' -i test2
{% endhighlight %}
This this adds a .zip to the end of the file test2

{% highlight sh %}
wget -i test2
{% endhighlight %}
Finally we read from the file test2 and download all those zips

SUCCESS
![placeholder](/images/crackme5.png)
