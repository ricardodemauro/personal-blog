+++
categories = ["C#", "en"]
date = 2019-09-29T20:35:05Z
description = ""
draft = false
slug = "csharp-implicit-conversion-cast-explicit-conversion"
tags = ["C#", "en"]
title = "C# Conversions - Casting, implicit and explicit conversion"

+++


Fist the data types available.

    int            whole numbers
    long           whole numbers (bigger range)
    float          floating-point numbers
    double         double precision
    decimal        monetary values
    char           single character
    bool           boolean
    DateTime       moments in time	
    string         sequence of characters


Table of implicit conversion made by C#.


    From           To 
    sbyte          short int long float double decimal 
    byte           short ushort int uint long ulong float double decimal 
    short          int long float double decimal 
    ushort         int uint long ulong float double decimal 
    int            long float double decimal 
    uint           long ulong float double decimal 
    long ulong     float double decimal 
    float          double 
    char           ushort int uint long ulong float double decimal


By the way, you cannot implicitly convert a **long** value to an **int**, because this conversion risks losing information (the long value might be outside the range supported by the int type). 

###Explicit conversions

In Visual C#, you can use a cast operator to perform explicit conversions. A cast specifies the type to convert to, in round brackets before the variable name

    int a; 
    long b = 5; 
    a = (int) b;    
    // Explicit conversion of long to int. 

##Using the System.Convert Class
The **System.Convert** class provides methods that can convert a base data type to another base data type. These methods have names such as **ToDouble**, **ToInt32**, **ToString**, and so on.

    string possibleInt = "1234"; 
    int count = Convert.ToInt32(possibleInt);

###TryParse conversion

    int number = 0; 
    string numberString = "1234"; 
    if (int.TryParse(numberString, out number)) 
    {     
        // Conversion succeeded, number now equals 1234. 
    } 
    else 
    {    
        // Conversion failed, number now equals 0. 
    }

