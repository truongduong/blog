---
title: Event-based Communication | Part 1 - Simple Event Bus Implementation
layout: post
date: '2021-07-03'
categories:
- Microservices
- Architecture
---

In microservices application, one of communication style among services is pub/sub via an event-bus

<img src="{{ '/blog/assets/rabbitmq-implementation.png'}}" />


*RabbitMQ implementation of an event bus*

[https://docs.microsoft.com/en-us/dotnet/architecture/microservices/multi-container-microservice-net-applications/rabbitmq-event-bus-development-test-environment
](http://)

This is the first of three posts series, we will impplement a simple event bus to leverage pub/sub communication among microservices

[[Part 2] Event-based Communication - NServicebus Event Bus](/blog/event-based-communication-part2)

[[Part 3] Event-based Communication - MasTransit Event Bus](/blog/event-based-communication-part3)


The simple use case is when the Catalog API publishes the event **ProductPriceChanged** and other Service APIs will subscribe and proceed this event i.e. Basket API

<img src="{{ '/blog/assets/2021-07-04_00h35_06.png'}}" />


#### **Generic Event bus implementation**

Event bus interfaces with two methods Publish() and Subscribe()

<img src="{{ '/blog/assets/2.png'}}" />


Handler interface with Handle() method which will be overridden by specific handler in subscriber service i.e. Bastket API

<img src="{{ '/blog/assets/3.png'}}" />


#### **In memory subscription manager**

Subscriber services could subscribe to multiple events and for each event there can be one or more handlers, so we need to manage all handlers for events

<img src="{{ '/blog/assets/4.png'}}" />


#### **A concrete event bus implementation using message broker RabbitMQ**

<img src="{{ '/blog/assets/5.png'}}" />


Implement IEventBus interfaces 

<img src="{{ '/blog/assets/6.png'}}" />


Implement the RabbitMQ peristent connection

<img src="{{ '/blog/assets/7.png'}}" />


Until now, we have the event bus implemented, to see how it works we will define events, handlers, publishing and subcribing in our microservices

Because we use RabbitMQ as message broker so we need to install it

Run the command below to start a RabbitMQ container listening on the default port of 5672. Admin site is on port 15672

{% highlight docker %}
docker run -d —-hostname my-rabbit —-name some-rabbit 
-p 15672:15672 -p 5672:5672 rabbitmq:3-management
 {% endhighlight %}
 
<img src="{{ '/blog/assets/14.png'}}" />


#### **Subscribe to ProductPriceChanged event in Basket API**

Event and its handlers

{% highlight csharp%}
public class ProductPriceChangedIntegrationEvent : IntegrationEvent
{
	public int ProductId { get; private set; }
	public decimal NewPrice { get; private set; }
	public decimal OldPrice { get; private set; }
	public ProductPriceChangedIntegrationEvent(int productId, decimal newPrice, decimal oldPrice)
	{
		ProductId = productId;
		NewPrice = newPrice;
		OldPrice = oldPrice;
	}
}

public class ProductPriceChangedIntegrationEventHandler 
        : IIntegrationEventHandler<ProductPriceChangedIntegrationEvent>
{
	private readonly ILogger<ProductPriceChangedIntegrationEventHandler> _logger;
	public ProductPriceChangedIntegrationEventHandler(ILogger<ProductPriceChangedIntegrationEventHandler> logger)
	{
		_logger = logger;
	}
	public async Task Handle(ProductPriceChangedIntegrationEvent @event)
	{
		_logger.LogInformation("Basket service - event handling: {0}", 
																	JsonConvert.SerializeObject(@event));
		await Task.Delay(1);
	}
}
{%endhighlight%}

Register Event bus services: IRabbitMQPersistentConnection,  IEventBus, IEventbusSubscriptionManager, ProductPriceChangedIntegrationEventHandler and finally subscript to this event

<img src="{{ '/blog/assets/10.png'}}" />

	
	
#### **Subscribe to ProductPriceChanged event in another API**

	
We do the same steps for another API, this is to see an event can be subscribed and handled simultaneously.

<img src="{{ '/blog/assets/11.png'}}" />
	

**After starting two APIs, we have an Exchange, two queues created and these are binding together with the same routing key which is the event name**

<img src="{{ '/blog/assets/12.png'}}" />

	
<img src="{{ '/blog/assets/13.png'}}" />
	

	
#### **Publish ProductPriceChanged event in Catalog API**

To see how the event is handled in subscribers, we will publish the event.

Create a simple integration service for publishing event

{% highlight csharp%}
public interface ICatalogIntegrationEventService
{
	Task PublishThroughEventBusAsync(IntegrationEvent evt);
}
	
public class CatalogIntegrationEventService : ICatalogIntegrationEventService, IDisposable
{
	private readonly IEventBus _eventBus;
	private readonly ILogger<CatalogIntegrationEventService> _logger;
	private volatile bool disposedValue;
	public CatalogIntegrationEventService(IEventBus eventBus, ILogger<CatalogIntegrationEventService> logger)
	{
		_eventBus = eventBus ?? throw new ArgumentNullException(nameof(eventBus));
		_logger = logger ?? throw new ArgumentNullException(nameof(logger));
	}
	public async Task PublishThroughEventBusAsync(IntegrationEvent evt)
	{
		//to be added event log table for tracking failure/transaction - sending event and update price to database
		try {
					_logger.LogInformation("----- Publishing integration event: {IntegrationEventId_published} from {AppName} -         ({@IntegrationEvent})", evt.Id, Program.AppName, evt);
				_eventBus.Publish(evt);
				await Task.Delay(1);
		} catch (Exception ex)
		{ 
			_logger.LogError(ex, "ERROR Publishing integration event: {IntegrationEventId} from {AppName} - ({@IntegrationEvent})", evt.Id, Program.AppName, evt);
			//mark event failed
			await Task.Delay(1);
		}
	}
}
{% endhighlight%}

Register EventBus and RabbitMQPersistentConnection and publish the event

<img src="{{ '/blog/assets/16.png'}}" />
	

Lets run the Catalog API to publish event ProductPriceChanged to queues

<img src="{{ '/blog/assets/18.png'}}" />
	

	
**Note that, I have stopped two subscriber APIs to see messages are delivered to subscriber queues. when I re-start these services we can see the event is consumed as below**

<img src="{{ '/blog/assets/19.png'}}" />
	
<img src="{{ '/blog/assets/20.png'}}" />


[[Part 2] Event-based Communication  - NServicebus Event Bus](/blog/event-based-communication-part2)

[[Part 3] Event-based Communication  - MasTransit Event Bus](/blog/event-based-communication-part3)
