---
title: "Yellow Style Modeling - Let's describe the World in some 2D tables"
date: 2018-03-31T17:13:26+02:00
tags: ["sql", "modeling", "rdbms", "uml"]
categories: ["database"]
---
Some friends asked me

* How to optimize their Business process?
* How could we develop application on domains (finance, medical..) that we don't have knowledge..
* We've got an idea, a business project, how can we materialize it to an application?

These questions come down to "How should we develop / organize / optimize **DATA** in applications?". Developing the **DATA** is a natural way to develop + materialize your idea (or project features). Understand the Business Logic / input + output + process is the first step to optimize them.

In addition, many peoples might think that they understand well their business logic (model), and have a clear idea about what they want.. However, it is not always true, all the confusion and missing information will come up when we question them about theirs DATA.

Ok, let's design our Data, we will grasp some "keywords" about their business, try to create some tables (in Excel or with your pen), maybe putting some data inside so that the tables make sens. Then we start to link some tables together.. Wao, no good, we should move this column to a new tables...etc.. => You are designing your Data Model, right?

As a programmers, you probably learned about:

* Relational Database (RDMS)
* Oriented-Object Programming (OOP)
* Object-Relational mapping (ORM)
* UML schema...

Unfortunately, many programmers don’t know how to apply these theories in the real world.

In less than 30 slides, I will unlock some practical theories, make them more accessible to not only programmers but to everybody:

* The 7 patterns of relation in the slides 2 is your final take away. These 7 simple patterns are enough for you to modelize any kind of super complex Business's data. You don't need to know any other complicate theories.
* Each of the pattern is described in detail with example in the following slides.
* In the end, I give examples about how to use these 7 patterns to design and optimize the DATA for
  * a Library Business
  * a Role-Base Authorization System.
  * a Cosmestics sales Business
* Reader must to pay attention to the colors, the borders of the boxes and the arrows. They are not simply for decoration, but also carry other information. I called these drawing: the "Yellow-style" simply because the color Yellow usually appears the most in these drawings.

{{< gslides src="https://docs.google.com/presentation/d/e/2PACX-1vRM8tkIlYDL-qKrsh2mRUULkoAuMLBpSMIo2vqVVr-Qr1i9JaKTKAtKM0vHfdQK5vaxEUYkAELpwlSZ/embed?start=false&loop=false&delayms=3000" >}}

Just consider all that you will learn as “tips / tricks” or “best practice” to help you organize your data or brainstorming your future project.

I hope you will enjoy it.
