---
author: Andrey Maksimov
pubDatetime: 2021-12-16T15:57:52.737Z
title: Component and Feature teams
postSlug: component-and-feature-teams
featured: false
draft: false
# ogImage: https://user-images.githubusercontent.com/53733092/215771435-25408246-2309-4f8b-a781-1f3d93bdf0ec.png
tags:
  - release
description: AstroPaper with the enhancements of Astro v2. Type-safe markdown contents, bug fixes and better dev experience etc.
---


<!-- ---
pub_date: 2021-12-16
---
title: Component and feature teams
---
twitter_handle: meamka
---
_discoverable: yes
---
_slug: component-and-feature-teams
---
meta_description:

Каждая продуктовая компания в процессе своего развития сталкивается с вопросом о том, как структурировать команды разработчиков. Мы рассмотрим 2 варианта: с помощью компонентных команд и, наоборот, фиче-команды. 
---
summary:

Каждая продуктовая компания в процессе своего развития сталкивается с вопросом о том, как структурировать команды разработчиков. Мы рассмотрим 2 варианта: с помощью компонентных команд и, наоборот, фиче-команды.  -->

Historically at [more.tv](https://more.tv) we work in teams formed around the principle of a single platform on which they work. The project in their case is not all _sea_, but part of it: backend, iOS, web, etc. Each team has strong typing and owns a limited set of languages ​​for development, as well as a limited set of opportunities to influence the entire product, however, can solve tasks narrowly focused on their platform most effectively. There is no faster way to solve a big backend problem than with a team of backenders. However, in the real world, often large tasks for several developers of the same specialization exist only at the start of the project when it is necessary to prepare the basis for further work. Months after the launch, as a rule, the tasks are reduced to solving product problems involving several platforms, but having a smaller volume for each of the platforms than before. There is a need to revise the development structure as a whole.

## Component commands

![Component and feature teams](/assets/components-and-feature-teams/komponentnaia-i-ficha-tima.jpg)

Let's consider a situation: you are developing a fast delivery application and you want to add the ability to automatically determine the geolocation of a client. In most cases, such a task can be solved as part of the development of a client application, because it knows where the user is at a given time - this even works for the web. In this case, a component team is quite suitable - a team responsible for a dedicated layer of service, whether it be a client application, a single backend, or a set of services - a team. This approach has both pros and cons. In particular, the component team allows you to build the application architecture consistently, respecting its integrity within the framework of solving different tasks, since it is controlled by the technical lead and team developers. It is easier to find developers in such a team, which is also important in the process of growth.

However, there are also disadvantages, for example, a single team resource introduces problems in the distribution and prioritization of tasks, complicates communications, which, in turn, increases the delivery time of tasks that require the involvement of several teams.

## Feature teams

Feature teams (see the figure above) are kind of the opposite of component teams: they are made up of people who can solve business problems end-to-end. Continuing with the delivery application example, let's imagine that you need to identify the nearest free couriers and show them on a map in the application so that the client can evaluate how quickly the courier is found. Such a task is no longer within the power of one component team because its implementation requires work both from the client-side and from the server-side: courier applications need to send their location and availability status, and the server needs to synchronize this data between the courier and client applications.

If we solve this problem with component commands, we need:

- predetermine the formats of interaction between applications
- implement server-side
- improve courier applications
- add a map of couriers to client requests to the server

At the same time, most likely, work on the last two points will be carried out sequentially, because the mobile development team is one, and there are two tasks.

Fichatima, on the contrary, will allow you to solve such a problem much faster than several component teams: it already has everything you need for implementation: mobile developers, backend developers, designers, testers, and a customer represented by a product manager. So, you will get a significant acceleration: developers do not need to plan work, they can solve emerging issues immediately when they arise because they are all busy on the same task. Such teams can take tasks directly from the backlog, because they do not need preliminary study, they can carry it out themselves and bring new features to the user as soon as possible.

Cons for such commands are opposite to component commands:

- several teams can implement the same functionality both in parallel and at different times
- each team is free to make their changes to the architecture of applill be carried out sequentially, because the mobile development team is one, and there are two tasks.

Fichatima, on the contrary, will allow you to solve such a problem much faster than several component teams: it already has everything you need for implementation: mobile developers, backend developers, designers, testers, and a customer represented by a product manager. So, you will get a significant acceleration: developers do not need to plan work, they can solve emerging issues immediately when they arise because they are all busy on the same task. Such teams can take tasks directly from the backlog, because they do not need preliminary study, they can carry it out themselves and bring new features to the user as soon as possible.

Cons for such commands are opposite to component commands:

- several teams can implement the same functionality both in parallel and at different times
- each team is free to make their changes to the architecture of applications, thereby complicating its control and, in the end, complicating the applications themselves,
- in the worst-case scenario, this approach may introduce an inconsistency in the user experience and interface, which also requires additional control.

## Resource pool
ications, thereby complicating its control and, in the end, complicating the applications themselves,
- in the worst-case scenario, this approach may introduce an inconsistency in the user experience and interface, which also requires additional control.

## Resource pool

![Resource pool](/assets/components-and-feature-teams/resursnyi-pul-i-proektnye-komandy.jpg)
 
The scheme is suitable for large development teams and frequently changing short-lived tasks: for each specialization in development, we recruit a certain amount of developers - the same resource pool. Each pool is managed by a direction team leader who is responsible for the available resources. Customers, when a task appears, come to an architect or a team of team leaders, who, according to the initial analysis of the task, calculate the necessary resources for the implementation of the functionality and choose the most effective direct people for solving a specific task: both of those who are available in the present current moment, and from those who can be released shortly from other teams.

The advantages of this approach are quite obvious:
- the most effective developers are selected to solve problems
- teams contain all the people needed to achieve the goals

The main disadvantages in this case are:
- the need to maintain a sufficiently large pool of developers for each specialization
- as well as the difficulty of motivating employees when waiting for tasks and switching between projects

## more.tv approach

Initially, we built the work on the principle of component teams, this approach is familiar to everyone and has proven itself well in situations where you need to build a large service in a short time, and the business requirements were completely different than now: it was important to work out the architecture of the service, lay in it opportunities for horizontal and vertical growth.

However, at some point it became clear that working with component commands, despite all its clarity and simplicity, does not bring us the result in speed that we want to achieve. We have reduced the release cycle, switched to feature testing, implemented CI / CD, and achieved significant improvement, but we want more :)

- C'mon, Andrey, tell us how you have it built now?

Now we are on the path of experimenting with feature timings and have not completely gone away from component teams yet. Although our first attempt was not a failure, the result is not so impressive as to run and redo the entire structure at once, so we decided to continue the experiment and organize a second-team, finalizing the metrics that we want to collect and adjusting our expectations. Let's see the results in a couple of months :) 
