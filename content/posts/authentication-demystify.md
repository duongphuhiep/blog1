---
title: "Authentication Demystify"
date: 2019-11-30T14:54:22+02:00
tags: ["oauth2", "openid", "authentication", "jwt"]
categories: ["security"]
---

In the process of revamping an old (classic) PHP application to a modern Single Page Application, I need to re-write the Authentication module.

This slide is my research on common questions / knowledege about Authentication:

* How the Authentication happened in the old PHP application?
* How it handled expired users, and keep active users connect?
* What is the Oauth2, OpenId
* JWT
* What is 2-Factor authentication? How to implement it?

The most important part is my **Refresh-token strategy**. The "Refresh Token" automatic renew the tokens of active users so that they will stay connect.
I come up with a **stateless Refresh-token strategy** so that nothing need to be stored or keep track of on the server side (You can go straight to the Slide 14)

{{< gslides src="https://docs.google.com/presentation/d/e/2PACX-1vS4J6oCC-MLEu6mL1V8g065it2IxugrhJplTHYyx149kSoXlHd6YaGl9VVP6xoFT_OeqVgFG8VQY6wy/embed?start=false&loop=false&delayms=3000" >}}

<!--
<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vS4J6oCC-MLEu6mL1V8g065it2IxugrhJplTHYyx149kSoXlHd6YaGl9VVP6xoFT_OeqVgFG8VQY6wy/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
-->