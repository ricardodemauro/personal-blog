+++
date = 2017-01-12T02:51:04Z
description = ""
draft = true
slug = "mongodb-for-net-developers-1"
title = "MongoDB for .Net Developers #1"

+++


Hi guys,

In this post I'll talk a little about MongoDB by the eyes of .Net Developer.

###Before you start
If you don't have already installed the MongoDB system use this [article](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/). 

>Just in case you don't know nothing about NoSQL them read some basics [here](https://en.wikipedia.org/wiki/NoSQL).

MongoDB it's a NoSQL system classified as a **Document-oriented database** (in a another article I'll talk about the concepts of NoSQL).

###Getting started

First create a Console Application to begin our lab.

![Empty Console Application](__GHOST_URL__/content/images/2017/01/emptyconsoleapplication.png)

Import using nuget the **MongoDB C# Driver**

Now let's create a POCO class named **ComicCon** with the following code.

    public class ComicCon
    {
        public int Edition { get; set; }
        public DateTime Date { get; set; }
        public string Location { get; set; }
        public int Attendance { get; set; }
        public string[] Guests { get; set; }
    }

Open the file "program.cs" and insert the code below in the using section.

    using MongoDB.Bson;
    using MongoDB.Driver;

All you need to know about MongoDB you can find in the official documentation [MongoDB](https://docs.mongodb.com/manual/).

