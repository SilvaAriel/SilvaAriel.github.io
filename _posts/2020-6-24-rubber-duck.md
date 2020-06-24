---
layout: post
title: "Rubber Duck: your best friend when debugging"
---

![rubber_duck]({{ site.baseurl }}/images/rubber-duck.jpg)

# The problem

I don't know about you, but I used to get really stuck when I was debugging.

Whether it was a new feature or a bug fix, I reached a point where I didn't know what was happening. 

So I would try to solve the problem. 

Sometimes there would have huge stack trace to help me, other times it wouldn't. After a long time trying unsuccessfully, my mind was tired and I was just walking in circles. This was the moment I would stop and finally ask for help.

Usually a few things would happen after that:
* I'd realized people have gone old and retired while I was trying to solve that bug.
* I'd take a walk to rest my mind.
* I'd start to think about the problem so I could explain to someone.

With the explanation ready, I'd reached out to a colleague and begin to explain the problem as detailed as possible. But, in the middle of my explanation, most of the solutions would come to me. Just like that.

What did just happened? Was it magic?

No. I just stopped to think. Simple as that.

Back then when I was debugging, I was just reacting to what I saw without taking any time to think about it. It's possible to spend hours doing something thoughtlessly, even if it doesn't look so.

# The solution

Just think.

That's all folks, bye!

---

Ok, it's not that simple.

After being hit by the same problem many times, I had to find a way to prevent it. That's when I found the Rubber Duck technique. It's quite simple. 

Remember when I approached my colleague to explain her my problem? And that I didn't have to finish explaining it? My colleague didn't say anything and still helped me just by listening to me. That's what the rubber duck is for: **it forces you to stop, think, and explain the problem.**

Of course, you don't have to buy a rubber duck and keep it on your desk ~~but you can, if you want~~. What I did is creating a imaginary persona. A programming noob I try to explain what I'm doing and what's going on. It's like the <a href="https://fs.blog/2012/04/feynman-technique/" target="_blank">Feynman Technique</a>.

That's nice, but explaining to the rubber duck doesn't happen naturally. Our major problem is reacting thoughtlessly, so how can we stop to actually think without wasting 2 hours of our lives?

It was a little annoying at first, but I developed the habit of setting a countdown whenever I begin to debug. So I had 15 minutes to solve the problem, or else I would ask for help. But before asking a colleague I would explain to my imaginary friend.

{% include images.html url="/images/bloo.jpg" description="Not this one, btw" %}

And most of the time that's what I needed.

# Summarizing
These are the steps I take:
1. Use a countdown to force me to stop.
2. Explain the problem to a rubber duck.
3. (If necessary) Explain it to a colleague.