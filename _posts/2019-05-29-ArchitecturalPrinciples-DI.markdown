---
layout: post
title: Architectural Principles - Dependency Inversion
date: 2019-05-29 19:20:23 +0900
category: architecture
---

Architecture is important while desiging buildings, so it is in software. If we do not built a good design, then we might not be able to scale and maintain applications well when requirements of software keeps on changing. 
This is a series of posts I am writing to investigate the common design patterns followed in Object oriented architecture (OOP). Whereever possible, I also compare on how these architectural principle is followed in Functional programming (FP). 

Here are a few common architectural pattern followed in mainstream languages. 

- Dependency Inversion
- Single Responsibility
- Encapsulation
- Don't repeat yourself

### Dependency Inversion (DI) : The C# way

**The direction of dependency within the application should be in the direction of abstraction, not implementation details**. Most applications are written such that compile-time dependency flows in the direction of runtime execution, producing a direct dependency graph. Let take a look on the following piece of code

``` csharp
public class ClassA
{
    public void DoWork() 
    {
        var b = new ClassB();
        b.DoStuff();
    }
}
public class ClassB
{
    public void DoStuff()
    {
        // Imagine implementation
    }
}
```

ClassA depends directly on ClassB and within it’s DoWork method, it creates a new instance before calling its DoStuff method. We can apply a couple of common principles to improve this code.

``` csharp
public class ClassA
{
    private readonly ClassB _dependency;
    public ClassA(ClassB classB) => _dependency = classB;
    public void DoWork() => _dependency.DoStuff();
}
 
public class ClassB : IThing
{
    public void DoStuff()
    {
        // Imagine implementation
    }
}
```
Firstly, we no longer use the new keyword to instantiate an instance of ClassB. Instead, we have specified the type it depends on within the constructor. An instance of ClassB must be provided when that constructor is called. This applies a form of Inversion of Control (IoC) since ClassA is no longer in control of creating an instance of ClassB.
We can apply a further principle to this code.

``` csharp
public interface IThing
{
    public void DoStuff();
}
public class ClassA
{
    private readonly IThing _dependency;
    public ClassA(IThing thing) => _dependency = thing;
    public void DoWork() => _dependency.DoStuff();
}
public class ClassB : IThing
{
    public void DoStuff()
    {
        // Imagine implementation
    }
}
```
We have now applied the **dependency inversion** principle. We no longer depend on a concrete implementation. Instead, we depend upon the IThing abstraction. We allow the caller of this constructor to pass in any valid implementation of this interface.

In application code, we can satisfy this manually with the following code.

``` csharp
class Program
{
    static void Main(string[] args)
    {
        IThing thing = new ClassB();
        ClassA classA = new ClassA(thing);
        classA.DoWork();
    }
}
```
We must first create an instance of ClassB, which now implements the IThing interface. We can create an instance of ClassA, passing in ClassB as the first and only argument. For these two classes, it’s pretty straightforward to handle this dependency injection manually. As applications grow, we are likely to have tens, even hundreds or thousands of types, many of which may have deeper dependency graphs.


### Dependency Inversion (DI) : The Scala way

#### Alternative 1: Self Type annotations

Now let us see how the same dependency inversion is followed in Scala, a part functional programming language (OOP-FP??)

Scala provides native language constructs to do dependency inversion also commonlu known as cake pattern

``` scala
class ClassA(dependency : Class B) {
    def DoWork() = dependency.DoStuff();
}
class ClassB {
    def DoStuff() = {
        // Imagine implementation
    }
}
```
Here you can see that we are referencing an instance of the ClassB. This is the dependency that we would like to have injected for us. Let's first wrap the ClassB in an enclosing trait and instantiate the user repository there.

``` scala
trait ClassBComponent {
  val classB = new ClassB
  class ClassB {
        def DoStuff() = {
            // Imagine implementation
        }
    }
}
```

This simply creates a component namespace for our ClassB. We can make use of this namespace later. Now let's revisit at ClassA, the user of the ClassB. We would like to have ClassB instance injected in ClassA. We will first do what we did with the repository above; wrap the it in an enclosing (namespace) trait and use a so-called **self-type annotation** to declare our need for the ClassA. 

``` scala
// using self-type annotation declaring the dependencies this
// component requires, in our case the ClassBComponent
trait ClassAComponent { this: ClassBComponent =>
  val classA = new ClassA
  class ClassA {
   def DoWork() = classB.DoStuff();
  }
}
```
The self-type annotation we are talking about is this code snippet:
``` scala
this: ClassBComponent =>
```
If you need to declare more than one dependency then you can compose the annotations like this:
``` scala
this: Foo with Bar with Baz =>
```

Now we have declared the ClassBComponent dependency. What is left is the actual wiring. In order to do that the only thing we need to do is to merge/join the different namespaces into one single application (or module) namespace. This is done by creating a "module" object composed of all our components. When we do that all wiring is happening automatically.

``` scala
object ComponentRegistry extends
  ClassBComponent with
  ClassAComponent
```
All this wiring is statically typed and defined as immutable. However this approach can still be made better by declaring all the namespace declaration as a abstract member field

``` scala
trait ClassBComponent {
  val classB : ClassB
  class ClassB {
        def DoStuff() = {
            // Imagine implementation
        }
    }
}
trait ClassAComponent { this: ClassBComponent =>
  val classA : ClassA
  class ClassA {
   def DoWork() = classB.DoStuff();
  }
}

object ComponentRegistry extends
  ClassBComponent with
  ClassAComponent {
  val classB = new ClassBComponent
  val classA = new ClassAComponent
  }

```

#### Alternative 2: Implicits

Scala also offers implicits feature where compiler can magically can bring in type instances if it available in scope. 
Though this feature might look magical, if is sometimes difficult to understand by a new programmer, unless the Development environment/tool supports it well. 

Here is an example implemented through implicits

``` scala
// =======================
// service interfaces
trait OnOffDevice {
  def on: Unit
  def off: Unit
}
trait SensorDevice {
  def isCoffeePresent: Boolean
}
// =======================
// service implementations
class Heater extends OnOffDevice {
  def on = println("heater.on")
  def off = println("heater.off")
}
class PotSensor extends SensorDevice {
  def isCoffeePresent = true
}

// =======================
// service declaring two dependencies that it wants injected
class Warmer(
  implicit val sensor: SensorDevice,
  implicit val onOff: OnOffDevice) {

  def trigger = {
    if (sensor.isCoffeePresent) onOff.on
    else onOff.off
  }
}

// =======================
// instantiate the services in a module

object Services {
  implicit val potSensor = new PotSensor
  implicit val heater = new Heater
}

// =======================
// import the services into the current scope and the wiring
// is done automatically using the implicits

import Services._

val warmer = new Warmer  // the magical confusing part
warmer.trigger
```

That sums up what I had planned to go through in this article. I hope that you have gained some insight in how one can do DI in C# and Scala, using language abstractions. What works best for you is up to your use-case, requirements and taste. 


