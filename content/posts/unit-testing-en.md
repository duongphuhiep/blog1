---
title: "Unit Testing"
date: 2018-11-01T00:06:10+02:00
tags: ["c#", "java", "xunit", "test"]
language: "english"
categories: ["backend", "test"]
include_toc: true
---
A long time ago, a certain CTO asked me to write unit tests for a legacy application in order to increase the [Sonar rating](https://docs.sonarqube.org/latest/user-guide/metric-definitions/).. I did all I could, and hardly covered up to 40% of the codes. My "unit tests" that I wrote can run only if the database was well configured with some specific data.. Many "unit tests" depend on the server of other teams..

At that moment I didn't realize that the task was absurd.., that what I wrote weren't unit-tests at all. Recently, a similar story happens for other friend and I realize that there are still many (experienced) developers / CTO misunderstood about unit testing. They thought about Unit Test as an easy side task, an unimportant task for trainee..

So I decided to write down my experience about Unit Testing, to tell you how hard it is to write a unit tests.. and so it explains why don't many developers write them…

I will use C# just for a typical technology example. However this article is applicable for just any application in Java, Ruby.. as well. (Just replace the word "C#" in this article with "Java" or "Ruby", or "Python" or whatever..)

To put it bluntly…

## Unit test = A test for a "specific function" in a "C# class"

**Each time you write a new function (method) in a class => you have to think about How will you (unit) test your new function?** Some developers evens adopt [TDD](https://en.wikipedia.org/wiki/Test-driven_development) - an extreme process which encourage you to write the tests even before writing the function.

A unit test is not a test scenario which requires database connection or web-service connection.

* If you need a database or a network connection in order to run your unit test => sorry it was not a **unit test**, you are trying to run an **integration test**.
* Many "Unit test framework" [xUnit](https://xunit.net/), [nUnit](https://nunit.org/).. certainly can be used to run integration tests or complex tests scenarios evens if they might not be the best tool for this job. The fact that you are using the xUnit / nUnit.. (launcher) to run your tests is not mean that your tests were **unit tests**..

## Who should write unit-test for this function?

Of course, the writer of the function should write the unit test for it. You should write unit test for functions that you created.. When you refactor things, you will have to also refactor all the related unit-test as well.

## It's impossible to write Unit test for this function!

It is a very common case: "I'm creating a new function (method), I think about how will I unit test it… impossible! My function depends on the database or other web-services in order to do the job => Unit test cannot cover my function".

For example: A `Rabbit.Jump()`function gets some data from an external WebApi, it only makes a **small calculation** (1 line of codes). Finally, it saves the result to the Database. Most of the codes is to do I/O => How could we suppose to unit test this thing?

```
class Rabbit {
  void Jump() {
    call external Web api // I/O codes - non-unit-testable
    small calculation // unit-testable (1 line of codes)
    save result to database // I/O codes - non-unit-testable
  }
}
```

You are right and also wrong. Unit test cannot cover 100% the function but it still can cover for eg: 10 or 20% of the function! What are you trying to **UNIT-Test** in `Rabbit.Jump()`?

The unit test doesn't suppose to test the external API or to test the connection to the database. It suppose to test everything else. In this case, you will have to write unit test to cover the **small calculation** (the 1 line of codes) in the `Rabbit.Jump()`.

**It is not an easy task**.. writing a unit test to cover this one line of codes which is surrounding by I/O codes. It usually requires to (heavily) refactor codes in order to separate **unit-testable** codes from **non-unit-testable** codes. The common way is to isolate I/O codes into separate classes and call them via interfaces / abstract class, in order to make the I/O codes "mockable". (This task requires much experience of the coder. I might demonstrate in another post..)

If you asked a junior to "add more unit test" to a "traditional" codes which the writer didn't think about Unit testing when they wrote all these codes, then it is absurd...

## Is it worth? what's the point?

To be able to unit-test just one line of the **small calculation**. We'll have to go through all the refactoring-fuss.. Writing unit test often requires a lot of refactor, mock... the test codes often (far) longer than the actual codes, and we will have to maintain all of them. => I dare to say that **writing codes with Unit test usually require triple or evens 4x the normal efforts compare to the "traditional way of coding"** (code without thinking about Unit tests).

So, is it worth to write unit test? My answer is "Yes" for most of cases..

In "traditional way of coding" (without thinking about Unit test): (junior) developers often cram everything into a **big function**. They hesitate to split the codes or to create new files / new classes / new functions.. because they don't have (yet) the habit to do so and they also don't know when we should split or merge the codes... In the **big function**, they won't hesitate to call other static functions in other modules, it is comfortable and feel natural.. So we should [just make everything static](https://codeburst.io/static-classes-are-evil-or-make-your-dependencies-explicit-af3e73bd29dd).. :)). Of course, the big function could work very well in application, and the (junior) developer get the job done, bravo...

Now, if you ask them to make unit test for the **big function**.. it is the dead-end! Their codes had not been wrote to run in the **Unit tests (poor) context** (no database, no network connection...) The **big function** codes could only run in the context of the full fledged application with all the database, external services, and configuration in place.. Each time you change something in the big function, you will have to run the entire application, wait for some circumstances (events) happening so that the changing codes would be run, and you can test them… You will have to do it over and over again..

In order to make the big functions unit-testable, the function (and all the application) will have to go through major refactoring to **separate Unit-testable codes from Non-unit-testable codes**. If you have the guts to go through this major refactor your codes usually become far better organized and far more flexible. **They become less dependent on the database and external services. Because all the I/O codes will become replaceable** (or "mockable")

**So the worth of Unit Testing is not really the sonar-rating or the test coverage percentages, but the boosting in the codes quality.**

At a glance in the codes, you can tell whether the writer has thought about unit-testing or not. In the "think-about-unit-test" codes, you usually see more interfaces / classes which will do the I/O tasks. In the "not-think-about-unit-test" codes, you usually see I/O codes mix with Business Logic codes and the usage of static everywhere.

## 100% test coverage is not possible with only Unit tests

You've gone through hellish refactor in order to add more unit test, but depend on the applications nature. Your Unit tests can only cover 30% of your codes at best… (it is just an example)

=> Don't feel disappointed! Your unit test covered only the internal codes of the application, 70% of your codes was for I/O (read / write database, call external services…) because the nature (the mission) of your application was to do I/O, the internal business logic (computation) are few, so it is very normal that your Unit tests could only cover this much.

For example: a "routing" application which have to listen to requests, forward them (as-is) to other services, then forward the response back to the original requester.. The unit test can hardly cover up to 5, 10% for this type of application: it can only cover the codes which compute the route..

In the other hand, if I/O is only a small part of your application, most of the application codes was to do complex computation, business logic ( if/ for/ while..), then the Unit test must to cover much more codes.. => if it wasn't the case for you => then it is the "smell" that your application architecture was bad… that there was many business logic mixing up with I/O codes and blocking you from unit testing them.

## How to achieve 100% test coverage

Let's say your Unit tests have covered 30% of the computational codes; the other 70% was for I/O codes. In order to cover for them, the only way is to run **Integration tests**: You must to have a test database, all the test (or mock) external services up and running then carefully program the tests scenario.. These "eco-system" must to be maintain and evolve with the integration tests and it is also not an easy task, it is evens trickier than making unit tests.

Today's trending is [Docker](https://www.docker.com/) which will help you build an environment "on the fly" each time you want to run the integration tests. The integration tests should be able to cover all the codes which has been covered by Unit tests. So it is common that developers go straight into writing the integration test and don't care about unit testing anymore.. (unit tests cannot cover 100% of the codes anyway). The funny thing is that they are still thinking "I'm writing Uni tests with Docker".

So is it worth writing Unit Tests anymore? My answer is still "Yes" for most of case because it still help to boost the codes quality.. Don't under-estimate the 30% codes coverage of your unit tests.. It cover for the most core part of your application and also mean that you can test one-third of your application without the needs to maintain a test database or depend on external service.. and it is amazing!

## Conclusion

Writing tests is hard, it might evens harder than writing the actual codes that you are testing. Maintaining the tests codes are not easy, maintaining the Integration tests are evens trickier and often require manual actions.. Please don't take them lightly or give this task to an inexperience developers.

The guys who wrote the codes should also write the test and think of a strategy of how to test their codes as easily as possible.. Don't just write the codes irresponsibly then throw them to a junior or the QA team and make them write tests for your codes… while evens yourself have difficult time to test them.

In fact while you are writing a function, if you think about how you will write test for it, how can you execute your function in a Unit tests "poor" context (without database, without network connection), then you will naturally refactor / split / re-arrange your function to adapt and got a higher codes quality.
