---
title: Event-based Communication | Part 2 - NServicebus Event Bus
layout: post
date: '2021-07-05'
categories:
- Microservices
- Architecture
---

In this second post, we will be looking at how to use NServicebus to implement an event bus that supports pub/sub communication


**NServiceBus** [https://docs.particular.net/nservicebus/](http://)


**NServiceBus Transports** [https://docs.particular.net/transports/](http://)


It is so simple to implement an event bus using NServiceBus, lets see how


#### **Subscriber**

Install nugget package

<img src="{{ '/blog/assets/nservicebus_01.png'}}" />


Define events, these will be used in both subscriber and publisher, so we share it

<img src="{{ '/blog/assets/nservicebus_02.png'}}" />


Implement event handler

<img src="{{ '/blog/assets/nservicebus_03.png'}}" />


Register and start the endpoint (subscriber)

<img src="{{ '/blog/assets/nservicebus_04.png'}}" />


#### **Publisher**

To be able to publish event, we also need event, create endpoint


Add referrence to shared events i.e. OrderReceived and ProductPriceChanged


Register and start the endpoint (publisher)

<img src="{{ '/blog/assets/nservicebus_06.png'}}" />


#### **Subscription management in various transports**


**SQL Server Transport**


There is a table SubscriptionRouting to store queue table addresses and endpoints as well

<img src="{{ '/blog/assets/nservicebus_07.png'}}" />



**RabbitMQ  Transport**


Queue is created per endpoint

<img src="{{ '/blog/assets/nservicebus_08.png'}}" />


Exchange is created by event and bind to queues with routing key

<img src="{{ '/blog/assets/nservicebus_09.png'}}" />


Exchange and queue are bind for push event to subscriber/endpoint

<img src="{{ '/blog/assets/nservicebus_10.png'}}" />



**Azure Service Bus  Transport**


Queue is created per endpoint(subscriber/publisher)

<img src="{{ '/blog/assets/nservicebus_11.png'}}" />


Subscriptions are under topic and contain filters to event types that allow routing to queue

<img src="{{ '/blog/assets/nservicebus_12.png'}}" />


<img src="{{ '/blog/assets/nservicebus_13.png'}}" />




[[Part 1] Event-based Communication - NServicebus Event Bus](/blog/event-based-communication-part1)

[[Part 3] Event-based Communication - MasTransit Event Bus](/blog/event-based-communication-part3)
