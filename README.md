Architecture

What does a truly flexible architecture look like?  Why is scaling or fault tolerance so difficult for established apps to introduce?  Language changes?

Design vs. Architecture vs. Infrastructure
-
From: https://en.wikipedia.org/wiki/Software_design

"Software design usually involves problem solving and planning a software solution. This includes both low-level component and algorithm design and high-level, architecture design."

"Obviously physical infrastructure and architecture are not the same. Yet an architecture both provides and, in a sense, is a kind of conceptual infrastructure - a meta-structure in which ideas of structure (and views of that structure, and so on) can be arranged in consistent, meaningful, useful ways." Tom Graves from http://www.ebizq.net/blogs/ebizq_forum/2011/03/how-is-architecture-different-from-infrastructure.php

Architecture is a simplified abstract representation of the infrastructure that will be deployed.  The infrastructure is the physical implementation of the architecture.  But it is not a 1-1 mapping: if we setup load balancers, there is no abstract representation of that in the software.

design: the component or algorithm
architecture: how the components are organized to accomplish specific use-cases.
infrastructure: the hardware components that make up an architecture.  The infrastructure of servers and associated networks support the architecture.

Screaming architecture: the directory structure should tell you the concerns of the the underlying code, and be free of assumptions of delivery mechanism.
-
From: http://blog.8thlight.com/uncle-bob/2011/09/30/Screaming-Architecture.html

"...software architectures are structures that support the use cases of the system."

"Architectures are not (or should not) be about frameworks. Architectures should not be supplied by frameworks."

"The reason that good architectures are centered around use-cases is so that architects can safely describe the structures that support those use-cases without committing to frameworks, tools, and environment."

"The Web is a delivery mechanism, and your application architecture should treat it as such. The fact that your application is delivered over the web is a detail and should not dominate your system structure."

I assume persistence is as well?  The reason apps can't scale or become fault tolerant easily is because the architecture is coupled to a delivery mechanism.

"If you are building a health-care system, then when new programmers look at the source repository, their first impression should be: “Oh, this is a heath-care system”. Those new programmers should be able to learn all the use cases of the system, and still not know how the system is delivered."

^ Good architectures clearly communicate what the software's business requirements are.


Clean Architecture: The Dependency Rule -- details need clear, inverted boundaries.
-
From: http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html

The point of all of these architectures is to change quickly based on business requirements.

"Independent" is the beginning of every point except easily testable.

"The overriding rule that makes this architecture work is The Dependency Rule. This rule says that source code dependencies can only point inwards. Nothing in an inner circle can know anything at all about something in an outer circle."

The layers:

entities -> use cases -> interface adapters -> frameworks and drivers

Entities: Objects that hold business logic and make no assumptions about how or where they will be consumed.  They must be generic enough that any consumer can use them to accomplish some goal.

Use Cases: Organizes the logic of the entities to accomplish an application-specific goal.

Interface Adapters: Converts data to and from the internal form used by use cases and entities to the data structures required by the presentation or persistence components.

Frameworks and Drivers: Databases, web frameworks, worker queues -- all of the infrastructral details separate from the business logic.

Always keep to "The Dependency Rule":

"As you move inwards the level of abstraction increases. The outermost circle is low level concrete detail. As you move inwards the software grows more abstract, and encapsulates higher level policies. The inner most circle is the most general."

Never let any detail of one layer leak into another layer.

Hexagonal Architecture (a.k.a Ports and Adapters)
-
From: http://alistair.cockburn.us/Hexagonal+architecture

"Allow an application to equally be driven by users, programs, automated test or batch scripts, and to be developed and tested in isolation from its eventual run-time devices and databases."

The application should be ignorant of how it receives information, and how it emits information.  Instead, adapters are used to convert the data from one format to another, depending on receiver.

This solution is mainly motivated by logic leaking into the presentation layer.  But the same problem exists when tied to the database layer.

"Imagine now that 'every' piece of functionality the application offers were available through an API (application programmed interface) or function call."

This provides the flexibility for any part of the application to be run via scripts for testing or background processing in a variety of formats, simplifies the architecture, and allows more specific, disciplined roll-out of features, like deploying in "headless mode" for API only interaction.

"Both the user-side and the server-side problems actually are caused by the same error in design and programming — the entanglement between the business logic and the interaction with external entities. The asymmetry to exploit is not that between ‘’left’’ and ‘’right’’ sides of the application but between ‘’inside’’ and ‘’outside’’ of the application. The rule to obey is that code pertaining to the ‘’inside’’ part should not leak into the ‘’outside’’ part."

"The word “port” is supposed to evoke thoughts of ‘’ports’’ in an operating system, where any device that adheres to the protocols of a port can be plugged into it"

A "port" is an abstraction that understands a specific protocol.  A protocol will also have "adapters" programmed to it.  For example, a GUI may have an API adapter that sends the data structure to the API port, and then will route the port data structure to the consumer's adapter, making the data structure suitable for the internal business logic to consume.  The persistence layer is just as important:

"The protocol is typically a database protocol. From the application’s perspective, if the database is moved from a SQL database to a flat file or any other kind of database, the conversation across the API should not change. Additional adapters for the same port thus include an SQL adapter, a flat file adapter, and most importantly, an adapter to a “mock” database, one that sits in memory and doesn’t depend on the presence of the real database at all."

"The hexagonal, or ports and adapters, architecture solves these problems by noting the symmetry in the situation: there is an application on the inside communicating over some number of ports with things on the outside. The items outside the application can be dealt with symmetrically."

Language choice
-
I can't really do a supervision tree easily in Ruby without extra infrastructure to support parallel execution and supervision.

