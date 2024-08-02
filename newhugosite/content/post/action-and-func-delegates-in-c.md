+++
date = 2023-04-15T20:50:51Z
description = ""
draft = true
slug = "action-and-func-delegates-in-c"
title = "Action and Func delegates in C#"

+++


In this article, we will explore two types of predefined delegates in C# `System.Action` and `System.Func`.

Delegates are one of the most important features of the C# language. We typically use them as a type-safe way to represent a method, which can be passed as an argument to another method.

## The Basics of Delegates

Before diving into Actions and Funcs let's discuss what Delegates are.

Delegates are a typed way to represent a method that can be passed as an argument to another method. We can use them as first-class objects, which means they can be stored in variables, passed as arguments to other methods, and returned as values from methods.

We see most of the use cases for delegates as event handling.

Check out this example of how a _Delegate_ works:

* One button is pressed in the GUI
* An Event is raised
* A method (the event handlers) is executed

## Using the Action Delegate

The `System.Action` delegate is a predefined delegate type in C# that represents a method that has a **void** return type and takes zero or more parameters.

We should use Actions in situations that perform computation without a return value.

C# has a few predefined Action delegates:

public delegate void Action(); //👇 The function arguments are defined using Generics, avoiding boxing unboxing public delegate void Action<in T>(T obj); public delegate void Action<in T1, in T2>(T1 arg1, T2 arg2); public delegate void Action<in T1, in T2, in T3>(T1 arg1, T2 arg2, T3 arg3);

It continues overloading until 16 arguments.

Check out the syntax for using Action delegates:

Action helloWorldDelegate = () => Console.WriteLine("Hello, World!"); helloWorldDelegate(); // Output: "Hello, World!" //👇 First and second arguments are integers Action<int, int> WriteSumOfDelegate = (operator1, operator2) => { Console.WriteLine("Sum of {0} and {1} is {3}", operator1, operator2, operator1 + operator2); }; WriteSumOfDelegate(10, 20); //Output: "Sum of 10 and 20 is 30"

## Actions with Non-Void Return: Here is the Func Delegate

The `System.Func` delegate is another predefined delegate type in C# that represents a method that has a return type and takes zero or more parameters.

We should use Funcs in situations that perform computation and return a value.

C# have some predefined Func<T> delegates:

public delegate TResult Func<out TResult>(); public delegate TResult Func<in T, out TResult>(T arg); public delegate TResult Func<in T1, in T2, out TResult>(T1 arg1, T2 arg2);

It also continues overloading until 16 arguments.

Next is the syntax for using a Func delegate:

Func<int, int> DoubleItDelegate = (operator1) => { return operator1 * 2; }; var result = DoubleItDelegate(2); //returns 4 Console.WriteLine("Func Double result {0}", result); //⌨ Output: "Func Double result 4"

In this example, we have defined a `Func` delegate that takes a `int` parameter and returns a `int` value. Then assign a lambda expression to the delegate, which **doubles** its input.

Finally, we call the delegate with an argument of 2, which returns the value 4.

## Conclusion

In conclusion, we can see how powerful delegates are and allow us to treat methods as first-class objects.

The `Action` and `Func` delegates are two predefined types that are commonly used for event handling, asynchronous programming, and other tasks that require passing a method as an argument to another method.

## Sources

[https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

[https://learn.microsoft.com/en-us/dotnet/api/system.func-2?view=net-8.0](https://learn.microsoft.com/en-us/dotnet/api/system.func-2?view=net-8.0)

[https://learn.microsoft.com/en-us/dotnet/api/system.action-1?view=net-8.0](https://learn.microsoft.com/en-us/dotnet/api/system.action-1?view=net-8.0)

