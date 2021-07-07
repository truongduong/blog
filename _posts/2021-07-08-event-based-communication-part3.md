---
title: Event-based Communication | Part 3 - MasTransit Event Bus
layout: post
date: '2021-07-08'
categories:
- Microservices
---

This is the last post in series Event-based communication using an event bus. in this post we will looking at MassTransit


MassTransit is a free, open-source distributed application framework for .NET. MassTransit makes it easy to create applications and services that leverage message-based, loosely-coupled asynchronous communication for higher availability, reliability, and scalability.


[https://masstransit-project.com/](http://)


Similar to NServiceBus, MassTransit implements the [*Publish–Subscribe Pattern*](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)


For both frameworks, multicast is supported, lets see how to use MassTransit to implement an event bus that can be used to support event-base communication in microservices

RabbitMQ transport is built-in supported so in this spost we will use it as a broker

Basically, we need to define events that are shared among microservices, events will be used by Publisher/Subsriber for publishing, subscribing and handling events

The same UC with [*the first post*](//blog/event-based-communication-part1) when Catalog API will publish event ProductPriceChanged and the Basket API will subscribe and handle it


<img src="{{ '/blog/assets/masstransit_00.png'}}" />


#### **Events**


Define events in shared building blocks. In multi-repo approach building blocks or shared code can be built as nugget packages

<img src="{{ '/blog/assets/masstransit_01.png'}}" />


#### **Install MassTransit nugget packages in services**

For .Net Core application, using RabbitMQ transport, install below package, refer to MassTransit [*Configuration* ](http://masstransit-project.com/usage/configuration.html#asp-net-core) for more options

MassTransit

MassTransit.RabbitMQ

MassTransit.AspNetCore


#### **Subscriber service (Basket)**

Implement consumer classes for subscribing and consuming events, inherit and implement IConsumer<Event>. The framework will handle subscribing, interacting with broker, creating proper exchange, queue and corresponding bindings
	
	
<img src="{{ '/blog/assets/masstransit_01.png'}}" />

	
(MassTransit uses the term Consumer instead of Hander i.e. someeventconsumer)
	
	
Register MassTransit in service container
	
<img src="{{ '/blog/assets/masstransit_04.png'}}" />
	

	
RabbitMQ broker host address config
	
<img src="{{ '/blog/assets/masstransit_4.1.png'}}" />


#### **Publisher service (Catalog)**


Register services in Startup.cs

<img src="{{ '/blog/assets/masstransit_05.png'}}" />

	
Simple message publishing

<img src="{{ '/blog/assets/masstransit_06.png'}}" />
	

Check RabbitMQ admin site to see how exchange and queue are created and bind for subscriber service

<img src="{{ '/blog/assets/masstransit_08.png'}}" />

	
Queue binding

<img src="{{ '/blog/assets/masstransit_09.png'}}" />

	
Now that we have setup basic publisher, broker and subscriber, we will publish an event to see how it is consumed in subscriber service
	
	
Publish an event from Catalog service using Postman

<img src="{{ '/blog/assets/masstransit_10.png'}}" />
	

Event is consumed in Basket service

<img src="{{ '/blog/assets/masstransit_11.png'}}" />
	
	
**Conslusion**
	
MassTransit is free software/open-source .NET-based Enterprise Service Bus software while NServiceBus required a commercial licence. MassTransit supports almost features to build message-based application, It can be an anternative to NServiceBus
