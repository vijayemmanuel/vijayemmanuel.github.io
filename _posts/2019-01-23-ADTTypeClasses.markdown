---
layout: post
title: Functional Constructs with ADTs, TypeClasses
date: 2019-01-23 19:20:23 +0900
category: scala
---

Traditionally in Object Oriented Programming (OOP) language we think in terms of objects. Object definition are something which we can relate to some real world entity.. This makes our software design closer to the real world. Objects have data (member variables) and its characteristics (methods). Often it happens that the methods we define for the object may have the business logic relating to the object. 

Lets take an a simple example. First we look into how we design an expression evaluator. In OOP approach I would have the following code snippet to define an abstract Expression class with an eval method. Addtionally I would define a Number class and Sum class extended from Expression class.

```scala
abstract class Expr {
def eval: Int
 }
 class Number(n: Int) extends Expr {
 def eval: Int = n
 }
 class Sum(e1: Expr, e2: Expr) extends Expr {
 def eval: Int = e1.eval + e2.eval
 }
 ```

 Evaluation of the expression looks like 
 ```scala
 Sum (Number(1), Number(2)).eval       //> res0: Int = 3
```

 Interospection with the above design:
 1. If I want to add a Product class, it would mean adding a new class with its eval logic. There is no impact of modifying the existing class. This is something like data extension.  
 2. There could also be extensions defined with regard to operations. Here let us say we need to add a expression formatter (pretty print). Coming back to the code, this would mean adding changing the definitions of existing classes for adding new method.

If I want to seperate out the data structure from the above code, Functional Programming (FP) recommendes that I seperate out the data part from the above code and represent it as a **Algebraic Data Type (ADT)**. This is how it would look like:

# Algebraic Data Type 

```scala
trait Expr // or abstract class  
case class Number(n: Int) extends Expr 
case class Sum(e1: Expr, e2: Expr) extends Expr
```
>Traits are akin to Java interfaces, but are allowed to have method implementations.
>Traits does not have constructors and thus cannot “stand by itelf” They can be used like mixin class composition.
>Case classes come with an implicit constructor as its own name. Thus we do not have to add new keyword when creating its objects. It also comes with an equals, hashCode and toString operator.

As you see here now we have completely seperated out the representation of data based on the domain definitions coming from the problem domain without mixing much of the business logic.


Now to in order to add the busiess rules which operate on the data (ADTs) we define it in a companion object. With Scala pattern matching we are able to add a new method over the trait Expr. With different match cases, we can check the type of the pattern and call its appropriate evaluation.

```scala
object Expr {
def eval(e: Expr): Int = e match {
 case Number(n) => n
 case Sum(l, r) => eval(l) + eval(r)
 }
}
```

 Interospection with the above design:
 1. As we discussed before, when new operation needs to be added we do have to modify the existing code-base. Adding an expression formatter method would require us to add new def.

Evaluation of the Sum of two numbers would still remain the same
```scala
 Sum (Number(1), Number(2)).eval       //> res0: Int = 3
```

Scala pattern matching is such a powerful concept that we could really ease our way of building programs over data. 
Lets take another example of JSON serialiser

```scala
// Define a very simple JSON ADT
sealed trait Json
final case class JsObject(get: Map[String, Json]) extends Json 
final case class JsString(get: String) extends Json
final case class JsNumber(get: Double) extends Json
case object JsNull extends Json

// The "serialize to JSON" behaviour is encoded in this trait
trait JsonWriter[A] {
  def write(value: A): Json
}
```

# Type class

JsonWriter is our **Type class** in this example, with Json and its subtypes providing supporting data. It is "type" class beacuse  the business logic is abstrated over any type 'A'.

# Type class instances

Type classes are usually supported by **type class instances**. The instances of a type class provide implementations for the types we care about, including types from the Scala standard library and types from our domain model.
In Scala we define instances by crea􏰁ng concrete implementatons of the type class and tagging them with the implicit keyword:

```scala
final case class Person(name: String, email: String)

object JsonWriterInstances {
  implicit val stringWriter: JsonWriter[String] =
    new JsonWriter[String] {
      def write(value: String): Json =
        JsString(value)
    }
  implicit val personWriter: JsonWriter[Person] =
    new JsonWriter[Person] {
      def write(value: Person): Json =
        JsObject(Map(
          "name" -> JsString(value.name),
          "email" -> JsString(value.email)
        ))
    }      
    // etc...
}
```

# Type class interface

A **type class interface** is any functionality we expose to users. Interfaces are generic methods that accept instances of the type class as implicit parameters.
There are two common ways of specifying an interface: **Interface Objects** and **Interface Syntax**

### Interface Objects

The simplest way of creating an interface is to place methods in a singleton object:

```scala
object Json {
def toJson[A](value: A)(implicit w: JsonWriter[A]): Json =
    w.write(value)
}
```

To use this object, we import any type class instances we care about and call the relevant method:

```scala
import JsonWriterInstances._
Json.toJson(Person("Dave", "dave@example.com"))
// res4: Json = JsObject(Map(name -> JsString(Dave), email -> JsString(dave@example.com)))
```
The compiler spots that we’ve called the toJson method without providing the implicit parameters. It tries to fix this by searching for type class instances of the relevant types and inser􏰁ng them at the call site:

```scala
Json.toJson(Person("Dave", "dave@example.com"))(personWriter)
```

### Interface Syntax

We can alternatively use extension methods to extend existing types with interface methods.

```scala
object JsonSyntax {
  implicit class JsonWriterOps[A](value: A) {
    def toJson(implicit w: JsonWriter[A]): Json =
      w.write(value)
    } 
}
```

We use interface syntax by importing it alongside the instances for the types we need:

```scala
import JsonWriterInstances._
import JsonSyntax._
Person("Dave", "dave@example.com").toJson
// res6: Json = JsObject(Map(name -> JsString(Dave), email -> JsString(dave@example.com)))
```

Again, the compiler searches for candidates for the implicit parameters and fills them in for us:

```scala
Person("Dave", "dave@example.com").toJson(personWriter)
```
