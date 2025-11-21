[Back](./README.md)

# Preface

I am still learning and gathering experience with DDD. Most of what I learned is from the original Domain-Driven Design book by Eric Evans (a.k.a. "the blue book"), and from real life experience. This document represents my current state of knowledge, which could be wrong. However, I am trying to capture the essence rather than every detail, so hopefully that will keep the mistakes to a minimum.

As such, the knowledge on this page is extremely condensed, it is not meant to replace the blue book. It is for organising our thoughts after we've already read the book.

I believe that DDD applies more to medium to large size projects in a team, and less to small size hobby projects that we do on our own, because of how it heavily emphasize communication. Having said that, successful hobby projects can attract people and grow into bigger projects, so the philosophy here will still be useful.

# Domain Driven Design

**Domain Driven Design** (DDD) is a framework of mind for creating software that stays as close to what it is meant to do as possible. In the corporate world, this means staying close to the business requirements. In the startup world or small-scale hobby projects, this means sticking to the vision that you have for it.

## What is a Domain

This "_what it is meant to do_", "_business requirements_", "_vision_", or as known in more general terms as the **problem space**, is called the **Domain** in DDD. It encapsulates the essence of the problem that we try to solve with the software.

There's a hierarchical structure to this. We have top-level domains, from which you can extract smaller subdomains. 

From my experience in the corporate world, the top-level domains are the top-level business areas surrounding the software. As a purely theoretical example, if we are making a shopping app, then the Domains might be Customer Management, Inventory, Payments. Within Customer Management, we might have Customer Data, Privacy, Customer Support as subdomains, and so on.

```mermaid
kanban
  [Customer Management]
    [Customer Data]
    [Privacy]
    [Customer Support]
  [Inventory]
    [Inventory & Supplies]
    [Pricing]
    [Sales]
  [Payments]
    [Payment Gateways]
    [Orders]
    [Warranty & Disputes]
```

How these Domains and Subdomains are defined will be based on our understanding of how the business functions, or ought to function. So the task of defining the Domains mostly should fall onto very senior roles in the business, such as a director, at least from my experience.

From the technical standpoint, we only need to know that Domains and Subdomains represent the problems of the business. Understanding what counts as a Domain and a Subdomain is not very important right now, as that will only come as part of working for the same business for some time. However, the framework of mind that helps understanding the Domain is a transferable skill, and worth investigating.

Since Subdomains are just smaller Domains within bigger Domains, I will refer to all of them as Domains from now for simplicity.

## Understanding the Domain

In order to create software that serves a Domain, we first need to understand what it is. Actually, DDD is mostly about achieving a deep understanding of Domains, so if we can do this, we are 80% of the way there.

### Ubiquitous Language

**Communication is key**. This involves ALL forms of communications, for example:
- Speech such as ad-hoc conversations and meetings.
- Texts such as messages and emails.
- Documents such as source code, wiki pages and tutorials, design documents and test reports.

It is basically everything. If there is an idea to convey between human beings, it falls under DDD's watch (if it is related to the work, that is). DDD rightly points out that the understanding of a Domain relies heavily on how well ideas are communicated.

How do we ensure that communication is done well? According to DDD, the same as with any other areas of life, by getting everyone to use a **common, consistent, ubiquitous language**.

In life, it is difficult for 2 people to communicate well if they speak different languages, or sometimes even different dialects of the same language. Although that's not the same as the **Ubiquitous Language** that DDD speaks of, the idea is the same. I have seen inconsistent use of language within the same software project way too often. The same concept is called many different terms. 

```mermaid
flowchart TD
  Y("You")
  P1("Package 1")
  P2("Package 2")
  P3("Package 3")
  A{"Adapter"}
  P1 --> User
  P2 --> Customer
  P3 --> Client

  Y --> P1
  Y --> P2
  Y --> P3
  Y --> A
  
  A --> User
  A --> Customer
  A --> Client
```

It may seem harmless at first, but consider this scenario where you need to read source code to understand what it is trying to achieve. In one package, you see the User class, so you look for its use in the rest of the code, but you couldn't find much. Turns out, it is called Customer in another package, and Client in another. You discovered this only after finding the adapters that convert objects of these types. 

Not only this is a lot of wasteful object creations, it creates huge frictions when the code is used as a communication tool. It is a lot more efficient if the same concept is represented by a single class instead of 3 different classes.

```mermaid
flowchart TD
  Y("You")
  U("User")
  P1("Package 1")
  P2("Package 2")
  P3("Package 3")

  P1 --> U
  P2 --> U
  P3 --> U

  Y --> P1
  Y --> P2
  Y --> P3
```

The way I've dealt with this is to create a **Glossary document** for the Domain, point all related design documents to it, and ask everyone to use it consistently.

| Term | Definition |
|---|---|
| App | The software that our team ABC is building for the purpose of XYZ |
| User | The end user, customer, client of the App |

_So create and enforce a Ubiquitous Language, get everyone to use it in the context of the Domain._

### Domain Experts

Alongside the Ubiquitous language, we need to identify the **Domain Experts**. These are people who serve as the sources of truth for everything related to the Domain, because they truly know it best.

To identify them, look for where the feature requests of our software came from. This might be the Product Owners if we have them in the team, or just a developer from another team who's making requests to our team. It's anyone who owns the idea, the concept of what they want our team to build.

Once we've identified them, we should talk to them as much as we can, because they are the key to understanding the Domain. Talk to them when they make a request, when we've made some progress, when we have a demo, when there are some hiccups, or when we simply want to deepen our understanding.

The blue book really emphasizes on the importance of this. How breakthroughs in understanding of the Domain through communication with the Domain Experts and through deep thinking, lead to fundamental and positive changes in the software.

### It is an iterative process

What we understand about the Domain in the beginning will never be perfect. Not only that, but the Domain itself evolves, it changes and moves forward. Or at least the Domain Experts' knowledge of it.

```mermaid
flowchart LR
  S1("Review awkward areas in the model")
  S2("Discover inconsistencies in the model")
  S3("Knowledge crunch with Domain Experts")
  S4("Achieve a breakthrough & improve the model")

  S1 --> S2 --> S3 --> S4 --> S1
```

Like agile development, we start with something rough, then iteratively refine our understanding, sometimes achieving breakthroughs. This process gradually moves the software towards better serving the Domain.

### Again, communication is key

Perhaps you have heard about techniques or ceremonies to extract Domain knowledge, such as Event Storming. 

I would like to stress again, that communication is key. Techniques such as Event Storming may work for some, but not others, so do not put too much weight on them and be flexible. 

As long as we gain knowledge, it does not matter much which technique we use.

## The DDD architecture

With all that prep talk out of the way, how do we distill our Domain understanding into a design? Ultimately, we need to understand the concepts, and the business rules governing those concepts.

To do that, I believe that there are 3 main components in a DDD architecture.

- The Domain Model
- The Domain Service
- The Anti-corruption Layer (Optional)

### The Domain Model

The concepts and business rules of the Domain is distilled into a **Domain Model**. It is almost similar to a relational database schema, but way more abstract, with business logic inside them. Imagine full-fledged abstractions of real life Domain objects with their behaviour (a.k.a. business logic) encoded within.

DDD asks that we must always ensure consistency and protect the integrity of the Domain Model. That means ensuring that all the business logic are properly encoded.

For example, from speaking to the Domain Experts, we learned several rules the User Management context:

- Each User should be unique and identifiable. There can't be duplicate Users.
- Each User should have at least 1 or more Addresses assigned.
- Each User can have 0 or more Orders assigned.
- Addresses and Orders are for display purposes only, they don't have to be unique and identifiable.

Note that these rules apply only to the User Management context. They will be different in another context, such as the Order Shipment context.

So in our Domain Mode for the User Management system, the User, Address and Order classes should have logic to satisfy all of the above rules.

In DDD, there are several tools to help us with building this. They consist of **Entities**, **Value Objects** and **Aggregates**.

#### Entities, Value Objects and Aggregates

**Entities are mutable objects with an identity**. Entities are mutable except for their ID. An example is User, where each instance of it corresponds to a real User of the App. In real life, each person can be identified by their social security number, and each User instance can be identified by, for instance, its UUID.

```mermaid
classDiagram
  class User {
    Id : UUID
    Name : Name
    Addresses : List~Address~
    Orders : List~Order~
    static CreateUser(Name, Address) User // generates new Id automatically
    AddAddress(Address)
    AddOrder(Order)
    Equals(User) bool // Compares Ids
  }
```

Entities need to have their integrity ensured at all times, a bit like an ACID database. Operations on Entities should be transactional, atomic with logic to ensure that no inconsistencies can rise. Because of this, Entities are inherently complex and not everything should be entities.

What determines that an Entity is consistent? The Domain's **business rules**.

**Value Objects are immutable objects without an identity**. An example is User's Name. There is nothing special about a Name, it is only so that the software knows what to put in the User's invoices. In fact, 2 Users can even share the same Name instance. Changing the Name of one User will not affect the other, due to Name being immutable and we just replace the whole instance. Value Objects are inherently much simpler than Entities. 

```mermaid
classDiagram
  class Name {
    Value : string
    static CreateName(string) Name
    Equals(Name) bool // Compares Values
  }
```

Another way to think of Entities vs Value Objects is to imagine implementing an equal comparison. For Entities, we compare their IDs, whereas for Value Objects, we compare the attributes.

Value Objects also may contain business rules, but they are immutable and vastly simpler.

Something can be a Value Object within a context, but an Entity in another context. It depends on whether or not the identity of that object is important for that context. This is called a _Bounded Context_, which I will describe later.

<table>
<tr>
<th> User Management System </th>
<th> Order Management System </th>
</tr>
<tr>
<td>

```mermaid
flowchart LR
  U("User<br/>[E]")
  A1("Address<br/>[VO]")
  U --> A1
```

</td>
<td>

```mermaid
flowchart LR
  A2("Address<br/>[E]")
```

</td>
</tr>
</table>

For example, the User's Address is a Value Object in the context of the User Management system. However, for an Order Shipment system, the Address might be an Entity, since it is important to the shipment of the order.

**Aggregates are groups of related Entities and Value Objects**. Since these objects are related to each other, they naturally falls in a hierarchy, and the top should always be an Entity.

An Aggregate can be thought of as a big & complex Entity. There's the usual suspects of being an Entity, any operation done to it must ensure its internal consistency, a.k.a. the business rules. This is why an aggregate should only be accessed through the top level Entity. Bypassing this would potentially bypass important internal logic.

Also, an Aggregate can be contained inside another Aggregate.

```mermaid
---
  config:
    class:
      hideEmptyMembersBox: true
---
classDiagram
  namespace UserAggregate {
    class User["User<br/>[E]"]
    class Name["Name<br/>[VO]"]
    class Address["Address<br/>[VO]"]
    class Order["Order<br/>[E]"]
  }
  User o-- Name
  User o-- "1..*" Address
  User o-- "0..*" Order
  Order o-- Address

  class External["External system"]
  direction LR
  External --> User: Query orders
```

### The Domain Service



## Model Integrity

### Bounded Context

As the name suggest, a Bounded Context is an abstract boundary which contains a bunch of Domains related to each other. 
