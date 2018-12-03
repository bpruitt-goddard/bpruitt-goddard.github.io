---
layout: post
title: Pitfalls of Ignoring Tests with Testing Frameworks
category: Testing
tags: [testing]
---

*This is a repost of the entry written on the [TrackAbout Blog](https://corp.trackabout.com/blog/pitfalls-of-ignoring-tests-with-testing-frameworks/)*

Recently there was a [pull request](https://github.com/trackabout/speceasy/pull/14) on SpecEasy, our open-source unit testing framework, for implementing an `IgnoreSpec` attribute to allow test methods to be ignored during test runs. This spurred a discussion on whether it was *necessary* to even provide the ability to skip tests.

## Basis
All major testing frameworks provide the ability to ignore tests, from [NUnit](http://www.nunit.org/index.php?p=ignore&r=2.2.10) to [MSTest](http://msdn.microsoft.com/en-us/library/ms182457(v=vs.100).aspx) to [XUnit](http://xunit.codeplex.com/wikipage?title=HowToUse). But that doesn’t mean we should automatically follow suit. Instead we challenged the feature as XUnit did when [doing away with the `Expected Exception` feature in favor of `Assert.Throws`](http://xunit.codeplex.com/wikipage?title=Comparisons#note1) and decided against adding it. 

## Why Ignore Tests?
There are 2 outcomes for a test during a test run. The test can either succeed by passing all assertion(s) or fail at some point before or during assertion checking. In the typical setup, these tests are either run locally or on a build server. If any tests fail, then the build fails, forcing the developer to fix the failed tests. The purpose of this failure is to emphasize that the code is not behaving as it should. Either the code needs to be fixed or the test needs to be adjusted to redefine the "normal" behavior.

There are rare cases where it doesn't seem feasible to simply "fix" the tests. For example, the test may be failing due to a build server configuration problem (*works on my machine*), the change may require a massive refactor that there isn't time for, or it may be dependent on third party code that needs fixing. It might seem like it is best to ignore the test for now and fix it later when there is more time or a dependency has been fixed. Doing so will get the build back to normal and everyone can continue working.

## Repercussions
The problem with ignoring a failing test is that it creates at least two bad scenarios. In the first scenario, the test code has the problem, and it is not validating "normal" behavior. In this case, it is good that the test is not succeeding as that would mean the code is behaving incorrectly. The more critical issue with this scenario is that leaving behind an ignored test may cause an intrepid developer to try to fix the code so that the bad test passes. This is especially harmful if the test was for a deprecated feature.

The second, and even worse scenario when ignoring a test happens when the test code is correct but the implementation is incorrect or requires refactoring. In this case, the code was or should be working, but that guarantee provided by the presence of the test is now broken, defeating the purpose of having the test in the first place. If more changes are pushed out before the code has been fixed, they might unknowingly further break this feature or behavior.

In both of these cases, confidence in the code is lost and any changes are more prone to introduce more bugs into the code. Additionally, simply fixing the code will indirectly get harder with ignoring a test as there will now be a time gap between realizing the test was broken and finally working on it, requiring all of the associated information to be re-remembered or discovered. By not allowing this behavior, we are forcing the issue to be addressed as it crops up, without simply pushing it off.

## Inevitable Exceptions
As with all rules, there are bound to be legitimate exceptions. For this reason, SpecEasy retains the ability to ignore tests, just not in a fully supported feature. SpecEasy picks up test methods by looking for all `public void` methods. To ignore one of these methods, simply change it from `public` to `protected` and the test(s) will be not be run. This workaround will not integrate in any way with test runner's ignored test reporting; it will simply not be picked up by SpecEasy. By not building out an ignore feature, we hope to quell most (if not all) of the urges to ignore a failing test.
