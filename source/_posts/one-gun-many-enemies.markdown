---
layout: post
title: "One Gun - Many Enemies"
date: 2013-07-24 14:32
comments: true
author: mbazydlo
tags: [JavaScript, Jasmine, Testing]
---

I spent some time, re-investigating Javascript. After a few months of intensive TDD and SOLID training at OpenTable I was curious how those principles apply in a slightly different environment. Guess what, they do not differ that much... I planned to write about all this sometime in the future after gaining more experience from real battles ahead, however [Watirmelon post](http://watirmelon.com/2013/02/09/why-i-dont-like-Jasmine/) invited me to attack this subject immediately.

I went through the available testing frameworks for Javascript and decided on Jasmine. Why? Mainly because of its BDD syntax which is fashionable this season. The second reason is that it has really cool documentation. Finally, the basic set up of just running an HTML file in a browser got me up and running fast.

Let's look at the basics - what I expect from different types of tests and how Jasmine fits into those requirements: 

Unit Testing
------------

### Advice from senior craftsman

* Shoot them one by one
(structure your code so that it is easier to maintain)
* Be sure you kill with every shot
(verify small bits of code to nail down issues)
* Kill them all
(verify edge cases)
* Kill them quick
(provide fast feedback on issues)
* Choose a fast shooting gun - the faster you shoot, the more enemies you will kill
(unit tests must be blazing fast)
* Choose the most reliable gun - if it is stuck then you are dead
(when unit tests are brittle you will stop depending on them)
* Your gun needs to be light enough to carry everywhere
(unit tests are your gun, so you must be able to run them all locally)

Jasmine works great for all those aims, as it is really fast and it allows you to stub both objects and DOM elements (especially combined with a Jasmine-jQuery plugin). Keep in mind that Unit Testing is useful only if you either write new code or refactor old code. Do not ever try to write Unit Tests for existing code which you don't intend to refactor. It's like shooting the dead. You simply cannot kill them with another bullet.

Integration Testing
-------------------

### Advice from senior craftsman

* Beware of the hidden sniper
(test against external dependencies like files, databases, network services)
* You are part of your squad
(verify that components of the application work well together)
* Observe the environment
(try to use depenedencies as close to the real problem as possible e.g. example file, database with test data, test version of an external service)
* Point in the right direction
(do not try to test your stack top-down, instead concentrate on interfaces and adapters that you could not test in unit tests)

Integration tests will naturally be harder to quickly setup - they might break due to configuration problems or inaccessible external services. That's why you don't want to have that many of them. Still, you want to know that all your problems are due to your partner changing protocol. Once again nothing stops us from using Jasmine here.

Jasmine just executes your tests, so the tests that point towards external services, send example files to your code etc. will all be a breeze to implement with Jasmine.

Acceptance Testing
------------------

### Aims
* Confirm you are fighting the right war
(show specification to your product owner/manager/client)
* Keep clean supply routes
(test the functionality of your app with full set up and all dependencies)
* Find hidden mines
(test your application in all environments to which you deploy)

### Advice from senior craftsman
* Speak in their language
(use a tool which allows writing tests in natural language such as Cucumber or SpecFlow)
* Take your time
(those tests will be slower, so accept that you will not always run them locally and that you will not run them every minute)
* Spend your resources wisely
(acceptance tests are naturally much more brittle then other types of tests, so try to keep their number reasonably small)

This is where I would not recommend Jasmine, simply because it doesn't fit this job. Its syntax is based on programming language, so it is harder to read by non-engineers. Jasmine allows you to execute events and call your code. However, your users will be most likely be using a browser to interact with your code, so it is much better to use a tool that also uses a browser to test your web page.


Conclusion
----------

I find Jasmine extremely well suited for unit and integration testing. I wouldn't use it for acceptance tests as there are quite few better tools for that job. And I guess that is the issue that Alister Scott has with Jasmine on his blog - he tried to use it for acceptance testing. In that case I wouldn't choose Jasmine either. On the other hand I don't like using a screwdriver to hammer a nail.


Post Scriptum (on DOM dependency)
---------------------------------

Alister also complains about integration issues between his server-side code and client-side code. His experience is that changes to IDs and classes in MVC applications result in failing Javascript. The issue is serious and shows one important mistake, which is hardcoding dependencies. IDs and class names are configurable details and Javascript code should be agnostic of them. To illustrate let's look at a simple test case from my pet-project:

	describe("GameController", function(){
	  describe("During Initialize", function() {
	    var view;
	    var subject;

	    beforeEach(function() {    
	        setFixtures("<div id="myid"></div>");
	        subject = new GameView();

	        subject.CreateFabricInDiv("#myid");
	    });

	    it("creates fabric in div", function(){
	        expect($("#myid")).not.toBeEmpty();
	    });

	    it("holds pointer to canvas", function(){
	        expect(subject._canvas).not.toBe(undefined);
	    });
	 }); 

Do you see how "#myid" parameter is passed into the method call? We moved the configuration detail out from the Javascript code, which in my opinion it is the simplest solution to Alister's problem.

If code is designed in such a way that IDs/class names are not embedded all around the codebase you can figure out quite few nice ways to keep them consistent between Javascript and server-side code.

It also helps with code re-usability as you can use the same code on two separate pages with differing IDs!
