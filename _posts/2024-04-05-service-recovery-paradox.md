---
layout: post
title: The Service Recovery Paradox and Invisible Technology
image: ../img/serviceRecoveryParadox/computer.png
tags: [Service Recovery Paradox, Kill it with Fire]
---

I recently finished Marianne Bellotti's book *Kill it with Fire: Manage Aging Computer Systems (and Future Proof Modern Ones).* It covers the unglamorous topic of what to do when you find yourself managing a system that is a bit long in the tooth and hasn't been properly maintained for years or even decades. The lessons discussed apply most directly to organizations that both run and create their own software, but there are important lessons for those of us who simply deal with patchworks of commercial software -  especially for those of us in higher education, where nearly every student information and ERP system was designed decades ago.

One section that gave me pause was in the chapter "Breaking Changes." Bellotti argues that system failures can result in *building* trust with your organization, if you are able to recover gracefully. Your organization will trust you more than if your systems never failed at all. This is because systems that are too reliable are taken for granted and become invisible to their users. This phenomenon is called the "Service Recovery Paradox." 

The empirical evidence for this paradox occurring in the wild is mixed, but it did make me question something that I had often thought about technology: that the goal is to make technology invisible. Good technology is often described as "seamless", an adjective that describes a lack of visibility. Seamless technology is a step on the way to invisible technology: technology so reliable that its existence is taken completely for granted and a natural part of the environment that no one would give an explicit thought to. After all, no one wants their technology to be intrusive. Really good technology becomes a part of yourself or at least an extension of yourself. But invisible things tend to get ignored or taken for granted. So it would not be a surprise that a system that is reliable almost all of the time and clearly communicates when there is an outage and recovers quickly, might be viewed more favorably than a system that one does not give much thought to at all. Bellotti's discussion made me a little less afraid of system failures. It's not a bad thing to bring an ERP down for needed upgrades. It's ok to implement changes that may result in outages, as long as the outage is quickly recoverable and brief.

The problem with software so reliable that it is invisible is you might also forget that it is there. That'll be fine for awhile but eventually it will become a system that is not upgraded and maintained...and that is a system that will become prone to significant failures - the kinds of failures that could undermine trust in the system. That brings me to a second lesson that Bellotti imparts: do hard things more often. Replacing a critical database server will be a lot easier for you staff to accomplish if you replace it every two years vs every six years. It's easier both because your staff will have more practice doing it and because you will likely have to address fewer accumulated technology platform changes. As a result of a faster change cadence, it will be a lot less risky to replace your database server and you won't be tempted to put it off as long as possible.