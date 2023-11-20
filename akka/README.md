### Akka
Used to build large distributor systems.
Based on **actors**, that exchange information and process the information. Actors:
- take decisions (such as changing state and/or behaviour)
- send messages
- start new actors

Actors don't share memory, they just share messages. Messages are processed atomically.

Akka: implementation of the actor model for the JVM. We will deal with the "Akka classic".

##### Create Actors
Inherit from **AbstractActor** class. User has to define the createReceive method to define how to process message. Other methods are already defined but can be costumized.

To create an actor you need to
– Instantiate an ActorSystem:

```ActorSystem sys = ActorSystem.create(“sys name”);```
– Instantiate a new actor starting from a property object that defines the class of the actor

```Props p = Props.create(MyActor.class); sys.actorOf(p, “actor name”);```
– Often, the property is created and returned within a static method of the actor class

##### Send messages to actors
• Upon creation, the actorOf method returns a reference to an actor (ActorRef)
• The ActorRef remains valid throughout the actor lifecycle, even if the actor stops and restarts
• ActorRefs can be passed as part of messages to inform other actors
• ActorRefs can be used to send messages to actors using ```tell()```
Messages processed in FIFO order.

##### Replying to messages
When processing a message, an actor can
– Obtain a reference (ActorRef) to itself with the self() method
– Obtain a reference (ActorRef) to the sender with the sender() method
- To reply to the sender with a message msg, an actor simply invoke ```sender().tell(msg, self());```