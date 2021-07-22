---
title: CSharp Delegates
layout: post
date: '2021-07-15'
categories: Programming Languages
---


## C# Delegate Types

A delegate is a type that represents references to methods with a particular parameter list and return type. When you instantiate a delegate, you can associate its instance with any method with a compatible signature and return type. You can invoke (or call) the method through the delegate instance.

### Func<> Delegate
```csharp
Func<int, int, string> sum;
```

It has **zero or more input parameters and one out parameter**. The last parameter(string) is considered as an out parameter, we can assign method, anonymous or lambda expression to a Func

```csharp
//Method
static int Sum(int x, int y)
{
    return x + y;
}
Func<int,int, int> add = Sum;

//Anonymous
Func<int, int, int> add = delegate (int x, int y) { return x + y; };

//Lambda expression
Func<int, int, int> add = (x, y) => x + y;

var result = add(1, 2);
```

### Action<> Delegate
The same with Func except that **it does not return a value**, the action delegate can be used/assigned with void() function

//Method
```csharp
static void Print(int i)
{
    Console.WriteLine(i);
}
Action<int> printAction = Print;
//or
Action<int> printAction = new Action<int>(Print);

//Anonymous
Action<int> printAction = delegate(int i) { Console.WriteLine(i); };

//Lambda
Action<int> printAction = i => Console.WriteLine(i);

printAction(10);
```
### Predicate<> Delegate
Predicate is the delegate like Func and Action delegates.. A predicate delegate method must **take one input parameter and return a boolean** - true or false

Three usages with method, anonymous and lambda

```csharp
public delegate bool Predicate<in T>(T obj);

public void IsEqual(string something) { return something.equal("something"); }

Predecate<string> isEqual = IsEqual;

Predecate<string> isEqual = delegate(string s) { return s.equal("something"); }

Predecate<string> isEqual = s => s.equal("something");

bool result = isEqual("somthing");
```
### Anonymous Method
```csharp

public delegate void Print(int value);

Print prt = delegate(string val) { Console.WriteLine("Anonymous method: {0}", val); };

public void PrintWithInputParam(Print print, string somethingToPrint)
{
			somethingtoprint += " somethingelse";
			print(somethingToPrint);
}

PrintWithInputParam(prt, "something to print");

//Anonymous is used as event handler
var consumer = new EventingBasicConsumer(channel);
consumer.Received **+= delegate(model, ea)**
{
		var body = ea.Body.ToArray();
		var message = Encoding.UTF8.GetString(body);
		Console.WriteLine(" [x] Received {0}", message);
};

//Lambda expression is used as event handler
var consumer = new EventingBasicConsumer(channel);
consumer.Received **+= (model, ea) =>**
{
		var body = ea.Body.ToArray();
		var message = Encoding.UTF8.GetString(body);
		Console.WriteLine(" [x] Received {0}", message);
};

```
