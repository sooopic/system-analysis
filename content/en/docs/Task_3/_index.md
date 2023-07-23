---
title: "Task 3. Architecture"
weight: 3
description: See your project in action!
---

## Task 3.1

{{% pageinfo %}}
What advantages and disadvantages of microservices do you know?
{{% /pageinfo %}}

### Advantages

* It's **easier to add new functions**, because developers could add another microservice and don't be afraid of
* It's **easier to scale** microservices than monolith application, because you can add new instance independently
* It's **easier to support** small, independent services than one huge application
* Microservises architecture **is more fail-safe**, because even if one service is dawn the others are still functions
* Microservises allows to **implement CI/CD process with different flows** for each services, because each service could be 
* Different teams could use **different technologies and their own prodaction cycle** for each service

### Disadvantages

* It's **hard to design the right set of services**
* It's more **difficult to start developing and testing**, espesially for the first period
* It's **hard to coordinate** a huge amount of services
* There are **extra latency during communication** because microservices uses high level API

## Task 3.2

{{% pageinfo %}}
Depict the top-level architecture of the trading application (e.g. _Tinkoff Investing_ or any similar)
{{% /pageinfo %}}


## Task 3.3

{{% pageinfo %}}
Choose any process that is typical for this type of service and depict its flow in BPMN or sequence diagram
{{% /pageinfo %}}


