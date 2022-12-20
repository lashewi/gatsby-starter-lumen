---
template: post
title: "A Note by Olórin: London vs Chicago in TDD"
slug: Let's begin by defining the terms used in this topic. On Test Driven
  Development (TDD), there are two schools of thought.
socialImage: /media/pexels-dominika-gregušová-672532.jpg
draft: false
date: 2022-12-18T13:25:06.626Z
description: |-
  A Note by Olórin is an article series based on my Engineering journey.
  A Note by Olórin: London vs Chicago in TDD
category: SOFTWARE ENGINEERING
tags:
  - TDD
  - Engineering
  - Programming
---
Let's begin by defining the terms used in this topic. On Test Driven Development (TDD), there are two schools of thought. **London style**, also known as Mockist Style, Top-Down, or Outside-In, is about working from the outside of the application inward to the lower layers. **Chicago Style**, also known as Detroit, Classic school, Bottom-Up, or Inside-Out, is characterised by starting from the inside of the application (usually the domain) and working outward to the APIs.

The London style is distinguished by providing a behaviour-based approach to TDD that begins outside of the application and works its way down to the persistence layer. For example, starting with the API/Controllers and working your way down to the application or domain layers. Because the team began outside of the application, this approach is more based on creating mocks to make it work, hence the name, Mockist style. Mocks are simple to use and inexpensive, but only if the behaviour does not change frequently.

Chicago style is more concerned with state changes and the return value, which is why it is also known as the Inside-Out approach. This introduces incremental complexity, and the team can test Integration from the beginning of development. Because there are fewer mocks, the system becomes more evolvable. You may, however, over-engineer your product at some point.

TDD practice is praised in both London and Chicago for its benefits, which include consistent incremental progress, simplicity of design, and code quality. They do, however, provide distinct guidelines for achieving TDD nirvana. it is not a matter of picking one over the other. It's about understanding your quality model and driving optimisation toward the qualities that need to be optimised; choosing one over the other isn't the point.

\
I﻿f you are looking for more resources,

1. <https://martinfowler.com/articles/mocksArentStubs.html>
2. [https://www.youtube.com/watch?v=_S5iUf0ANyQ&ab_channel=ContinuousDelivery](https://www.youtube.com/watch?v=_S5iUf0ANyQ&ab_channel=ContinuousDelivery)
3. <https://softwareengineering.stackexchange.com/questions/166409/tdd-outside-in-vs-inside-out>