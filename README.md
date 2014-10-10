Reactive Streams: AMQP
====

Experimental implementation of [Reactive Streams](http://www.reactive-streams.org) for AMQP based on [RabbitMQ](https://www.rabbitmq.com/) library.

Example
----
```Scala
// streaming invoices to Accounting Department
val factory = new ConnectionFactory()
val connection = AmqpConnection(factory)
val publisher = connection.consume(queue = "invoices")
val subscriber = connection.publish(exchange = "accounting_department",
  routingKey = "invoices")

publisher.subscribe(new Subscriber[Delivery] {
  override def onError(t: Throwable) = subscriber.onError(t)
  override def onSubscribe(s: Subscription) = subscriber.onSubscribe(s)
  override def onComplete() = subscriber.onComplete()
  override def onNext(t: Delivery) = subscriber.onNext(t.message)
})
```

Caveats
----
 * `QueueSubscription` will cancel underyling consumer when demand goes to zero. This may cause queue with `autoDelete` flag to be removed by the broker. Solution to this problem is not ready yet.
