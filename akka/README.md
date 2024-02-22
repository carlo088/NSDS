## Akka
#### Lecture 1

Used to build large distributor systems.
Based on **actors**, that exchange information and process the information.

Actors:

- take decisions (such as changing state and/or behaviour)
- send messages
- start new actors

Actors don't share memory, they just share messages. Messages are processed atomically.

Akka: implementation of the actor model for the JVM. We will deal with the "Akka classic".

##### Create Actors
Inherit from **AbstractActor** class. User has to define the createReceive method to define how to process message. Other methods are already defined but can be costumized.

To create an actor you need to

– Instantiate an ActorSystem (playground, or system, where actors will run): ```ActorSystem sys = ActorSystem.create(“sys name”);```

– Instantiate a new actor. Start from a property object that defines the class of the actor. ```Props p = Props.create(MyActor.class);``` and then create actor itself ```final ActorRef counter = sys.actorOf(p, “actor name”);```

– Often, the property is created and returned within a static method of the actor class

##### Send messages to actors
• Upon creation, the actorOf method returns a reference to an actor (ActorRef)

• The ActorRef remains valid throughout the actor lifecycle, even if the actor stops and restarts

• ActorRefs can be passed as part of messages to inform other actors

• ActorRefs can be used to send messages to actors using ```tell()```. It is asynchronous, actor doesn't wait for reply

Messages processed in FIFO order.

##### Replying to messages
When processing a message, an actor can

– Obtain a reference (ActorRef) to itself with the `self()` method

– Obtain a reference (ActorRef) to the sender with the `sender()` method

- To reply to the sender with a message `msg`, an actor simply invoke ```sender().tell(msg, self());```

##### Check NSDS\_akka\_tutorial for code of Counter.java, CounterActor.java

#### Lecture 2

Communication not mediated, at most once and no order guaranteed. Called "best-effort" protocol, which is most one can hope for in distributed systems. Forces to reason on worst-case scenario, making implementation more robust, on-top-of the basic model.

**Stashing a message**: can be done by inheriting from `AbstractActorWithStash.

**Ask Pattern**: sends a message and returns a `future` which will contain the reply once it becomes available. Ex: `Patterns.ask(reciever, msg, timeout)`.

Actors distributed on different machines communicate only by knowing **address** of other actors.

To build a client/server application, one can rely on multiple communication protocols such as TCP, which will need a hostname and port. These info are kept on a configuration file and not in the akka code. When building Actor, the possibility on speaking with other machines will be specified, and client actor will need hostname and port.

#### Lecture 3

Managing faults: done through `supervisor` of actors (which are actors themselves), which can terminate and restart faulty actors. They are organized in supervision trees. They can also source data and delegate other actors.

**Fault tolerance**: actors are supervisors of what they create. Create through `getContext().actorOf()` and stop with `getContext().stop()`. Supervision strategy can be overrided by changing the `supervisionStrategy()`.








