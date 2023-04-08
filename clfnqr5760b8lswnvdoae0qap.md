---
title: "Book Review: Software Engineering at Google (Part 1)"
datePublished: Sat Mar 25 2023 09:00:39 GMT+0000 (Coordinated Universal Time)
cuid: clfnqr5760b8lswnvdoae0qap
slug: book-review-software-engineering-at-google-part-1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1677947355780/bcb30a20-7507-425a-a378-23a58a6745ac.jpeg
tags: books, engineering, software-engineering, bookreview

---

Let me introduce you to the first article of my book review series. I'll, hopefully, transmit to you the most important lessons learned. Take such review as a [TL;DR](https://www.techtarget.com/whatis/definition/tldr-TLDR#:~:text=TL%3BDR%20is%20an%20abbreviation,t%20read%20it%20at%20all.) on steroids :-D

# Book's purpose

SWE (Software Engineering) at Google retraces the main challenges Google's teams faced over time and how they solved them. It isn't a software development toolkit but a compilation of recipes they use to help create and maintain better products.

The first important thing to note: the online version of this book is publicly available on the [Google Abseil team's website](https://abseil.io/resources/swe-book). Thanks, and enjoy :-)

# Best lessons learned

## Software engineering is not software development

It's a philosophical thought, but the book points out a significant difference between software engineering and development. According to the authors, software engineering corresponds to ***Programming integrated over time***. In other words, it's the art of making your code the easiest to integrate now and to modify in a significant amount of time. The following lessons will help illustrate this principle ;-)

## Pain caused by software upgrades

This is a classic. Everyone knows we should change...but that change is costly, challenging, and hard to reach. You'll likely be integrated into meetings where nobody wants to take the upgrade's responsibility though everyone knows it's necessary. Time goes by and is little by little replaced by panic. The book does not treat the psychological bias involved in that mechanism. However, it illustrates precisely the life spans and importance of upgrades :

![ Figure 1-1. Life span and the importance of upgrades](https://abseil.io/resources/swe-book/html/images/seag_0101.png align="left")

*Figure 1-1 Life span and the importance of upgrades*

I guess you feel the urge you've encountered several times caused by unanticipated (or avoided) upgrades. This chart highlights one specific point: the importance explodes after many years. So, should we plan updates for short-term projects? The answer is ***it might be*** But watch out: if you're incapable of changing, this is extremely risky to hope that this change will never come and never be critical. Over several decades, this will be a painful road to travel.

Why starting upgrades that haven't been planned are so painful? Well...

* Many more hidden assumptions have likely been added since the beginning of the project.
    
* As upgrades aren't familiar, the engineers handling them may lack experience in such tasks.
    
* The more you wait, the more significant the upgrade will be...and the promotion size is often underestimated.
    

What to do then Plan regular, incremental upgrades You'll probably find ways to make them more secure through canary releases, testing, and automation. You'll also gain confidence in modifying your product safely and quickly. Yes, this is a virtuous circle.

## Hyrum's Law

This one is a fascinating concept :

> With a sufficient number of users of an API, it does not matter what you promise in the contract: all observable behaviors of your system will be depended on by somebody.

It makes me think of this famous TikTok video :

%[https://www.youtube.com/watch?v=cUbIkNUFs-4] 

We expect the user to put the shape in its corresponding hole, But some spots were not expected to be a perfect match for many different forms.

There are many parallels we can do: unexpected user paths in our mobile application, wrong forms, etc.

This is an excellent explanation of the anger you can receive when sometimes you fix your API... If you expose a wrong contract but your consumer sticks to that contract, change is costly.

# The cost of change

Over time, you'll find new ways of doing the same things, but probably at a lower cost and more efficiently.

The cost of change will depend on your capability to implement the transition easily and quickly.

The higher its cost is, the most likely it'll be deferred. Here again, you'll probably think about situations you met. Evolutions are delayed until a change is mandatory. Although the cost of change scales linearly, the labor needed does not scale so well:

* The expert you had back in the time left your company.
    
* You need an army of engineers.
    
* You have to meet short delays while you had plenty of time to evolve before...
    

While it'll be harder to change, you'll probably also have much more difficulty estimating the scope involved in the change. This will increase the risk of discovering skeletons in your closet.

## The Churn rule

A simple depreciation policy notifies the consumers of your API that an old version will be removed at a specific date. They should migrate to the new version before the removal. It can work if your consumers don't depend on many APIs that follow the same policy, as migration requires much work.

The book highlights how this doesn't scale well. Instead, they propose the **Churn Rule** :

> Instead of pushing migration work to customers, teams can internalize it themselves, with all the economies of scale that provides

The dependency on the update is inversed: expert teams help customer teams to do the update. This helps avoid a painful and unplanned change.

## The Beyoncé Rule

This rule protects the ability of the infrastructure team to operate changes frequently :

> If a product experiences outages or other problems as a result of infrastructure changes, but the issue wasn’t surfaced  by tests in our Continuous Integration (CI) system, it is not the fault of the infrastructure change.

This forces consumer teams to test the dependencies. They called it the Beyoncé Rule because ***If you liked it, you should have put a CI test on it***, about the singer's super famous song ***Single Ladies (Put a Ring on it)****.*

This rules answers again a scaling problem. If it weren't in place, the infrastructure team would need to track down every group that uses their code.

## Embracing the change

This may be a no-brainer, but the more often you change, the easier it is. The first upgrades are always harder to do, and you must **expect that** Changing your company's culture to embrace change will take time and effort.

## Rejecting "because I said so" as a reason

The authors highlight that Google pushes a data-driven culture for making decisions. Overall, every decision must be justified with arguments, and authority decisions made "because you said so" don't have their place. As they say, unanimity isn't expected, but the **consensus** is. Deciders must be capable of giving a reason when they're asked.

## Confidence helps you save money

Instead of controlling your coworker when they need a pen or any cheap stuff to work, build an obstacle-free environment. For instance, keep a closet full of such supplies that are free to take. This will avoid spending money and time on unlikely and unimportant robberies.

## Be vigilant about your resource consumption

This incentive is particularly actual: our planet's resources are limited. We need to pay attention to our product's consumption and tools.

The authors highlight a paradox I didn't know about before, the [Jevons Paradox](https://en.wikipedia.org/wiki/Jevons_paradox):

> Consumption of a resource may increase  as a response to greater efficiency in its use

As a developer, you must have already heard about someone asking for a new, more powerful computer to compile faster. Or add more RAM and CPUs to some servers to make the web applications run faster.

Most of the time, **this is okay**. But only if you have eliminated the bloat in your tools and software. For instance, upgrading your build system costs less than changing your computer.

## **The Genius Myth**

Software engineering is often about team working, not about being a genius. Few IT jobs require zero interaction with humans :-D.

> What will make or break your career, especially at a company like Google, is how well you collaborate with others.

It's pleasant to see that collaboration is seen as a skill. The more you take responsibility, the more likely your collaboration skills will be valuable. Rhetoric and communication will be determinants.

# Conclusion

This format is about lessons put one after another, so I'll keep this article short. See you for the next part!