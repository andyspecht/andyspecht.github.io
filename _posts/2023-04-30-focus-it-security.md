---
layout: post
title: Where to Focus IT Security at a College
image: ../img/securitycollege/ransomware.png
tags: [security, ransomware, higher ed]
---

Last year I talked about implementing multifactor authentication in higher ed. Today, I want to take a minute to zoom out and look at the bigger security picture for colleges. When considering security improvements, we are sometimes faced with a list of dozens or hundreds of ideas or recommendations for improving security posture. No one has the time, money, or will to implement all of them, so where should you focus? Where are the real security risks for colleges? What kinds of controls should we implement to mitigate these risks?

## Ransomware

At the risk of stating the obvious, I posit that ransomware is the threat that higher ed institutions most need to address. First, it's very common. By one estimate, [44 colleges and universities saw ransomware attacks in 2022](https://universitybusiness.com/no-improvements-schools-were-hit-steadily-with-ransomware-attacks-in-2022/). Based on news coverage, many California community colleges have had significant ransomware disruptions over the last few years ([Hartnell College](https://www.govtech.com/education/higher-ed/hartnell-college-confirms-ransomware-attack), [College of the Desert](https://kesq.com/news/i-team/2023/03/18/college-of-the-desert-paid-1-1m-to-hackers-in-2020-ransomware-attack-doing-more-now-to-cyber-safeguard-its-it-systems/), [Sierra College](https://www.insidehighered.com/quicktakes/2021/05/24/ransomware-attack-takes-sierra-college-line), [Imperial Valley College](https://calexicochronicle.com/2020/08/10/breaking-ivc-reportedly-hit-with-ransomware-attack-systems-impacted/), and [Ohlone College](https://www.peraltacitizen.com/ohlone-ransomware-attack-highlights-vulnerability-of-colleges/) off the top of my head). The impact of a successful ransomware attack on an institution is massive. Part of the mission of any college is educating students and ransomware events may disrupt the ability to hold classes (especially online classes), register students, and process financial aid. In addition, the college may struggle to pay its own employees if their payroll system is affected. Ransomware attacks bring the core functions of a college to a halt and result in a financial loss [at least in the hundreds of thousands and more likely in the millions](https://www.highereddive.com/news/ransomware-surge-education/627305).

## Ransomware Attack Vectors

If we want to stop ransomware, we have to know what is required to successfully execute a ransomware attack. Ransomware requires access to your institution's IT systems. Typically, it involves gaining a foothold in the Windows ecosystem and obtaining the appropriate permissions to move throughout the ecosystem and encrypt files. It is important to note that in practice this malicious access is usually gained remotely. Organizations in other countries are [almost always the initiators of ransomware attacks](https://www.bbc.com/news/technology-60378009), and they're not buying plane tickets to visit your campus. So that means that the core requirement for ransomware is some form of *remote* access. This can occur in a few different ways.

### Publicly-Accessible Systems

Take an inventory of any servers you manage that are exposed to the public internet. If the system on the server is misconfigured, for example there is an administrative account with default credentials or an unsecured guest account, then that's an easy way to get access. There also may be an unpatched vulnerability that allows anonymous access. If you have publicly-accessible systems with significant configuration problems or vulnerabilities, it is only a matter of time before attackers come knocking. This is the very low-hanging fruit to address.

### Remote network access

The alternative to gaining access through a publicly-available system is to find a way into a private system using someone's credentials. The mechanisms for gaining access to a private system remotely is often VPN or remote desktop. Therefore, focusing on hardening those areas makes sense (if you are using them). At a minimum, any kind of remote access should require multifactor authentication and should be adequately logged and audited. In addition, the keys to remote access typically involve usernames/password, so taking measures to ensure that your employees' current credentials are not leaked or very weak is essential.

### Malicious Emails/Websites

The other common way for an attacker to remotely gain a foothold in your walled garden is through malicious emails. If the attacker can convince an employee of the college to download or run something on their computer, this could be used to open a door for the attacker to access your network. By [one estimate](https://blog.knowbe4.com/70-to-90-of-all-malicious-breaches-are-due-to-social-engineering-and-phishing-attacks
), 70 to 90 percent of breaches are due to phishing or other social engineering. There are three different vectors to address this challenge. First, you can configure your email system or use additional software to try to reduce the amount of phishing and other malicious email that makes it into inboxes. Second, you can train your employees and students to be less likely to fall for phishing emails. Finally, you can reduce the damage that malicious emails can do by minimizing the permissions assigned to accounts that log into computers and configuring your computer and networks to make it more difficult to jump from a computer to critical system resources.

## Putting It All Together

In the end, by focusing on ransomware attack vectors we end up with a list of security practices that shouldn't be too surprising: give care to systems that are public facing, restrict and protect remote access, try to prevent phising emails, train your colleagues to detect phishing and social engineering, and make it more difficult to execute malicious scripts and move to other systems. There is of course more to do. In addition to considering the attack vectors of ransomware, you can come up with strategies that focus on minimizing the spread and impact of ransomware if an attack were successful. You can even work with your institution to discuss what it would like if a ransomware attack were *completely successful* and how you would work through that scenario. But by thinking through the vectors that ransomware attacks frequently use, you can come up with a list of security activities that will hopefully have a clear impact on reducing the likelihood of a successful ransomware attack on your campus.






