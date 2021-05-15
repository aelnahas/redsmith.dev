---
title: "Circuit Breaker Pattern Part 1: A Brief Overview"
date: 2021-05-10T17:01:59-04:00
draft: false
tags:
  - patterns
  - circuit-breaker
---

I recently read [Microservices Pattern](https://www.amazon.ca/Microservices-Patterns-Examples-Java/dp/B07ZFZ4SJM/ref=sr_1_1?dchild=1&keywords=microservice+patterns&qid=1621115846&sr=8-1) by Chris Richardson, where he discussed many aspects of the microservice architecture including development, decomposition, distributed transactions, and CQRS. 

The `Circuit Breaker Pattern`, is a pattern that is that help improve failure resiliency.I thought to write this blog to give an overview and discuss the use case. In future articles, I hope to dive deeper in some of the implementation details.

## The Use Case

Suppose that you have two services the `Order` and `Payment` services. Futhermore, suppose that whenever a new order is submitted to the `Order` service, it calls an api on the `Payment` service, for instance to complete a payment. 

What can possibly go wrong here ? Well, if the services has an availabiliity of 99% that means that there is a 1% chance of it failing every now and then. These types of failures are sort of expected, and can be worked around with strategies such as retries. 

However, what happens if the `Payment` service goes down unexpectedly. For instance, due to a bad deployment, bug in the code, or a database issue. Such failure, is not only unexpected, but can also last a while. Retrys wont help since the failure might last for a long while. In fact, if each of the calls to that services blocks until a response is receive, you might find your service in situation where all of its resources have been exhausted waiting for the service recover. In other words, a failure in a downstream service just brought down your own service. So, in short this can cause a cascading failure among other systems.

One way to work around this is avoid making calls to the Payment service for a while, opting to either failing fast or returning some cached value. In fact, A similar situation to this would be a major car accident in a road, so major that it blocks the entire road. In this situation, rather than allowing further traffic into this road and cause a potential traffic backups, it would be better to block entrance to that particular road from the closest intersections. You block the roads until it has been cleared and only then bring back the traffic. That is the same aim behind the `Circuit Breaker` pattern.

## Overview 

![State Machine](/img/diagrams/state_machine.svg)

The core of the circuit breaker pattern is its state machine, which helps to determine when requests can be executed. In the diagram above, we can see a circuit breaker utilizes 3 states, the closed, open, and half-open states.


#### Closed State

This is the normal state, during which request are being executed.


#### Open State

If the requests fails "too many times", then the circuit breaker breaks open. During this mode no requests are forwarded for a duration of time. The idea here is give the callee or the impacted service a time to recover.

####  Half-Open State

Once the `Open` state cooldown duration expires the circuit breaker switches to the `Half-Open` state. The idea here is give the circuit breaker a way to test the water by only executing a fraction of the normal requests ( say 10% of the requests get executed and the rest are denied). If those requests are successful, then we can close the breaker and resume normal operations, otherwise go back to the `Open` state and wait for additional time.

This is done to avoid the situation where we go immediately to the `Closed` state and start running the requests only to find out that the service hasn't recovered yet. 

## When should the circuit breaker open ?

There are a few ways to implement the logic. I plan to discuss them in further details in future blogs, but to briefly discribe them :

- Keep a count of successive failures, if it goes over a certain amount, then consider the service to be down and open up the circuit.

- Instead of counting the failures, calculate a threshold. The first way is to using a sliding window of fixed size ( say in the span of last 100 requests gather the metrics and compute the failure rates ).

- The Fixed width sliding window inheritently assumes that all past request have equal weight, but in reality that may not be the case. That is, more recent requests is probably more indicative on the status of the service than older requests.
