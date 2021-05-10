---
title: "Microservice Pattern Chapter 1: Escaping the Monolithic hell"
date: 2021-05-09T13:51:20-04:00
draft: false
categories: readings
tags: 
 - microservices
 - readings
 - mircoservice-patterns
---
## Introduction 

These are my notes from [Microservice Patterns](https://www.amazon.ca/Microservices-Patterns-examples-Chris-Richardson/dp/1617294543/ref=sr_1_1?dchild=1&keywords=microservice+patterns&qid=1620607913&sr=8-1) by `Chris Richardson`, on Chapter 1.


## Chapter Summary 
- Monolithic architecture pattern produces code as a single deployment unit. Wheras, the Microservice architecture pattern decomposes an application into a set of deployments, each with their own storage.
- Monolithic architecture is simple and quick to iterate on at the beginning, making it a good choice for simple application.
- Microservice architecture helps accelerates the velocity of software development by enabling teams to work automonously on different services.
- In exchange for being able to better able to scale features and teams, mircroservices come with a drawback of the complexity.

## 1.1.2 : The Benefits of the monolithic architecture
At the beginning of the startup or project monolithic architectural approach offers some benefits, some of which are:

- `Easy to Develop`: Allows devs to focus on developing a single app, which more aligned with IDE's and dev tools
- `Easy to make changes`: Because all the code is in one spot, it is easier to implement and deploy big changes.
- `Easy to test and deploy`
- `Easy to scale`: Single deployments means that it is easier to scale the infrastructue , by either spinning more instances, or picking a better hardware.


## 1.1.3: Living in the monolithic hell

Over time more features and changes are being implemented on the monolithic app. This makes development, deployment, scaling, and testing more difficult due to:

- `Complexity`: As more code is added to the app, different modules in the system might become more coupled, making it harder to reason with and adapt to the changes needed. This in turn also makes it hard to test the changes.

- `Development becomes more slow`: Larger applications take longer to build, boot and compile.

- `Deployments is long and audous`: Similarly larger apps take more time to deploy, there is also more impact for errors due to it being a single source of fault.

- `Scaling becomes harder`: Different sub services might need different resources. For example, an Image Processing piece might need more CPU power, where as an in memory database might need more memory capabilites. Conflicting resource needs makes it harder to scale the application.


### 1.4.1: The Scale Cube

![scale cube](/img/diagrams/scale_cube.svg)

#### X-Axis Scaling
- Also known as horizontal scaling
- Run multiple identical instances of an application behind a load balancer
- Load balancer distributes the requests across N instances.
- This aims to increase the application's availability and capacity.


#### Z-Axis Scaling
- Similar to horizontal scaling in that multiple instances are running of the same application.
- unlike X-Axis , Z-axis scales by splitting the data into smaller partitions.
- Each instance holds a subset of the data.
- Data can be partition by using identifying attribute of the data ( e.g. primary key)
- This scales the app by increasing its data volume
- the outcome is better availability and capacity


#### Y-Axis Scaling
- Scaling by functionally decomposing an app into services
- while X and Z scaling address capacity and availability, Y addresses scaling deployments, and development complexity
- This scaling breaks down a monolthic app into a set of services, each implements a focused functionality.

My thoughts: where does vertical scaling ( aka scaling the hardware ) sit with this cube.


### 1.4.2 : Microservices as a form of modularity

The main idea described in this section, is to decompose applications into modules that are developed and understood by different people.

Microservices architecture uses services as a unit of modularity. The service's API forms its boundary that is difficult to violate. Because of the strongly enforced boundaries, microservices can help loosen the coupling between different services. 

It's therefore crucial that each service be given its own database to avoid communication / coupling through their databases. Additionally, loosely coupling applications can allow their schema's to evolve independently without impacting other services.

### 1.4.5: Comparing the microservices architecture with SOA

While Service Oriented Architecture (SOA) might seem similar to the microservice architecture, there are some fundamental differences. These differences are summarized in the following table:

| Criteria | SOA | Microservices|
| --- | --- | --- |
| Inter-Service Communication | Smart pipes, such as Enterprise SErvice Bus, using some heavyweight protocols such as SOAP and the other WS* standards. | Dump pipes, such as message broker, or direct service-to-service communication, using lightweight protocols such as REST or gRPC |
| Data | Global data model and shared data-busses | Data model and database per service |
| Typical service | Larger monolithic application | smaller service | 


### 1.5.1: Benefits of microservice architecture

- Enables continues deliver and deployments of large complex applications. Basically, this enables teams to autonoumsly test and deploy their applications. This takes advantage of loosely coupled applications.
- Services are small and therefor easier to maintain
- Services are indpendently scalable. Different services can have different needs,such as CPU or Memory. Because they have segregated infrastructure, it is easier to tailor its scaling needs.
- It has better fault isolations.
- It allows different services to adopt newer or more suitable technologies.

### 1.5.2: Drawbacks of the mircoservice architecture

Microservice architecture isnt without its drawbacks, and they are summarised as:

- Its often difficult to define the right set of services. Decomposing services incorreclty, can lead you into a distributed monolith, as system of coupled services that must be deployed together. This is the worst of both worlds because it is sum of drawbacks from both the monolithic and microservices architecture.

- Distributed systems are complex by design. There are many aspects that need attention, such as communication, distributed transactions, monitoring, distributed tracing and more.

- Often you can end up with features that span multiple services. The challenge is to orchestrate the development, testing and deployments among the different services.

- Deciding when to adopt microservices is difficult. It's often a task that needs careful plannig and can take a while to set the all the pieces together.


### 1.6: The microservice architecture pattern language

### 1.6.2: Patterns and Pattern languages

A `Pattern` is a reusable solution to a problem that occurs in a particular context. Patterns can be valuable because it must describe the context with which it applies. It forces you to describe other critical yet frequently over looked aspect of the solution.

#### Commonly used structure to describe patterns
- `Forces`: describes the issues that you must address when solving a problem in a given context. While you may have conflicts and may not be able to solve all of them, forces can help with prioritization.

- `Resulting Context`: describes the consequences of applying a pattern. It consists of three parts:
    - Benefits
    - Drawbacks
    - Issues

- `Related Patterns`: Describes the relationship between the pattern and other patterns. 

There are 5 types of relationships between patterns:
    - `Predecessor`: a pattern that motivates the needs for this pattern
    - `Successor`: a pattern that solves an issue introduced by this pattern
    - `Alternative`: A pattern that provides an alternative solution
    - `Generalization`: A pattern that gives a more general solution to the problem
    - `Specialization`: A special form of a particular patterns 
### 1.6.3: An Overview of the Microservice architecture pattern language

 The Microservice Arch. pattern is a collection of pattern that helps with architecting and design microservice systems. 

 The pattern language consists of several groups of patterns. 

 In a highlevel it looks like :

 ![An overview of Microservice Patterns language](/img/diagrams/ms_pattern_language_overview.svg)



There are subgroubs that can be looked at individually

- `Patterns for decomposition`: Address how to decompose the business into a microservice architecture. There are two primary patterns:
    - Decomposing by business capability
    - Decomposing by business domain


- `Communication Patterns`: Organizes ways to communicate between services and covers:
    - `Communication Style`
    - `Discovery`
    - `Reliability`
    - `Transactional messaging`
    - `External API`

- `Data consistency patterns for implementing transaction management`
    - Address how to maintain consistency especially when trying to carry out a transaction across multiple systems. The `Saga` pattern is an example of a solution.

- `Patterns of querying data in a microservice architecture`
    - with services each having a database, we need a reliable way to execute queries across the different services.

The rest of the chapter previewed the topics in the upcoming chapters
