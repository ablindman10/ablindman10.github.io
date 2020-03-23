---
layout: post
title: Information Leaks
---

One common challege is to reverse a string minpulation. The authors of the challenge can get really creative with how they are minpulating the strings, but if they are not careful they can leak information. Why reverse the algorithem they use to minpulate the string, when you can just look at the compaison between the correct input vs what you input.

### How information leaks occur

An information leaks occurs when they programer stores information on the stack, does not clear out the registers, or just has information in the clear.

You first need to understand how information is stored in registers and on the stack. Refer to the following gif.
![placeholder](/images/stack_regs.gif)

While the program executes the machine code, it will store values on the stack or into registers. For effciency sometimes the compilers dont clear out the registers, because of this important information will be left there. Here is an example RE challenge where this occurs.



### mgdilolmsoamasiug

In this challenge we are given a binary, that takes two inputs. String 1 that is manipulated, and string 2 that you "guess" what it will look like post manpulation.

![placeholder](/images/post2_pic3.png)

The first thing I always do is check what type of file it is, run strings on it, and try to run it.
![placeholder](/images/post2_pic1.png)
![placeholder](/images/post2_pic2.png)


You can see its a 64bit ELF binary, and it has some strings that are referenced when it runs, specifically the "good job" or the "not exactly". Lets see if we can figure out if this program leaks information instead of trying to reverse whatever algorithem they use to manipulate the input string.


![placeholder](/images/post2_pic4.png)

Loading it in ghidra and looking in main reveals they store 2 strings from user input, do some magic to it, then do a compairson. Lets look right before the compairson executes to see what its compairing.

So we should stop the program around line 34-35, which is when the compairson is made. We should stop it around there and take a look on the stack and in the registers. To do this I used GDB with PEDA(a useful python extentions for GDB).

![placeholder](/images/post2_pic5.png)

Stopping it just before the if statement is executed (line 35), you can see their is some interesting information laying around. In this example the input was "mgdilolmsoamasiug", and you can see on the stack and in the registers is "mgdilolmsoamasidg". Lets try that and see if thats the correct solution.

![placeholder](/images/post2_pic6.png)


SUCCESS! It loks like that was correct. Why bother reversing the string manuplations when you can peak at the compairson being made.







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
