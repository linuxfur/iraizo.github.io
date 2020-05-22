---
title:  "CSS Frameworks"
layout: post
---

We need to talk about css frameworks, and how they improve the performance and standards of developement in the frontend.

# So first of all what is a framework?

`In computer programming, a software framework is an abstraction in which software providing generic functionality can be selectively changed by additional user-written code, thus providing application-specific software.`

TLDR; many css styles in one css file made in classes 

Heres a example of the popular framework [tailwind](https://tailwindcss.com/).
{% highlight html %}
<div class="w-full max-w-xs">
{% endhighlight %}

Let me break these simple classes down to its core, w-full means width full which if we look up in the [documentation](https://tailwindcss.com/docs/width/#app), means the div's width is set to 100%.

{% highlight html %}
.w-full 	width: 100%;
{% endhighlight %}


# But why use one?

Now why does this improve the stanrds of developement, well basically you just need one css file to build your site.
But you also dont need to write a single line of css so instead of looking up how to set the width to 100% you can simply
enter width in the tailwind documentation and find out what class you need to give the div.
This greatly speeds up the developement proccess and you can focus on more important things.

# Performance

The most common question i get is, isnt it gonna hit the website performance if you have thousands of lines of css that you dont even use?
Yes this is true, thats why you would use something that deletes the css lines you dont use when making a production build for your website.
I can recommend [purgecss](https://purgecss.com/).
<br>
 `PurgeCSS is a tool to remove unused CSS`.

# Standards
You/Your team dosent need to watch out to code the same css classes or styles which are basically useless, they can simply use the framework
without needing to care since you just add classes to the HTML elements.

# TLDR
Your code gets automatically optimized, you dont write a single line of css and get a better workflow.