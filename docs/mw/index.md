# Middleware reference


## Overview

Nova Middleware is the communication library that enables data exchange between distribute software components.
It follows the common [publish/subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) pattern, where data producers (called [publishers](#publishers)) do not talk directly to specific consumers, but instead they published [messages](#messages) on corresponding [topics](#topics).
Similarly, data consumers (called [subscribers](#subscribers)) express interest in one or more topics and only receive messages that are of interest, without knowledge of which publishers, if any, there are.

## Nodes

Nodes are software components performing simple tasks, with the goal of satisfying a specific functional requirement or a part of it. An application is then implemented as a network of nodes, running on the same module or distributed on several modules.
Nodes provide modularity from the software point of view: complex activities to achieve a goal are split in multiple tasks solving a smaller and well defined problem.
Then, nodes can be reused in different projects showing common needs, speeding up the development and improving maintainability.

```c++
core::mw::Node mynode("node_name");
```

## Messages

The Nova Middleware follows the type-based publish/subscribe paradigm: a topic does not only identify the subject of messages being exchanged, but also their data type.
The strong typed definition of data flows improves the decoupling between producers and consumers, as referring to the same topic guarantees that the content of exchanged messages is consistent.
Nodes can then be updated, or even replaced, with any other implementation which publishes and subscribes the same topics.

The content of messages is specified by defining standard C++ classes, composed by typed fields, and such specification is common to all nodes.
Message definitions must inherit from the core::mw:Message class

```c++
class Timestamp : public core::mw::Message {
	uint32_t sec;
	uint32_t nsec;
} CORE_PACKED;

class IMUAttitude : public core::mw::Message {
	float roll;
	float pitch;
	float yaw;
} CORE_PACKED;
```

## Topics

Topics represent the channel used by nodes to actually exchange messages about a particular subject.
Each topic is identified by a string, the topic name, and they are uniquely identified on the network as module name/node name/topic. Topics are instantiated when they are first referred by an advertisement (the creation of a publisher), or by a subscription (the creation of a subscriber).

## Publishers and Subscribers

Publishers and subscribers are the mediators used by nodes to communicate.
They are declared within nodes, defining the data they are going to produce and the data they need to operate.
In other words, by declaring its publishers and its subscribers, a node specifies the output and input ports to connect with other nodes.

### Publisher node

The following code instantiate a publisher of messages of type "LedMsg" on the topic "led".

```c++
/*
 * Led publisher node
 */
void ledpub_node(void * arg) {
	Node node("pubnode");
	Publisher<LedMsg> led_pub;
	bool state = 0;

	node.advertise(led_pub, "led");

	for (;;) {
		LedMsg *msgp;
		if (led_pub.alloc(msgp)) {
			msgp->led = 1;
			msgp->value = state;
			state = !state;
			led_pub.publish(msgp);
		}

		chThdSleepMilliseconds(500);
	}

}
```

### Subscriber node


