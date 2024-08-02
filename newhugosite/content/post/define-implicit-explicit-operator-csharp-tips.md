+++
categories = ["C#"]
date = 2022-11-10T18:48:49Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1509869175650-a1d97972541a?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDExfHxlcXVhbHMlMjBtYXRofGVufDB8fHx8MTY2ODEwNDcwNw&ixlib=rb-4.0.3&q=80&w=2000"
slug = "define-implicit-explicit-operator-csharp-tips"
tags = ["C#"]
title = "Define Implicit and Explicit Operator - C# Tips"

+++


C# allows us to define Implicit and Explicit operators. Unlike casting _I**mplicit**_ **__** and **_Explicit_** operators defines how C# should behave when encountering an equals sign.

**_Implicit_** operator execution can be invoked when assigning a variable or calling a method.

To use **_Explicit_** operator we should do the same as casting an object. It's similar to a **_cast_** an object.

```csharp
public record class Email(string Value)
{
    //define implicit operator
    public static implicit operator string(Email value) 
      => value.Value;
    
    //define implicit operator
    public static implicit operator byte[](Email value) 
      => Encoding.UTF8.GetBytes(value.Value);

    //define explict operator
    public static explicit operator Email(string value) 
      => new Email(value);
}
```

To define a implicit/explicit we need to make use of "operator", "implicit" or "explicit" keywords.

The following example demonstrates the use of both operators.

```csharp
Email email = new("some@email.com");

//use implicit operator. This is not a cast
string stringEmail = email;

Email secondEmail = (Email)stringEmail;

//output rmauro@rmauro.dev
System.Console.WriteLine(stringEmail); 
System.Console.WriteLine(secondEmail.Value.ToString());
```

The explicit conversion is similar to a cast operation. We make visible the type to which we will convert the object. The implicit operator is less visible and difficult to understand if you don’t know that it exists.

## ❤️ Enjoy this article?

**Forward to a friend** and let them know.

