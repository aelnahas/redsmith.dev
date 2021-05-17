---
title: "Circuit Breaker Pattern Part 1: A Brief Overview"
date: 2021-05-10T17:01:59-04:00
draft: false
tags:
  - patterns
  - circuit-breaker
  - mircoservice-patterns
  - microservices
---

I recently read [Microservices Pattern](https://www.amazon.ca/Microservices-Patterns-Examples-Java/dp/B07ZFZ4SJM/ref=sr_1_1?dchild=1&keywords=microservice+patterns&qid=1621115846&sr=8-1) by Chris Richardson. The book highlights the different aspects of designing a microservice architecture. Some of the topics discussed include service decomposition, service communication, handling transactions, and CQRS ( Command Query Responsibility Segregation).

The `Circuit Breaker Pattern` is one  of those patterns presented to help improve the resiliency of your service communication. I thought it would be an interesting pattern to develop and talk about. I wrote a [golang implementation](https://github.com/aelnahas/circuitbreaker) of it, and in this blog, I will be describing an overview.

## The Use Case

Suppose that you have two services: the `Order` and `Payment` services. Furthermore, suppose that whenever a new order is submitted to the `Order` service, it calls an API on the `Payment` service, for instance to complete a payment. 

How can these calls fail? Well, for one, if the service has an availability of say 99%, then that means there is a 1% chance of the requests failing every now and then. These types of failures are sort of expected, and can be worked around with strategies such as retries. 

However, what happens if the `Payment` service suddenly goes down? For instance, it could happen due to a bad deployment, bug in the code, or a database issue. Such failure, is not only unexpected, but can also last a while. Retrying the request wont help here, since the failure might last for a long while.In fact, if each of the calls to that service blocks until a response is receive, you might find your service in situation where all of its resources have been exhausted waiting for the service recover. In other words, a failure in a upstream service just brought down your own service. So, in short this can cause a cascading failure among other systems.

One way to work around this, is to avoid making calls to the Payment service for a while, and opting instead to failing fast or returning some cached value. A similar situation to this would be a major construction in a road, so major that it blocks the entire road. In this situation, rather than allowing further traffic into this road and cause a potential traffic backups, it would be better to block entrance to that particular road from the entrances. You block the roads until it has been cleared, and only then bring back the traffic. That is the same idea behind the `Circuit Breaker` pattern.

## Overview 

![State Machine](/img/diagrams/state_machine.svg)

At The core of the circuit breaker pattern, lies its state machine. Its responsibility is to determine when requests can be executed, particularly the state of the breaker. In the diagram above, we can see a circuit breaker utilizes 3 states, the closed, open, and half-open states.

**Closed State** : This is the normal state, during which request are being executed.

**Open State**:  In this state the breaker is "open", and is no longer letting the requests go through, it stays in this state for some timeout duration.

**Half-Open State**: This is kind of a hysteresis state. The idea here is preventing the circuit from immediately switching back to the closed state when the target service hasnt recovered yet.

### State Transitions

Next, some thoughts about the transitions between states.

#### Closed -> Open

When the target service starts to fail "too many times", the circuit breaker would switch from closed, accepting request, to open, denying request. I will be writing articles about the implementation of these criterias,however, here is a brief overview of some of the different ways to build the failure decision:

- **Counting Successive Failure**: One way of determining your failure threshold, is to count the number of consecutive failures. Once it goes over a target, say 10 consecutive failures, then you open a request.

- **Sliding window Count based**: Another way is to calculate the failure rate based on the previous N requests, where N is fixed. Basically, we create an array with size N, and collect the metrics from the previous calls, and then calculate a rolling average. The underlying assumption here is that all previous requests have the same weights. 

- **Sliding Window Time based**: Similar idea here, we calculate a failing rate based on previous requests. However, we want to give more recent request a heavier weight than older ones, since they are more representitive of the service's state.

#### Open -> Half-Open
Once the breaker is open, it stays there for a period of time. I called this in my implementation as `CooldownDuration`. Once the cooldown duration has expired, then the breaker automatically switches to the `Half-Open` state.

#### Half-Open -> Closed

Our main in goal in the half-open state is to determine whether or not the target service has recovered. We can do this by allowing a portion of the request to go through while denying the rest, for instance 10 requests. We gather metrics, and check to see if the succes is acceptable. We can do this by verifying that the successesful responses are above some threshold, or alternatively if the failure drops below a threshold. If the requests seem to be succeeding, then we can assume that the service has reccovered,and proceed to close the circuit.


#### Half-Open -> Open

If the requests in Half-Open keeps failing then we can conclude that the service hasn't recovered yet, and can send the circuit breaker back to the open state for another cooldown duration.


## Conclusion

`Circuit Breaker` pattern, is  pattern that helps services build resiliency in their communication, particularly by avoiding the need to invoke a service that is down. The main idea is to have a state machine to keep track of the state of the circuit breaker open, closed, or half-open state. In the next article, I will be going over the logic of calculating the overall failing rate to determine if the breaker needs to open.