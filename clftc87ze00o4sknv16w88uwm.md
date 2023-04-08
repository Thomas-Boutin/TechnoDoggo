---
title: "Book Review: Software Engineering at Google (Part 2)"
datePublished: Wed Mar 29 2023 07:00:39 GMT+0000 (Coordinated Universal Time)
cuid: clftc87ze00o4sknv16w88uwm
slug: book-review-software-engineering-at-google-part-2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1679836011524/e6ced454-fc7e-44e5-807b-331516bb8cad.jpeg
tags: books, software-engineering, book-review

---

This article is the second of my book review series. Part one is [here](https://technodoggo.hashnode.dev/book-review-software-engineering-at-google-part-1). This second part will focus on the next best lessons learned.

# **Next best lessons learned**

### The bus factor

This one is widely known :

> Bus factor (noun): the number of people that need to get hit by a bus before your project is completely doomed.

It's deeply connected to SPOF (Single Point Of Failure): you must know about people who can't be replaced. At worst, because they're dead. But this observation must be made for trivial events: holidays, illness, strikes, etc. You have to manage your team and design your product to avoid the doom of your project.

### Learn to give and take criticism

This part starts with a funny comparison about our politicians and the feeling that they never admit when they're wrong :-D

The point is, in IT, you don't always have to be on the defensive. On a side note, I'd say that admitting your errors strengthen your leadership. **Everyone mostly shares the same goal in a team**. So, to be a friendly teammate :

* Don't back-seat: don't interrupt discussions to offer your opinion without committing to the discussion
    
* Pay attention to the expression of bias (racism, ageism, homophobia, seniority, gender...). Avoid, for instance, sentences like "This one is an easy one" when someone comes for help.
    
* Play the mentor role. Help juniors grow in their hard and soft skills. Code reviews are such important moments.
    
* Celebrate the success of your mates. Forward what's been nicely done by a mail, a slack message, etc. Everyone should feel legitimate to work in your team.
    

### **Engineering for Equity**

This part relates to the 2015 Google Photo issue: the classification algorithm used to treat black people as gorillas. That was a shock. Google's engineers built a best practice on that incident :

> Design for the user who is least like you

This starts by embracing diversity in your workforce. If you need to build for everyone, you need heteroclite teams. You must develop your product **with** your users, not **for** them. This means choosing different population categories to ensure your algorithms are prompt to accept diversity. Designing for the user who'll probably have the most difficulty using your product helps find issues. Finally, your approach should be supported with metrics to avoid biases.

### **Grow your team as an individual contributor**

Once you're senior enough, you'll often face the choice of coding something quickly or delegating the job to someone else, **knowing this will be executed slowly.**

The second option is best: you can't manage everything alone, and you need your team to grow. [Pair programming](https://martinfowler.com/articles/on-pair-programming.html) is a great option to consider. Remember that as an individual contributor, your departure should not bring chaos to your team.

### **Climbing the career ladder**

At Google, the next job on the career ladder is tried for several months by the candidate. This helps detect bus factor issues, but above all, the candidate can be sure that the next job will be great.

### **Asking questions as a manager**

Instead of providing immediate solutions, ask the right questions, so the person that contacts you finds the key by herself. That's even more important for newcomers as they learn your company's culture and the responsibility they'll assume. Being a good mentor helps them feel safe in your workplace.

I liked the idea of asking what your team members need to track their happiness. Asking regularly "what do you need" during the one-ones will eventually trigger a list of things that could make everyone's job better.

### **The cycle of success**

In this part, the authors theorize the cycle which leads to success:

* Analysis: you receive a problem to solve. You identify the blinders, the trade-offs, and build consensus on how to manage them
    
* Struggle: you start to work on the issue and face failures, retries, and iterations.
    
* Traction: this is the moment where you make better decisions and start seeing nice results
    
* Reward: you're congratulated on the job accomplished
    

They conclude that the reward for success is more work and responsibility. I would say that constantly pushing to the limits is harassing, and that's typically an attitude that a manager should be aware of. Many (most?) people aspire to **a bit** of challenge but not constant pressure.

### **Prioritizing**

Simple rule: only 20% of the requests are urgent. 60% should be arbitrated, and 20% are somehow useless. The difficulty is to identify what requests are critical and be able to deny or postpone the others. How? You can typically start to ask how urgent the demand is. Resist also processing your boss' request before all the others. They won't likely always be critical.

### **Taking a step back about metrics**

As underlined in previous lessons, Googlers make decisions by creating and analyzing metrics. Yes, every company is not Google. However, I like the focus taken on avoiding biases and suppositions.

### Codebase openness

The more open your codebase is, the more likely it'll be safe and secure. More people will look into it and be able to propose fixes. I'll comment there that direct contributions by customer teams aren't the easiest culture shift to achieve. However, openness gives more feedback to product teams and helps customer teams to find ways to fix faster the encountered problems.

# **Lessons about testing**

I want to highlight specifically the lessons about testing. In my opinion, this book offers an excellent compilation of testing best practices.

### Testing benefits

There still is a widely spread and liked saying about testing: testing is doubting. To me, this shows how testing is sometimes feared, some others mocked...and others, again, it's hard to know where to start.

The book starts by explaining, according to the authors, why there is no evil in testing :-)

First, it should provide less debugging to the engineers as automated maintenance systems help detect defects on their behalf. If you link automatic rollbacks to those detections, you dramatically minimize the risk of failure. The more people work on the same product, the more relevant it is.

Second, it gives you confidence in your project. You're less afraid of change as the main behaviors are continuously checked. Refactoring is made easy. If you only change an implementation, no test should be changed.

Third, it improves your documentation. Good tests serve that purpose. They're well-written and give examples of how your product behaves.

Fourth, they make the review simpler. Reading the implemented tests, you should find understanding someone else's code easier.

Fifth, it gives a thoughtful design. Most of the time, when some code is hard to test, the design is wrong. Loose coupling, modularization, and many other technics help achieve an excellent design and an easy testing setup.

Sixth, with tests, come fast and high-quality releases. As teams have high confidence in their products, they ship many updates fast.

### Code coverage

As the authors say:

> Code coverage is a measure of which lines of feature code are exercised by which tests

When you analyze code coverage, you typically express it with a percentage measure. I agree with their constatation: it's often seen as a silver bullet. Let's be honest: high code coverage does not prove anything. It's easy to trick the analysis by making useless tests exercise large portions of your codebase. However, a low code coverage shows that your testing strategy is incorrect or incomplete.

When teams set a given percentage, let's say 90%, the temptation is big to reach that goal with bloated tests. It's also tempting to deactivate the analysis if your build pipeline is interrupted because the test coverage doesn't match the threshold.

Another approach consists in defining with the team what tests must be written and if enough tests are in place. Of course, this requires honesty and a deep understanding of the critical business functions the product covers.

### Unchanging tests

As said above, ideal tests should not change as long as the expected behaviors don't either. This means that whether it's about fixes, refactoring, and new features, you should strive to write stable tests in those conditions. When your tests constantly change, **they're wrong**. They should bring you value with no additional maintenance. Like your code, your tests should be well-designed. If you feel that they're hard to write. They may be poorly designed as well. Take a look at the famous article [Mocks aren't stubs](https://martinfowler.com/articles/mocksArentStubs.html). Understand the difference between [the mockist and the classicist school of testing](https://medium.com/@adrianbooth/test-driven-development-wars-detroit-vs-london-classicist-vs-mockist-9956c78ae95f). The pathway to correct testing isn't a piece of cake, but it's passionate :-)

### Well written tests

Tests should be concise and meaningful. They should be DAMP :

> Descriptive And Meaningful Phrases

In other words, it's better if the name of your test function is long but descriptive than short and with less sense. The function's body should only contain the relevant information of your test. Minimize the setup with the appropriate parameters. I should be understood why they're needed to explain how the test works.

### Test sizes

Googlers distinguish three test sizes:

* Small: they should be isolated and fast (for example, unit test, few milliseconds to complete)
    
* Medium: less isolated, slower (for example, integration tests, few seconds to execute)
    
* Large: even less isolated, even slower (for example end to end tests, few tens of seconds to several minutes or hours)
    

Large tests exist to address the need for fidelity: they are more brittle, but usually, they reflect more on the actual behavior of the product. As they're closer to reality, investing more in this kind of test is tempting. This way, you'll fall into the Software Testing Ice Cream Cone AntiPattern.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680041313364/306cdc9d-53b9-4a7b-8a5d-4a0abf0de350.png align="center")

This schema is the mirror version of the test pyramid, a famous and controversial testing strategy. This is an antipattern because it isn't scalable. The higher you are in the cone, the longer it takes to execute. As they're slow and fragile, those tests will take days or weeks (!) to run. If they don't cover a defect, you'll likely experience interruptions because you won't have enough automation to roll back and push a fix fast to production.

# Conclusion

As I've come across many technical lessons, I decided to finish this book review with a third and last article. I hope you've enjoyed this one! See you for the final part ;-)