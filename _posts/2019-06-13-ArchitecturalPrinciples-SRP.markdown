---
layout: post
title: Architectural Principles - Single Responsibility
date: 2019-06-13 19:20:23 +0900
category: architecture
---

Architecture is important while desiging buildings, so it is in software. If we do not built a good design, then we might not be able to scale and maintain applications well when requirements of software keeps on changing. 
This is a series of posts I am writing to investigate the common design patterns followed in Object oriented architecture (OOP). Whereever possible, I also compare on how these architectural principle is followed in Functional programming (FP). 

Here are a few common architectural pattern followed in mainstream languages. 

- Dependency Inversion
- Single Responsibility
- Encapsulation
- Don't repeat yourself

### Single Responsibility 

Single Responsibility principle (SRP) states that objects should have only one responsibility and that they should have only one reason to change. Specifically, the only situation in which the object should change is if the manner in which it performs its one responsibility must be updated. 
The idea of having this principle helps to produce more loosely coupled and modular systems, since many kinds of new behavior can be implemented as new classes, rather than by adding additional responsibility to existing classes. Adding new classes is always safer than changing existing classes, since no code yet depends on the new classes.


In case of smaller implementations, applying single responsibility is matter of knowing the right level of seperation we need for class definitions. 

``` csharp
public class Service {

    public void insertStudent(Student student){
        // insert student
    }

    public void updateStudent(Student student){
        // update student
    }

    public void deleteStudent(Student student){
        // delete student
    }

    public void insertCourse(Course course){
        // insert course
    }

    public void updateCourse(Course course){
        // update course
    }

    public void deleteCourse(Course course){
        // delete course
    }
}
```
In code above shows violation of SRP because Service handle the business logic for Student and Course class. In the future Service can become bloaters or even become our ‘nightmare’ because we put everything in Service.
The solution is we need to split Service into specific class to handle business logic for Student (StudentService) and Course

``` csharp
public class CourseService {

    public void insert(Course course){
        // insert course
    }
    public void update(Course course){
        // update course
    }
    public void delete(Course course){
        // delete course
    }
}
... 
public class StudentService {

    public void insert(Student student){
        // insert student
    }
    public void update(Student student){
        // update student
    }
    public void delete(Student student){
        // delete student
    }
}
```

### Seperation of Concerns 
In a large monolithic application, we can apply the single responsibility principle at a high level to the layers in the application. Presentation responsibility should remain in the UI project, while data access responsibility should be kept within an infrastructure project. Business logic should be kept in the application core project, where it can be easily tested and can evolve independently from other responsibilities.

Here is an example from MVC pattern

![MVC Pattern](/assets/architecture/model-view-controller-03a.png){:class="img-responsive"}

Here, you will see that the GUI is handled in the Presentation layer, business rules are handled in the Business layer, and database access is handled in the Data Access layer. 
But this might not seem close to the definition of SRP, but we have achieved a similar concept "Seperation of Concerns" (SoC)

SoC's scope is at a higher framework/application level. Like...separation of concerns between areas of the framework and application to split them up in logical parts, so they are more modular, reusable and easier to change.
The goals of both concepts are the same, however, you can't always say you have successfully complied with SoC and it directly means you have properly done SRP too.


### Microservice
When this principle is applied to application architecture and taken to its logical endpoint, you get microservices. A given microservice should have a single responsibility. If you need to extend the behavior of a system, it's usually better to do it by adding additional microservices, rather than by adding responsibility to an existing one.

In Scala, one of the best microservice pattern implementation can be seen in Akka toolkit. 

Some of the common patterns followed here are Domain Driven Design (DDD)/ Hexagonal Architecture.

