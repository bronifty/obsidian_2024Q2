### cohesion, coupling
#### cohesion
- definition: to what extent parts of a module should be contained together (how related the parts are in a module); attempting to divide a cohesive module would result in increased coupling

##### range of cohesion:
	1 functional: module contains everything and nothing but what it needs to function
	2 sequential: modules interact where one outputs data that becomes input to another
	3 communication: modules participate in a comms chain where to contribute to some output (eg add db record and send email update about it)
	4 procedural: modules execute in a particular order
	5 temporal: modules are related based on timing dependencies (eg docker compose web app depends on db)
	6 logical: modules data are related logically but not functionally (eg util package for operating on a data type; they all logically work on the same thing, but are otherwise unrelated)
	7 coincidental: parts in module only relate in that they exist in the same file

there are four measurements of architecture: 
1 LCOM 
- LCOM measures the extent to which methods of a class share instance variables
2 abstractness
- abstractness is a ratio of abstract methods to the total number of abstractions + implementations 
3 instability
-  instability is a measure of outward coupling 
4 distance from the main sequence 
- distance from the main sequence is an X Y axis measuring abstraction and coupling with abstraction on the Y axis and coupling on the X axis; in the top right hand corner, you have pure abstraction plus coupling in the bottom left-hand corner you have zero abstraction zero coupling


> Cyclomatic Complexity 
> - cycle refers to graph of control flow in a program
	- more paths through the code base means harder to understand
	- essential v accidental
	- governance and fitness functions (to test the above)

### Static Coupling
- range of coupling (from best to worst coupling)
- name
	- update all variables/methods with the same name - easy to change
- type
	- update all types of methods/variables
- meaning
	- hardcoded numbers rather than constants (eg true is 1 and false is 0)
- position
	- function arg position
- algorithm
	- security hash that must run on both server and client to produce identical results to authenticate the caller

Dynamic Coupling
- execution
	- order of execution (eg code that sends an email needs to have subject line set first)
- timing
	- timing of execution (eg race condition between 2 competing threads)
- values
	- several values relate and must change together (eg distributed systems transactions; multiple databases must be updated together or not at all)
- identity
	- a distributed queue maintains the identity between writer and reader (reader is programmed to read from a specific named service with a specific protocol/message format)
----

<details>
  <summary>ChatGPT Prompt: a queue being written to and read from distributed services has a tight coupling compared to a messaging service facilitating publishing and subscriptions to a topic</summary>
  

Answer: Yes, you're correct. A queue being directly written to and read from by distributed services often results in tighter coupling compared to using a messaging service that facilitates publishing and subscriptions to a topic. Here's why:

### Direct Queue Interaction

- **Tight Coupling:** When services directly interact with a queue, they often have explicit knowledge of the queue's existence, its name, and possibly its schema. This setup means that producers and consumers are closely tied to the specifics of the queue, leading to tight coupling. For instance, if a service posts messages to a queue, any consuming service must know the exact queue to listen to and understand the format of the messages it receives.
- **Dependency on Queue Mechanics:** Both the producing and consuming services must manage their connection and interaction with the queue, including error handling, message serialization/deserialization, and understanding the queue's delivery semantics (e.g., at-least-once, exactly-once).

### Messaging Service with Topics

- **Looser Coupling:** In a publish/subscribe model using topics, services don't directly interact with a queue. Instead, they publish messages to a topic or subscribe to a topic without needing to know about other services. This approach decouples producers from consumers—producers just emit messages to a topic, and consumers listen to the topic they are interested in without needing to know who the producers are.
- **Abstracted Interaction:** The messaging system abstracts the complexity of message distribution, allowing services to focus on their business logic rather than the intricacies of message passing. This setup facilitates easier changes and scalability, as adding or removing consumers or changing a message format can often be done without impacting other services.
- **Flexibility and Scalability:** With topics, it's easier to scale the system by adding more consumers or publishers. Since services are not tightly coupled, new services can be introduced to publish or consume messages without requiring changes to existing services.

### Example: Order Processing System

- **Direct Queue Interaction:** An `OrderService` directly puts an order into a `ProcessingQueue`, and an `InventoryService` reads from this queue. If the queue's structure or the message format changes, both services need to be updated. Additionally, if a new service like a `ShippingService` needs to process the same orders, it must also interact directly with the `ProcessingQueue`, increasing coupling and complexity.
- **Messaging Service with Topics:** The `OrderService` publishes orders to an `OrderTopic`. Both `InventoryService` and `ShippingService` subscribe to the `OrderTopic`. The `OrderService` doesn't need to know about the subscribers, and new subscribers can be added without affecting the publisher or other subscribers, leading to a more flexible and scalable architecture.

In summary, using a messaging service with the publish/subscribe model provides a more decoupled, scalable, and flexible architecture compared to direct interactions with a queue, facilitating easier maintenance and the ability to evolve parts of the system independently.

</details>

-----

Tips for Coupling: Keep things that change together in the same module(s)

Note: deriving components from domains in chapter 8


 