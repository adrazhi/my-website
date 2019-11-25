---
layout: post
title: On Good Security Advice
---

##  Distinguishing good security advice (for Developers)

As a software engineer you get a lot of security advice. 
What might surprise you is that the advice you receive 
can be useless or slow you down tremendeously. 
Often times we speak of this friction that exists 
between developers and security engineer and one of the 
greatest skills you can gain as a developer is the ability 
to distinguish good security advice from bad security advice. 
Doing so will give you the power to gaze out into the future 
and make smart decisions. 

But what is good advice? What do we mean by advice? 
Fundamentally, it's a recommendation for how to proceed in 
the future. For example: "use vault to store your secrets", 
or "make sure you properly validate user input" or "make sure
to sign and verify docker images to prevent MITM attacks" etc. 
Good security advice relies on two things: 
- Good understanding of how the ecosystem works (technologies, CI pipeline, deployment etc)
- The circumstances of a parcitular piece of code, service, technology that is the target of the advice. 

Most people think their intuition can help separate the good advice from all the poor advice when they get it. This is not
good advice about advice. 

*Use your intuition to ask questions, not to answer them*

As a developer, you must train yourself to understand what characteristics make "good" advice and how you shoudl employ it 
when you recognize it. 

## Characteristics of good security advice
Good security advice is often based on a casual theory, in 
other words, good advice in based on a theory that explains 
*what* and *why*. 
Casual theory is theory that exhibits 
both of these two characteristics. First, it alawys identifies a casual mechanism. Advice then must leverage this casual mechanism to make predictions about the future. 
The second characteristics of theory that yields good advice is that it is *circumstance-based*. 
To form the basis for good advice, our casual theories must define the various situations in which 
one may find oneself and predict specific outcomes based on each situation. 

One of the most common mistakes about security advice, is that it is not clear about the 
circumstances in which it applies. Think for a moment the advice given by consultants, bankers, 
or management academics: It's broadly defined so frequencly need to backtrack it with "it depends"
or "time will tell". If you hear such terms from a Security Engineer and when you push them on 
their recommendations they cannot specify what "it" depends upon, then you know you're getting bad advice. 

Good advice stems from understanding a specific circumstance, identifying the desired outcome, 
and discerning what action you must take as a developer to reach the outcome based on an 
established casual mechanism. *Example*: If you're running a suite of microservices build on top of 
GCP, AWS, Azure etc and you have to comply with certain compliance requirements, it's often a good idea
to go through these requirements and understand how you can map them into technical items. However even 
the most inexperienced Security Engineer would not advise you as a developer to go throuch each and 
every one requirement even if the desired end goal is that the requirements are somehow translated into code. Why? Because the Security Engineer's understanding of the developer's circumstances and a casual 
theory (if the developer will go through each and every one of the requirements it will be unproductive and chance something important may be missed are high), allow the Security engineer to dispense such bad advice. 

## Spotting Bad Advice

If good advice is any advice that is based on circumstance-specific, causal theory, then advice that lacks either an underlying causal mechanism or regard for circumstances is, by and large, bad advice. 

There are three flags to look for:
First, sages - people eager to bestow their wisdom upon others - will give advice lacking causality. Instead, they support their assertions only through correlation. With vast amounts of data at their fingertips, most sages cannot resist running complex regressions with hopes of maximizing r2, or the relationship between two characteristics they observe in companies. The problem with the results of these lengthy, data-driven studies is that those who use the results confuse correlation with causality. Correlation merely explains that two things can be observed together. Without being armed with a causal theory of the world, these sages cannot actually leverage their observations to predict which of the two things causes the other.

The second issue that arises from correlative research is that correlation analyzes attributes, not circumstances. When sages identify correlative relationships, they cannot know with
any certainty whether those relationships apply to your specific circumstance. Yet when strong, attribute-based correlations are established, sages offer advice similar to “9 of 10 successful organizations do this; therefore, you should do this.” This is not good advice. Good advice always takes into account your specific circumstance, not merely the average circumstance of all companies.

The third way to know whether to trust advice, where it applies, and where it does not, is to examine where the boundaries around the circumstances are mutually exclusive. And as the body of theory becomes more complete, the set of circumstances in which you might find yourself step by step become collectively exhaustive.

## Conclusion

As a software developer often times you will hear Security engineers tell you how something 
should be done. In better security-oriented engineering cultures, as a software developer you'll 
be lucky enough to be able to work with security engineers that have built security tools so that 
hopefully they're easy to integrate in your CI/CD pipeline. What we should actually strive for is building tools that do things in a secure way, so that they can be used by engineers on different circumstances. 




