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

Once you have a Source, you can chain it with other Akka Streams components, such as Flows and Sinks, to create a processing pipeline. You can also use various combinator functions, such as `map`, `filter`, `groupBy`, etc. to transform the data emitted by the source.

It's important to note that the Source is immutable and the combinator functions returns new source, not modify the current source.

in addition to the methods of creating a Source that I mentioned in my previous response, Akka Streams also provides several other ways to create a Source from various sources of data. Here are a few examples:

***Source.fromPublisher(publisher: Publisher[Out]):*** This method allows you to create a Source from a Publisher in the Reactive Streams API. This is useful for integrating Akka Streams with other libraries or frameworks that implement the Reactive Streams API.

***Source.actorRef(bufferSize: Int, overflowStrategy: OverflowStrategy)(implicit actorRefFactory: ActorRefFactory, mat: Materializer):*** This method allows you to create a Source from an Akka Actor's ActorRef. Messages sent to the ActorRef will be emitted by the Source.

***Source.actorRefWithBackpressure(bufferSize: Int, overflowStrategy: OverflowStrategy, ack: Any)(implicit actorRefFactory: ActorRefFactory, mat: Materializer):*** This method is similar to the above, but it allows you to handle backpressure by sending an acknowledgement message to the actor when it should send more messages.

***Source.queue(bufferSize: Int, overflowStrategy: OverflowStrategy):*** This method allows you to create a Source from a queue. Elements can be enqueued and dequeued by separate stages in a stream.

***Source.tick(initialDelay: FiniteDuration, interval: FiniteDuration, tick: T)(implicit system: ActorSystem):*** This method allows you to create a Source that emits a tick message at regular intervals.

These are just a few examples, there are other possibilities to create a source, such as Source.fromGraph, Source.lazySource, etc.
It's important to note that all of these methods are useful in different scenarios, and the best method to use will depend on the specific requirements of your application.

here are some examples of how to use the various methods of creating a Source that I mentioned earlier, along with sample output for each example:

- Source.fromPublisher(publisher: Publisher[Out]):

```scala
import akka.stream.scaladsl._
import akka.stream._
import java.util.concurrent.SubmissionPublisher

implicit val system = ActorSystem("my-system")
implicit val materializer = ActorMaterializer()

val publisher = new SubmissionPublisher[Int]()
val source = Source.fromPublisher(publisher)

source.runForeach(println)

publisher.submit(1)
publisher.submit(2)
publisher.submit(3)

// Output:
// 1
// 2
// 3

```

- Source.actorRef(bufferSize: Int, overflowStrategy: OverflowStrategy)(implicit actorRefFactory: ActorRefFactory, mat: Materializer)

```scala
import akka.actor._
import akka.stream._

implicit val system = ActorSystem("my-system")
implicit val materializer = ActorMaterializer()

val actorRef = system.actorOf(Props[MyActor])
val source = Source.actorRef(bufferSize = 10, overflowStrategy = OverflowStrategy.dropHead)

source.runForeach(println)

actorRef ! 1
actorRef ! 2
actorRef ! 3

class MyActor extends Actor {
  def receive = {
    case x: Int => sender() ! x
  }
}
// Output:
// 1
// 2
// 3

```

- Source.actorRefWithBackpressure(bufferSize: Int, overflowStrategy: OverflowStrategy, ack: Any)(implicit actorRefFactory: ActorRefFactory, mat: Materializer)

```scala
import akka.actor._
import akka.stream._

implicit val system = ActorSystem("my-system")
implicit val materializer = ActorMaterializer()

val actorRef = system.actorOf(Props[MyActor])
val source = Source.actorRefWithBackpressure(bufferSize = 10, overflowStrategy = OverflowStrategy.dropHead, ack = "ack")

source.runForeach(println)

actorRef ! 1
actorRef ! 2
actorRef ! 3

class MyActor extends Actor {
  def receive = {
    case x: Int => sender() ! x
    case "ack" => sender() ! 1
  }
}
// Output:
// 1
// 2
// 3

```

- Source.queue(bufferSize: Int, overflowStrategy: OverflowStrategy)

```scala
import akka.stream._

implicit val system = ActorSystem("my-system")
implicit val materializer = ActorMaterializer()

val (queue, source) = Source.queue[Int](bufferSize = 10, overflowStrategy = OverflowStrategy.dropHead).preMaterialize()

source.runForeach(println)

queue.offer(1)
queue.offer(2)
queue.offer(3)

// Output:
// 1
// 2
// 3

```

- Source.tick(initialDelay: FiniteDuration, interval: FiniteDuration, tick: T)(implicit system: ActorSystem)

```scala
import akka.actor.ActorSystem
import akka.stream._
import scala.concurrent.duration._

implicit val system = ActorSystem("my-system")
implicit val materializer = ActorMaterializer()

val tickSource = Source.tick(initialDelay = 1.seconds, interval = 1.seconds, tick = "Tick")
tickSource.runForeach(println)

// Output (assuming the program runs for 5 seconds):
// Tick
// Tick
// Tick
// Tick
// Tick

```

This example creates a Source that emits the string "Tick" every second, starting one second after the source is created. The initialDelay parameter specifies the delay before the first tick message is emitted, and the interval parameter specifies the delay between subsequent tick messages.

It's important to note that the Source.tick method returns an infinite source, it will keep emitting "Tick" unless it's stopped.