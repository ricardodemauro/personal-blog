+++
date = 2023-04-28T23:20:01Z
description = ""
draft = true
slug = "c-records-types"
title = "C# Records Types"

+++


## Introduction

C# 9.0 introduces record types. You use the record keyword to define a reference type that provides built-in functionality for encapsulating data.

### What are records?

A record type in C# 9 is a lightweight, immutable data type (or a lightweight class) that has read-only properties only. Because a record type is immutable, it is thread-safe and cannot mutate or change after it has been created.

### Mutable property

A record type can have mutable properties if you use init-only setters.

### Init-only setters

You can initialize a record type only inside a constructor.

### How to use it

You can create record types with immutable properties by using positional parameters or standard property syntax.

### Conclusion

Record types are distinct from classes in that record types use value-based equality. Two variables of a record type are equal if the record type definitions are identical, and if for every field, the values in both records are equal.

I hope this helps! Let me know if you have any other questions.

