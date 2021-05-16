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

These are my notes from Chapter 1 on [Microservice Patterns](https://www.amazon.ca/Microservices-Patterns-examples-Chris-Richardson/dp/1617294543/ref=sr_1_1?dchild=1&keywords=microservice+patterns&qid=1620607913&sr=8-1) by `Chris Richardson`


## Chapter Summary 
- Monolithic architecture pattern results in code that is deployed as single unit. Where as, the Microservice architecture pattern decomposes an application into a set of services, each with its own storage and deployment.
- Monolithic architecture is simple and quick to iterate on at the beginning, making it a good choice for simple application.
- Microservice architecture helps accelerates the velocity of software development by enabling teams to work autonomously on different services.
- In exchange for being able to better scale features and teams, microservices come with a drawback of the complexity.

## 1.1.2 : The Benefits of the monolithic architecture
At the beginning of the startup or project, monolithic architecture tends to offers some benefits, some of which are:

- `Easy to Develop`: Allows devs to focus on developing a single app, which is more aligned with IDE's and dev tools.
- `Easy to make changes`: Because all the code is in one spot, it is easier to implement and deploy big changes.
- `Easy to test and deploy`
- `Easy to scale`: Single deployments means that it is easier to scale the infrastructure ,by either spinning more instances, or picking better hardware.

## 1.1.3: Living in the monolithic hell

Over time, more features and changes get implemented on the monolithic app. This makes development, deployment, scaling, and testing more difficult due to:

- `Complexity`: As more code is added to the app, different modules in the system become more coupled, making it harder to understand and adapt to the changes needed. Additionally, it becomes harder to test these changes.

- `Development becomes more slow`: Larger applications take longer to build, boot up and compile.

- `Deployments is long and audacious`: Similarly larger apps take more time to deploy. Also, there is a higher risk of a production issue since all of the code is deployed on the same service, a faulty deployment can affect the entire app. That is this is a single source of failure.

- `Scaling becomes harder`: Different sub-services might need different resource. For example, an Image Processing service would  probably be more CPU intensive and can benefit from more CPU power. Where as, an in memory cache or database makes more use of the RAM or SSD capabilities and would benefit from boosting these resources instead. You might end up with conflicting needs, thus making it harder to scale the application properly.

### 1.4.1: The Scale Cube

![scale cube](/img/diagrams/scale_cube.svg)

#### X-Axis Scaling
- Also known as horizontal scaling
- Idea is to run multiple identical instances of an application behind a load balancer, this is why sometimes this is called scaling by `cloning`
- A Load balancer distributes the requests across N instances.
- This scaling aims to improve the availability and capacity.


#### Z-Axis Scaling
- Similar to horizontal scaling in that multiple instances are running of the same application.
- unlike horizontal scaling however, Z-axis scales by splitting the data into smaller partitions.
- Each instance holds a subset/partition of the data.
- Data can be partition by using identifying attribute of the data ( e.g. primary key)
- This scales the app by increasing its data volume
- the outcome is better availability and capacity


#### Y-Axis Scaling
- Scaling by functionally decomposing an app into services
- while X and Z scaling address capacity and availability, Y addresses deployments, and development complexity
- This scaling breaks down a monolithic app into a set of services, each implements a focused functionality.

### 1.4.2 : Microservices as a form of modularity

The main idea described in this section, is to decompose applications into modules that are developed and understood by different people.

Microservice architecture uses services as a unit of modularity. The service's API forms its boundary, which is difficult to violate. Because of the strongly enforced boundaries, microservices can help loosen the coupling between different services. 

It's therefore crucial that each service be given its own database to avoid communication / coupling through their databases. Additionally, loosely coupling applications can allow their schema's to evolve independently without impacting other services.

### 1.4.5: Comparing the microservices architecture with SOA

While Service Oriented Architecture (SOA) might seem similar to the microservice architecture, there are some fundamental differences. These differences are summarized in the following table:

| Criteria | SOA | Microservices|
| --- | --- | --- |
| Inter-Service Communication | Smart pipes, such as Enterprise Srvice Bus, using some heavyweight protocols such as SOAP and the other WS* standards. | Dump pipes, such as message broker, or direct service-to-service communication, using lightweight protocols such as REST or gRPC |
| Data | Global data model and shared data-busses | Data model and database per service |
| Typical service | Larger monolithic application | smaller service | 


### 1.5.1: Benefits of microservice architecture

- Enables continues deliver and deployments of large complex applications. Basically, this enables teams to autonomously test and deploy their applications. This takes advantage of loosely coupled applications.
- Services are small and are easier to maintain
- Services are independently scalable. This can help with better tailoring how to 
- It has better fault isolations.
- It allows different services to user newer and more suitable technologies.

### 1.5.2: Drawbacks of the microservice architecture

Microservice architecture isn't without its drawbacks, and they are summarized as:

- Its often difficult to define the right set of services. Decomposing services incorrectly, can lead you into a distributed monolith, as system of coupled services that must be deployed together. This is the worst of both worlds because it is sum of drawbacks from both the monolithic and microservices architecture.

- Distributed systems are complex. There are many aspects that need attention, such as communication, distributed transactions, monitoring, distributed tracing and more.

- Often you can end up with features that span multiple services. The challenge is to orchestrate the development, testing and deployments among the different services.

- Deciding when to adopt microservices is difficult. It's often a task that needs careful planning and can take a while to set the all the pieces together.


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

 In a high-level it looks like :

 ![An overview of Microservice Patterns language](/img/diagrams/ms_pattern_language_overview.svg)



There are subgroups that can be looked at individually

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
