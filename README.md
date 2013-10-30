akka3d
======

A DDD, CQRS and event sourcing framework built on top of Akka

Why the name?
-------------

Because 3d is short for Domain Driven Design (DDD).

What is it?
-----------

It's an actor based framework for building DDD, CQRS and event sourcing applications. It's built on top of Akka and makes use of the simply awesome [akka-persistence](http://doc.akka.io/docs/akka/snapshot/scala/persistence.html) extension (and many other Akka goodies). The design is influenced by a vision to have a 1:1 relationship between an Aggregate Root (AR) and an actor. And, importantly, it promotes a simplified concurrency model whereby only one actor per aggregate root instance can exist at a time. 

Initially, akka3d is more a collection of ideas and best practices for combining the actor model with DDD, CQRS and event sourcing. It does kind of represent a framework, but it's possibly a bit generous to call it one in its current form.

This project aims to provide a viable alternative to traditional database backed CRUD solutions. No longer must we fight against business logic saturated services, anaemic domain models and bloated ORMs.

akka3d is the materialisation and extension of some ideas I first wrote about [here](http://www.dreweaster.com/blog/2013/10/27/Akka-DDD-CQRS-Event-Sourcing-And-Me/).

What is it not?
---------------

Close to being ready to use in anything other than prototyping contexts :-) And it only has single node support for now which could be quite limiting.

akka3d does not currently have a Java implementation - it is planned!

A short example
---------------

This is just a very basic example of akka3d in use:
```scala
  val domainModel = DomainModel("my-domain") {
    Customer
  }

  val readModel = domainModel.subscribe(Customer, CustomerReadModel.props)

  val customerId = UUID.randomUUID
  val customer = domainModel.aggregateRootOf(Customer, customerId)

  customer ! CreateCustomer("Andrew", 33)
  customer ! ChangeCustomerName("Andy")
  customer ! ChangeCustomerAge(34)

  (readModel ? GetCustomer(customerId)).map {
    case Some(customer: CustomerDTO) => println(customer)
  }
```
TODO
----

* Add Distributed PubSub style to event publishing - topic per aggregate root type. Read models subscribe to "feed" for particular aggregate root type. Subscribe via DomainModel.
* Support for become/unbecome without breaking snapshoting
* Aggregate root timeouts (clear cache) - ReceiveTimeout
* Snapshot scheduling
* DI integration (subcut?)
* Avoid missing published events in read models?
* Add Akka cluster support for fully distributed aggregates - might take a while :-) But this could be of limited use without it
* Support for services (what are these exactly?). How do aggregates access them. Subcut/DI? Services as actors?
* Support with process managers (not sure what I really mean by this yet!). Actor that receives events and sends commands (and is persistent)...
* Scheduling support - linked with process managers, e.g. timing out a ticket reservation. Must support durable scheduling - i.e. scheduled jobs must survive restarts/crashes
* Support for FSMs
* What the hell to do about errors, e.g. trying to send a Create to an existing entity. Error stream?! This is a wider CQRS topic
* Java API
* Play! integration
* Spray integration
* Build on TravisCI and deploy artifact to some maven repository
* Create Typesafe Activator template that uses akka3d
