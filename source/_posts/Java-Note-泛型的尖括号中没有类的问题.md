---
title: Java-Note-泛型的尖括号中没有类的问题
date: 2018-04-20 19:43:04
tags:
category: Java
---
- 这其实是一个语法糖, 可以简化代码
- 详见:
```

MORE LIKE THIS
6-7-8 hopscotch
Java 101: The essential Java language features tour, Part 5
A stack of coins
Java 9's other new enhancements, Part 2: Milling Project Coin
Detecting Class Innards in Groovy
RELATED ARTICLES
cloud ladder climb sky
AWS Lambda tutorial: Get started with serverless computing
NASA Apollo 8 - December 24, 1968 - Earthrise
Cosmos DB review: Database for a small planet
open source license primary.jpg
GitHub tutorial: Get started with GitHub
See all Insider
Project Coin provides numerous "small language enhancements" as a subset of the new JDK 7 features. I recently blogged on Project Coin's switching on Strings and in this post I write about the new Diamond Operator (<>).

The Diamond Operator reduces some of Java's verbosity surrounding generics by having the compiler infer parameter types for constructors of generic classes. The original proposal for adding the Diamond Operator to the Java language was made in February 2009 and includes this simple example:

For example, consider the following assignment statement:

Map<String, List<String>> anagrams = new HashMap<String, List<String>>();

This is rather lengthy, so it can be replaced with this:

Map<String, List<String>> anagrams = new HashMap<>();

The above example provided in Jeremy Manson's proposal (which was one of the first in response to a call for Project Coin ideas) is simple, but adequately demonstrates how the Diamond Operator is applied in JDK 7. Manson's proposal also provides significant into why this addition was desirable:

The requirement that type parameters be duplicated unnecessarily like

this encourages an unfortunate

overabundance of static factory methods, simply because type inference

works on method invocations.

In other words, the JDK 7 Project Coin addition of a Diamond Operator brings type inference to constructors that has been available with methods. With methods type inference is implicitly done when one leaves off the explicit parameter type specification. With instantiation, on the other hand, the diamond operator must be specified explicitly to "tell" the compiler to infer the type.
```
- 参考文档: https://www.javaworld.com/article/2074080/core-java/jdk-7--the-diamond-operator.html
