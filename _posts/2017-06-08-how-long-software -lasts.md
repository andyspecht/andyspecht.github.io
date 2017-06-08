---
layout: post
title: How Long Should Software Last? 
image: ../img/HowLongSoftware/ComputerStuff.png
tags: [ERP, Access]
---

Here's a question I've been thinking about: how long should a piece of software last? Or: how long should a piece of software be useful? The question has been prompted by working with two completely diverging kinds of software.

### The Ancient Microsoft Access Program

In my job one of my smaller responsibilities is maintaining a few Microsoft Access programs that have managed to stick around for close to two decades. These are not just databases with basic forms and reports; they are labyrinths of VBA, intricate GUIs, and in one case hundreds of distinct Access databases that are manipulated from a main database. They were created by a single developer and passed down over the years. No technical documentation still exists (if it ever did). These programs are very much still in use, but there's no new development on them, and I avoid looking at them as best I can. Everyone bemoans the fact that these Access programs are still around, but there's little movement towards replacing them, because, hey, they still basically work.

These have longevity. They still exist because they were completely customized and hand-built to have a very specific purpose. I suppose they would be less frustrating to work with if they were built with higher quality, better documented code, but even then, they would still run into compatibility issues with newer versions of Access and newer OS's. It makes me wonder if the original developer thought that these would be around nearly 20 years later. Did MS Access look like the ultimate product for creating programs for end-users, or was it supposed to be a temporary solution to be swept away by technological progress?

The horror of having to try to maintain these ancient Access programs leads to the suggestion that small/medium-sized organizations should not try to develop their own software in the first place. No matter how great your developers are now, it seems inevitable that they will produce something that will look like ugly obsolete technology soon after its complete, your developer will leave, and no one will know how to/want to maintain it. Also, obtaining and retaining the necessary developers ends up being expensive compared to the alternative...

### ERPs

The other kind of software that seems like it sticks around forever are big ERP systems. "ERP" or "enterprise resource planning" is a terrible name - it's basically just integrated business software. Some of these products have also been around for decades, but they generally aren't as painful as working with Microsoft Access (some of them are painful - like when they run in Java applets). How do they do it? Well, they constantly come out with new versions that slowly incorporate new technologies. That's not to say these things are cutting-edge; you'll often find a surprising amount of COBOL if you dig deep enough, but they slowly progress and avoid obsolescence. ERP's definitely stay afloat by vendor lock-in, but they also obviously require a much smaller IT staff than if you try to grow your own software. So, they also can make sense economically.

### What Comes Next?

At this point, utilizing ERP software frequently seems like a sensible replacement for developing customized Access programs. They also provide an answer to the question of how long software should last: forever. ERP providers will keep enhancing and modifying their software to keep up with current technology as long as you pay a yearly fee and they stay in business. However, one thing to consider is that it's not obvious that ERP's will always remain an economical choice. If an ERP provider finds that they have a (nearly) captive audience, they have reason to keep increasing subscription rates as long as their customers can find the money.

Anyway, Access programs and ERP's are two opposing models of how organizations can approach their software. In the past twenty years, it seems that we've swung sharply from the former to the latter. I know better than to try and guess where things will go from here. 

I'll leave you with two old but amusing takes on merits of Access:

[Top 10 Reasons Why Access Still Rocks for Developers](http://www.databasejournal.com/features/msaccess/article.php/3862011/Top-10-Reasons-Why-Access-Still-Rocks-for-Developers.htm)

[Top 10 Reasons Why Access Still Doesn't Rock](https://www.brentozar.com/archive/2010/02/top-10-reasons-why-access-still-doesnt-rock/)