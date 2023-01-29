---
layout: posts
title:  "Scala Implicit Cheatsheet"
date:   2023-01-27 10:38:44 +0530
categories: Scala Implicit Type-Classes CheatSheets
---
In Scala, "implicit" refers to a mechanism for automatically passing unnamed arguments to a function or method. The significance of this feature is that it allows for more concise and readable code, as well as the ability to create type classes and other advanced abstractions.

One of the challenges of using implicit parameters is that they can make code more difficult to understand, especially for developers who are not familiar with the feature. Additionally, the use of implicit parameters can lead to "implicit ambiguity" errors when the compiler is unable to determine which implicit parameter to use. Careful design and documentation can help mitigate these challenges.

An implicit cheat sheet can be helpful for developers working with the "implicit" feature in the Scala programming language. It can provide a quick reference for the syntax and usage of implicit parameters.

Here, I've attempted to keep the use of the `Implicit` topic brief and to the point so that it can be quickly and easily remembered.
# Scala 2
## 1. Implicit Conversion

An implicit conversion function is a function with a single parameter that is declared with the implicit keyword. As the name suggests, such a function is automatically applied to convert values from one type to another.

```scala
implicit def int2Fraction(n: Int) = Fraction(n, 1) 
implicit val str2Person(str: String)= Person(str) 
```
- Now, if you have an implicit conversion String ===> Person in scope, you can use a string wherever a Person is needed, and it will be automatically converted by the implicit conversion.
- Additionally, the string can use all methods defined in the Person class, since it will be converted to a Person object.

## 2. Implicit class for Enriching Existing Classes (Extension)
**Use case:** The java.io.File class does not have a read method. If we want to add a read method to the File class, we can use implicit conversions.

**Step 1:** Define an enriched class that provides the desired read method:

```scala
class RichFile(val from: File) { 
    def read = Source.fromFile(from.getPath).mkString
}
```
**Step 2:** There are two options for providing an implicit conversion:

**Option 1:** Define a type conversion function that converts a File object into a RichFile object implicitly, so that the read method will be available in the File class:

```scala
implicit def file2RichFile(from: File) = new RichFile(from)

```
**Option 2:** Declare RichFile as an implicit class:

```scala
implicit class RichFile(val from: File) { ... }

```
- An implicit class must have a primary constructor with exactly one argument, and the argument type should be the class in which enrichment is needed. The constructor becomes the implicit conversion function. It is a good idea to declare the enriched class as a value class:

```scala
implicit class RichFile(val from: File) extends AnyVal { ... } 

```
- Note that an implicit class cannot be a top-level class. It must be placed inside the class that uses the type conversion, or inside another object or class that you import.

## 3. Implicit Parameters

A function or method can have a parameter list that is marked implicit. In that case, the compiler will look for default values to supply with the function call. Here is a simple example:

```scala
case class Delimiters(left: String, right: String)
def quote(what: String)(implicit delims: Delimiters) = delims.left + what + delims.right
```
- There can only be one implicit value for a given data type. Thus, it is not a good idea to use implicit parameters of common types. For example,

```scala
def quote(what: String)(implicit left: String, right: String) // No! would not work—one could not supply two different strings.
```
- If an implicit parameter is a single-argument function (higher order function) instead of value, then it is also used as an implicit conversion.

## 4. Implicit Scope

The places where the compiler searches for candidate instances is known as the implicit scope. The implicit scope applies at the call site; that is the point where we call a method with an implicit parameter.

The implicit scope roughly consists of:

- val/var
- Object
- Accessor methods (def with no parenthesis) - Note that if parenthesis is used it will not be treated as an implicit parameter
- Local block
- Current class
- Inherited definitions (extends classes, traits)
- Imported definitions
- Implicit functions or implicit class definitions in the companion object of the source or target type class
- Definitions in the companion object of the parameter type

