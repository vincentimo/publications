# 2019-04-07 Preventing Integration Problems with Continuous Integration (CI)

Migrated from Medium. Original link: https://medium.com/@vincentimo/preventing-integration-problems-with-continuous-integration-ci-1c2094087eba

<!-- toc-gitlab:start mode=full -->
## Contents<br>
1. [Background](#background)
2. [What is continuous integration?](#what-is-continuous-integration)
3. [How does continuous integration improve the quality of the product?](#how-does-continuous-integration-improve-the-quality-of-the-product)
4. [What are common practices in continuous integration?](#what-are-common-practices-in-continuous-integration)
5. [What are the advantages and disadvantages of continuous integration?](#what-are-the-advantages-and-disadvantages-of-continuous-integration)
6. [How can I get started in applying continuous integration?](#how-can-i-get-started-in-applying-continuous-integration)
7. [Conclusion](#conclusion)
<!-- toc-gitlab:end -->

## Background

In a [collaborative project development](<2019-04-03 Enabling a More Collaborative Open Source Project Development.md>), each contribution made by each team member needs to eventually be integrated. However, it is not always easy; we need to ensure that the contributions function together a system. If handled inefficiently, this could turn into a [long and unpredictable process](https://martinfowler.com/articles/continuousIntegration.html), as:

1. each team member might have different practices on writing codes, and
2. the integration process might happen long after the initial contributions were made, so he/she might not remember how their code works when identifying their piece of contribution or solving integration problems.

**Continuous integration (CI) aims to make integration process as painless as possible, which benefits both the team members and their users.** CI puts potential integration problems forward, allowing team members to tackle them at their earliest convenience in small portions. This will in turn speeds up the integration process, and the users can enjoy the product faster without compromising its quality. In fact, CI can even improve the quality of the product with lower cost given [the cost to fix a defect gets more expensive the later you find it](https://searchsoftwarequality.techtarget.com/tip/Continuous-integration-Quality-from-the-start-with-automated-regression).

This article aims to illuminate non-technical readers about the following topics.

1. What is continuous integration?
2. How does continuous integration improve the quality of the product?
3. What are common practices in continuous integration?
4. What are the advantages and disadvantages of continuous integration?
5. How can I get started in applying continuous integration?

It should be noted that this article provides high-level perspective of CI, and the low-level details is linked throughout the article.

## What is continuous integration?

Let’s begin by defining CI.

> Continuous Integration is a software development practice where members of a team integrate their work frequently, usually each person integrates at least daily — leading to multiple integrations per day. Each integration is verified by an automated build (including test) to detect integration errors as quickly as possible. Many teams find that this approach leads to significantly reduced integration problems and allows a team to develop cohesive software more rapidly.
> 
> [Source: Martin Fowler’s page on CI](https://martinfowler.com/articles/continuousIntegration.html)

## How does continuous integration improve the quality of the product?

In CI, automated tests are performed each time an integration is made. Automated tests are tests that can be run without the need of human intervention in a repeatable way, at any time. You typically have to write down a script to test some assertions or validate the behavior of your application. The script is then run by a machine which provides the results as an output. **Automated testing is a key part of CI.**

A team that relies primarily on manual testing may get feedback in a couple hours, but in reality, comprehensive test feedback comes a day, or several days, after the code gets changed. By that time, more changes have occurred, and to perform fixing, team members need to dig through several layers of code to get at the root of the problem. **CI enables faster feedback on code changes.**

By enabling faster feedback, team members can solve integration problems more quickly. **As a result, there is a stable piece of code that works properly and contains few defects.** Less time is spent trying to find defects because they show up quickly.

## What are common practices in continuous integration?

Common practices that make up effective CI are outlined as follows. **These practices will help translate abstract concepts discussed above into actionable items.**

1. [Maintain a single source repository](https://en.wikipedia.org/wiki/Version_control)
2. [Automate the build](https://en.wikipedia.org/wiki/Build_automation)
3. Make your build self-testing
4. Everyone commits to the mainline everyday
5. Every commit should build the mainline on an integration machine
6. Fix broken builds immediately
7. Keep the build fast
8. [Test in a clone of the production environment](https://en.wikipedia.org/wiki/Test_environment)
9. Make it easy for anyone to get the latest executable
10. Everyone can see what’s happening
11. Automate deployment

Learn more about those practices in [Martin Fowler’s page on CI](https://martinfowler.com/articles/continuousIntegration.html#PracticesOfContinuousIntegration).

## What are the advantages and disadvantages of continuous integration?

As with everything else, there are advantages and disadvantages for CI. **Inspect this section to assess whether or not CI is suitable for your project.**

Advantages of CI include:

1. Integration problems are detected early and are easy to track down due to small changes committed for each integration.
2. Codebase reversion to a defect-free state without debugging, if necessary, only loses small number of changes.
3. Constant availability of the current build for testing, demo, or release purposes.
4. Last-minute chaos at release dates, when everyone tries to check in slightly different codebases, are avoidable.

Disadvantages of CI include:

1. CI is not necessarily valuable if the project scope is small or contains untestable legacy code.
2. Value added depends on the quality of tests and how testable the code really is ([Source: Assessing challenges of continuous integration in the context of software requirements breakdown: a case study](http://publications.lib.chalmers.se/records/fulltext/220573/220573.pdf)).
3. Larger team means that new code is constantly added to the integration queue, so tracking deliveries (while preserving quality) is difficult and builds queueing up can slow down everyone.
4. With multiple commits and merges a day, partial code for a feature could easily be pushed and therefore integration tests will fail until the feature is complete.

## How can I get started in applying continuous integration?

CI works best alongside a [source code control system (SCCS)](https://en.wikipedia.org/wiki/Source_Code_Control_System). Here’s a resource you can use to get started in applying continuous integration.

> https://github.com/ligurio/awesome-ci

## Conclusion

This article explored continuous integration (CI) to make integration process in your collaborative project development as painless as possible. Methods discussed in this article are summarized in the following points.

1. CI is a software development practice where members of a team integrate their work frequently.
2. CI improves the quality of the product by enabling faster feedback on code changes.
3. CI common practices are there to help translate abstract concepts into actionable items.
4. The main advantage of CI is the capability to detect integration problems early.
5. The main disadvantage of CI is it is not necessarily valuable if the project scope is small or contains untestable legacy code.