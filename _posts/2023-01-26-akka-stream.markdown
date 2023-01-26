---
layout: posts
title:  "Akka Stream Source!"
date:   2023-01-26 19:38:44 +0530
categories: Akka Stream
---
Akka Streams is a powerful tool for building concurrent and distributed stream processing applications on the JVM. It provides a set of abstractions and components for working with streaming data, including Sources, Flows, and Sinks.

A Source is a component that generates a stream of data. It can be thought of as the "producer" in a stream processing pipeline. A Source can be created using various methods, such as `Source.single`, `Source.repeat`, `Source.fromIterator`, `Source.fromFuture`, etc.

```scala
// Create a Source that emits the integers 1 to 10
val intSource: Source[Int, NotUsed] = Source(1 to 10)

// Create a Source that emits a single string "Hello"
val singleSource: Source[String, NotUsed] = Source.single("Hello")

// Create a Source that emits the string "Hello" repeatedly
val repeatSource: Source[String, NotUsed] = Source.repeat("Hello")

// Create a Source from an Iterator
val iteratorSource: Source[Int, NotUsed] = Source.fromIterator(() => Iterator.from(1))

// Create a Source from a Future
val futureSource: Source[String, NotUsed] = Source.fromFuture(Future("Hello"))

```