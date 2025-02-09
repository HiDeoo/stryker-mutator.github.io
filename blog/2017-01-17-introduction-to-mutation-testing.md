---
slug: introduction-to-mutation-testing
title: Introduction to mutation testing
author: Simon de Lang
author_title: Stryker Team
author_url: https://github.com/simondel
author_image_url: https://avatars1.githubusercontent.com/u/4221997?s=460&u=d09f7c27690d66764ff2f2ebb6d9f8d5431ad9e3&v=4
tags: [stryker-js]
---

Writing unit tests can be hard. Even for an easy example such as a calculator app.
There are unlimited combinations of inputs for a calculator and you're not going to test every single one. So when are you done testing?

<!--truncate-->

Say you're building an online casino, users can only enter the casino when they're over 18. So you write the following piece of code to check if someone is allowed to use the website:

```javascript
function isUserOldEnough(user) {
  return user.age >= 18;
}
```

Easy right? So what inputs do you use to test? Perhaps you would pick your own age (25 for example) if you're over 18 and pick something below 18 (16 or so) to check the other outcome.
You run your code coverage tool on the code and it turns out your code is 100% covered. Great, we're done testing!

## The code coverage problem

I used to think that code coverage tells you which code is tested. But I realized I was wrong. Code coverage doesn’t tell you which code is tested, it only tells you which code is being executed during testing. There is an important difference here. You could write one test which hits 100 lines of code, but that doesn’t mean that all 100 lines of code work as you would expect. If you would write 10 tests for that piece of code, you may still have the same code coverage. You could even write tests without assertions to boost your code coverage.

And how do you know that enough is enough? Some teams even have code coverage goals that they have to hit (100% anyone?). As if that would make you write better tests. Sure, it forces you to write tests, but it doesn’t promise that they’re good tests. It even encourages bad behavior for some people. During one of my projects as a student, our school decided a little competition would be fun. The team with the highest code coverage at the end of the project would win a small prize. It didn’t take long to find out that tests could be written without assertions to easily boost their code coverage (of course, I would never do that ;)).

The only way to know that a test actually _works_ is when it fails when you make a code change.

## Mutation testing

You could go through your code base and make changes to see if certain tests fail. However, this process is time consuming. You're much better off using a mutation testing framework. A mutation testing framework will look through your code to see where it can introduce bugs, which we will call _mutants_ from now on.

Say you have the following code:

```javascript
function isUserOldEnough(user) {
  return user.age >= 18;
}
```

A mutation testing framework will find the return statement and decide to change it in a number of ways:

```javascript
/* 1 */ return user.age > 18;
/* 2 */ return user.age < 18;
/* 3 */ return false;
/* 4 */ return true;
```

Each one of those mutants should make a test fail. After the mutants have been found, they are applied one by one and your tests will be executed. If at least one of your tests fail, we say the mutant is _killed_. If no tests fail, it _survived_. The better your tests, the less mutants survive. The first mutant would have survived if we had a test with a user aged 16 and 25, since we didn't test the boundary.

With mutation testing you’re testing your tests.

## Getting started

The best thing to do if you’re not already using mutation testing is to just try it once. Most languages have a mutation testing framework.
Just take a look on GitHub and you will probably find one for the language you use.

If you’re using JavaScript, I would suggest looking at Stryker. It has a great [quickstart page](http://stryker-mutator.github.io/quickstart.html) to get you up and running.

If you've first used a mutation testing framework, you may find that there are a lot of mutations that survive, but that’s ok!

## Setting goals

After you’ve used a mutation framework a couple of times, you may think about setting a mutation testing goal,
just like with your code coverage goal. I sometimes get asked what a good goal is for mutation testing.
It can be a difficult question to answer and I honestly don’t know what a good goal is for your application.

Mutation testing frameworks make very different changes depending on the language or even framework you’re using.
Stryker could assign any value to any variable since it tests JavaScript. That doesn’t work for a framework like [PIT](http://pitest.org/),
which can be used to mutation test Java code. This means that your score could differ a lot depending on the language and framework you use.
The goal you want to set may also differ depending on the type of application that you're writing. A banking application may require a higher mutation score than some side project.

I would suggest running a mutation testing framework and set a higher goal than your current score.
I would not advise to make 100% mutation coverage your goal though. The most important thing is that you write good tests and mutation testing can help you achieve that.
So if you’re going to set any goal, make writing good tests your goal.
