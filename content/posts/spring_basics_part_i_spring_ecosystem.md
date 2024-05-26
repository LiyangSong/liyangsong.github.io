---
title: "Spring Basics Part I: Spring Ecosystem"
date: 2024-05-23T16:29:45-04:00
description: "Basic introduction to Spring and Spring ecosystem, the most widely used Java framework today."
tags: [Spring, Java, Backend, System Design]
featured_image: "/img/spring.jpg"
images: []
categories: Spring
comment: true
draft: false
---

## Introduction

**_Spring_** is the most used Java framework today. 

An application framework is a broad set of common tools and software functionalities that can be used to develop apps. However, not all the features of the framework need to be used, programmers should choose the right components depending on the requirements of the app and know how to assemble them to achieve the right result.

The idea of frameworks comes from the observation that many applications have similar requirements and often reuse parts of non-business code, such as 
- logging
- data transfer
- security
- communications
- caching or data compression

In contrast, the business logic code (what implements the users' expectations and what differentiates an app from another) in an app is significantly smaller the engine (plumbing) of the application. 

Reusing these already-used code can save time and money, has fewer chances to introduce bugs, and get advices from a large community of developers.

&nbsp;

## Spring Ecosystem

Spring is not just a framework but an ecosystem of frameworks, which includes several parts of software capabilities such as:
- **_Spring Core_**: One of the fundamental parts of Spring, including features such as Spring context, Spring aspects, Spring Expression Language (SpEL), etc.
- **_Spring model-view-controller (MVC)_**: Used to develop web applications that serves HTTP requests and use a standard servlet fashion.
- **_Spring Data Access_**: Used to connect to SQL databases to implement app persistence layer, including using **_JDBC_** integrating with **_object-relational mapping (ORM)_** frameworks like **_Hibernate_**.
- **_Spring Testing_**: Used to write unit and integration tests for Spring apps.

![spring_core.png](/img/spring_core.png)

&nbsp;

#### Spring Core

Spring works based on the principle of **_inversion of control (IoC)_**, which allows the Spring framework (dependencies) to control the app code and executions rather than the app's own code and dependencies. The term "control" means actions like creating instances or calling methods.

Within the IoC container (**_Spring Context_**), Spring controls instances added to the container by intercepting methods that represent the behavior of these instances, which is called **_aspecting_** the method. This way of programming is called **_aspect-oriented programming (AOP)_**.

&nbsp;

#### Spring Projects

Spring ecosystem also includes a big collection of projects, which can be used together to develop an app. Such as:
- **_Spring Boot_**: Offer a default configuration that follow known conventions and can be customized as needed.
- **_Spring Data_**: Enable connection to both SQL and NoSQL databases and use the persistence layer with a minimum number of lines of code. Note **_Spring Data Access_** is a module of **_Spring Core_**, while **_Spring Data_** is an independent Spring project.
- **_Spring Security_**

&nbsp;

#### Spring Applications in Real World**

- Backend App: Backend applications execute on the server side and have the responsibility of managing data and serving client applications' requests. The client apps make requests to the backend app to work with users' data, and the backend might use the databases or communicate other backend apps.

![spring_back_end.png](/img/spring_back_end.png)

- Automation Test: Automation testing refers to implementing software that development teams use to make sure an application behaves as expected. It can be scheduled to frequently test the app and notify the developers if something is wrong. It is always a good idea to automate test cases for small systems, and it is even not an option to manually test all flows in complex systems. The most efficient solution is to have a separate team implement a testing app that validates all the flows, yet the app could still benefit from Spring's tools.

![spring_automation_test.png](/img/spring_automation_test.png)

- Desktop App: A desktop app could successfully use the Spring IoC container to manage the object instances and other Spring's tools.
- Mobile App: Spring Android project provides a REST client for Android and authentication support for accessing secured APIs.

However, there are also some scenarios that frameworks should be avoided:
- For quite small apps, especially deployed in containers, it makes more sense to improve their initialization and make their footprint (size of app) smaller.
- Apps in defense field or government organizations may become vulnerable with open-source frameworks.
- Apps that need to be customized so much that programmers end up writing more code than if a framework hadn't been used.
- Switching to a new framework might not bring a benefit with time and money invested.

&nbsp;

## Summary

Spring is not just a framework but an entire ecosystem formed of various functionalities and many projects, with each of them dedicated to a specific domain. Using Spring can help building apps more efficiently and open a door to a large community. However, always think of possibilities when implementing an application including not using a framework and alternatives to frameworks.
