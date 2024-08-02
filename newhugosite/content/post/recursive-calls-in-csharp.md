+++
categories = ["C#", "DotNet", "Recursive-Call", "Recursive-Function"]
date = 2024-07-19T19:18:44Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1604971666408-9dcd56ece0bf?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDN8fE1pcnJvcnxlbnwwfHx8fDE3MjAyMDkxMzJ8MA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "recursive-calls-in-csharp"
tags = ["C#", "DotNet", "Recursive-Call", "Recursive-Function"]
title = "Recursive Calls in C#: A Deep Dive"

+++


Recursion functions are like a magical mirror that reflects upon itself. It's a concept where a function calls itself, creating a mesmerizing loop of self-referential elegance.

Let's break it down how recursive functions works using **Factorial Calculation** as sample.

## What is a Factorial?
Before we dive into the code, let's unravel the mystery of factorials. 
A factorial of a non-negative integer N, denoted as N!, is the product of all positive integers from 1 to N. 

For instance:

5! = 5 × 4 × 3 × 2 × 1 = 120
3! = 3 × 2 × 1 = 6

## Recursive Factorial Function
Let's create a function called **Factorial** that takes an integer x as input and returns its factorial.

```csharp
static int Factorial(int x) 
{ 
  // Base case: 0! = 1 
  if (x == 0) return 1; 
  
  // Recursive case: N! = N * (N-1)! 
  else return x * Factorial(x - 1); 
}

int facResult = Factorial(4);
Console.WriteLine($"4 Factorial is {facResult}");
```

When we call Factorial(4), it unfolds like this.

* Factorial(4) = 4 × Factorial(3)
* Next: Factorial(3) = 3 × Factorial(2)

Continuing down the rabbit hole:

* Factorial(2) = 2 × Factorial(1)
* Factorial(1) = 1 × Factorial(0)

Finally, Factorial(0) is the base case, which gracefully returns 1. The recursive function unwinds, multiplying the results back up the chain.

The termination condition `if (x == 0)` ensures that the recursion stops, preventing an infinite loop.

### Looking at a different angle

```csharp
Call Factorial(x = 4) (which returns 4 * 3)
  x doesn't == 0
  return 4 * Factorial(3)  
  
    Call Factorial(x = 3) (which returns 3 * 2)
      x doesn't == 0
      return 3 * Factorial(2)  

         Calls Factorial(x = 2) (which returns 2 * 1)
           x doesn't == 0
           return 2 * Factorial(1)  

              Calls Factorial(x = 1) (which returns 1 * 1)
                x doesn't == 0
                return 1 * Factorial(0)

                   Calls Factorial(x = 0) (which returns 1)
                     x == 0 so return 1
```

Or... starting from the bottom of the picture.

![recursive-call](https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/recursive-call.png)

Now you've glimpsed the enchantment of recursive calls in C#.

## CLR (Common Language Runtime) and the Call Stack

When you invoke a recursive function, the Common Language Runtime (CLR) allocates memory on the call stack for each function call.

As the recursion progresses, the stack grows, creating a stack frame for each invocation.

When the base case is reached (in our example, when x == 0), the stack starts unwinding. Each stack frame is deallocated, and the results are propagated back up.

Be cautious! Too much recursion can lead to stack overflow errors. The CLR has a finite stack size, and excessive recursion can exhaust it. 

So, while recursion is elegant, use it judiciously.

Happy coding! 🚀

## References
[Stack Overflow: Factorial Calculations using Recursive Method Call](https://stackoverflow.com/questions/27307229/factorial-calculations-using-recursive-method-call)



