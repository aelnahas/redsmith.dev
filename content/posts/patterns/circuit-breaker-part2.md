---
title: "Circuit Breaker Part 2 -- Calculating Rates With Fixed Size Windows"
date: 2021-05-25T18:59:10-04:00
draft: false
tags:
  - patterns
  - circuit-breaker
---

This is a continuation of [Part 1]({{<ref "circuit-breaker-part1.md">}} "Part 1").  In this article I will be going over the `Fixed Size Window` approach, which allows the circuit breaker to observe the results of the requests, and calculate a rolling `failure rate`.


### Fixed Size Window

![Buffer](/gif/buffer.gif)

This implementation uses a circlar buffer of fixed size N. The buffer is used to store the result from the previous N results. You could decide on what sort of metrics or dimensions you need to record, for instance saving the outcome of each request.

Additionally, we need to keep track of the overall aggregates somewhere. This aggregate can later be used to compute the ratio of the requests that fail to the total requests executed (aka failure rate). As the we start recording new results, the buffer begins to fill up with these measurements. Eventually, when it is full, we start to wrap around and overwrite older results. When overwriting previous results, we also need to remove their contribution from the overall aggregate.

Below is an example of `Golang` struct that describes a `FixedSizeGauge`, which is the structure to store these metrics:

```golang
    // FixedWindowGauge collects and aggregates outcomes from previous requests
    // It has a fixed window size N. If requests happened more than N requests ago then they get
    // evicted from the gauge window, and are removed from the total aggregate.
    type FixedWindowGauge struct {
      windowSize     int
      // pointer to the latest measurement
      head           int
      // a series of recordings of the outcome of each request
      measurements   []Aggregate
      // a total aggregate that tally the requests, failures and successes so far 
      totalAggregate *Aggregate
    }
```


Futhermore, the struct implements the `Gauge` interface which is defined as:
```golang
    // Gauge provides an interface to logging and aggregating request results, mainly will be used
    // by the state machine to be determine which state it is in
    type Gauge interface {
      // Log outcom of a new request
      LogReading(Outcome)
      // Get Aggregate results so far
      OverallAggregate() Aggregate
      // Reset the gauge
      Reset()
    }
```

To log a reading we generally need to do the following:
1. Move the head of the window over one spot
2. record the outcome into the Total Aggregate
3. Overwrite the old measurment with the new one and remove its contribution from the total aggregate. 

Finally, I used another struct `Aggregate` to keep a record of all the counts:

```golang
// Aggregate is used to keep track of the current performance of the outbound requests
type Aggregate struct {
	// Keep track of total requests in the snapshot
	RequestCount int
	// Keep track of requests that have failed
	FailureCount int
	// Keep track of request that succeeded
	SuccessCount int
}
```

You could expand on this list, to include things like `ResponseTime`, but I kept it simple for now.

I have also opted to use `Aggregate` struct to store individual results. I felt that it is a reasonble thing to view these entries a sub aggregates based on each individual request, and that we have a total aggregate which is the sum of the previous N aggregates. This will allow to describe other strategies in the future.


To see the details of the implementation please checkout the [guage file](https://github.com/aelnahas/circuitbreaker/blob/master/circuitbreaker/gauges/fixed_window_gauge.go), and [fixed window gauge](https://github.com/aelnahas/circuitbreaker/blob/master/circuitbreaker/gauges/fixed_window_gauge.go)

### Pros

In general the code is pretty straight forward once you get the idea,  making it an easy way to calculate a rolling failure rate. Since the size is fixed, that means that you can limit its memory impact. 

### Cons

One potential pitfall with this approach is that we dont take time into consideration. That is, metrics in the buffer could have been collected a long time ago. This means those measurments are no longer a good representation of the service's current state.

One possible mitgation, is to keep the window size sufficiently small. While this does not completely eliminate the risk of stale measurements, it does increase the chances of those measurements being wiped out soon, and thus lowers the chances of stale measurements.

### Conclusion

In general, a fixed size window is an approach that would allow us to compute the rolling failure rate of the previous N requests in an efficient manner. It is possible to adapt a similar approach with a time sensitivity enhancement ( older requests are removed as time goes by). I will present that idea in another article.