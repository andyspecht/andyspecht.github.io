---
layout: post
title: Sending MFA to College
image: ../img/mfacollege.mfa.jpeg
tags: [security, MFA, 2FA, higher ed]
---

It's 2022: does your school use MFA to protect accounts? If you are looking to implement MFA at your institution or expand its usage, let's talk about some strategies and lessons learned from my experience building out MFA from scratch at a college. The first question you might ask is: do I really need to implement MFA? Yes, at least in some form. It is an essential strategy to prevent account compromises from phishing emails, and phishing and social engineering attacks account for [70% to 90% of all malicious breaches](https://blog.knowbe4.com/70-to-90-of-all-malicious-breaches-are-due-to-social-engineering-and-phishing-attacks). It also serves as a failsafe for accounts with weak passwords that could be discovered in data breaches. MFA, in some form, is a must for any account that you would care about being breached.

## Make the Case

Making the case for MFA to the campus community may be straightforward - it might be required. The insurance provider for the college may require that certain practices are in place to qualify for cyber liability coverage. If you don't implement MFA, you won't qualify for insurance or you'll face higher premiums. If this the case, then you'll likely have some quick allies in the college's senior administration. However, if you don't have such a requirement there are a few other ways to make a compelling case.

### Phishing and Account Compromises

Do the employees and students at your college receive frequent phishing emails? If not, that's great; you're either lucky or doing something right in IT. But you likely have recent, real phishing emails with malicious links that try to steal credentials. MFA is an essential tool for defeating these emails. Even if the attacker were successful in stealing a username/password combination, they would be severely limited in what they could do with it if there is a strong MFA implementation. If you have actual examples of successful account compromises at the college, then it's even easier to explain exactly what MFA can prevent. 

### Security Incidents at Other Colleges

Even if things are going swimmingly when it comes to account security, your college's luck may one day run out. If you search "Ransomware College" in Google News, you'll find some recent examples. Right now, there are articles about ransomware attacks at Ohlone College, Centralia College, and Pellissippi State Community College just in the last month. The message is simple: if it can happen at other colleges, it can happen here as well, and we need to take steps to protect ourselves.

## Start Small

Once you have the buy-in to start rolling out MFA, start with a small group of fairly tech-savvy users who are especially important to protect. Your IT staff are good candidates, as are HR, financial aid, or business office staff. As you implement MFA with them, develop documentation and start addressing any problems that pop up. 

## Make it Convenient

Perhaps the most important factor in a successful MFA deployment is making MFA convenient - or at least not terribly inconvenient.

### Give Options

Most identity systems allow multiple ways to retrieve that second factor (the "something you have"). It could be through an email, text message, hardware device, phone app, or something else. Recently, there has been a lot of discussion about some MFA options being less secure (see [Josephine Wolff's recent article in Slate](https://slate.com/technology/2022/02/google-multifactor-authentication-effective-research.html) or Roger Grimes's [entire book on the weaknesses of MFA methods](https://www.wiley.com/en-us/Hacking+Multifactor+Authentication-p-9781119650805)). In particular, the vulnerabilities with SMS text messaging have been in the spotlight. Here, you're going to have to do a bit of your own research and decide if the potential vulnerabilities outweigh the convenience for the population at your school.

One issue in this area that frequently comes up at colleges is personal cell phones. Usually the most convenient and also some of the most secure MFA options involve downloading a phone app. Most schools do not provide school-issued cell phones, and some employees may balk at putting something work related on their personal phone. One way to accommodate such employees is to give the option for employees to use a physical hardware device like an [inexpensive Yubikey](https://www.yubico.com/product/security-key-nfc-by-yubico/). That way you can head off anyone trying to exempt themselves from MFA on the grounds that it uses a personal device.

### Save Sessions

Most systems don't necessarily prompt every user for multifactor authentication every time they log in. Usually, there is an option to have your browser or device "remembered" for some period of time before reprompting. It could be for 12 hours; it could be forever. The period you set here should partially depend on what the account has access to. If the account has access to sensitive HR data, you probably want to reprompt for MFA more frequently than if it's a student account that doesn't have access to anything beyond their own data. One disadvantage to prompting infrequently is that users may be caught off guard and not have easy access to their second factor, if they are not regularly prompted.

### Use contextual rules

Along with setting the appropriate length for saving sessions, you may be able to set up contextual rules. These are conditional rules that prompt for MFA (or perform some other action) only if certain conditions are met. For example, you might have a rule that always prompts for MFA if someone is logging in from outside of the country. Or you could set rules that check for impossible travel (e.g. logging in from NYC and then logging in from LA 20 minutes later). Some systems can check if the device that is trying to log in is a trusted (for example school-issued) device and can apply additional authentication checks if it's not trusted.

## Conclusion

Those are some pointers for getting started with MFA at a college. I think we often overestimate how much of cultural shift MFA will be. By now, most of us are used to it in our own lives - Google uses it, Apple uses it, you probably even see if you try to log into your cable account on a new TV. It makes sense for a college to take steps to protect the institution and implement MFA.