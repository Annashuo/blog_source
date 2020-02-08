---
title: Distributed System - Network Communication
date: 2020-02-04 11:57:57
tags: distributed system
categories: STUDY
---

<!--more-->

### TCP / IP Network Model

![](https://raw.githubusercontent.com/Annashuo/hello-world/master/4_network_layer.png)

#### Layer 1 Data Link
- Physical delivery of data over a single link
- In charge of 
	- Encapsulation of the data
	- Flow control
	- Error detection
	- Error correction
	- etc...
- Ethernet Protocol - wraps data packets into frames and uses the devices' MAC addresses to deliver those packets from one device to another device
- Data link is like the post office

#### Layer 2 Internet
- In charge of delivery across multiple networks and rout the packets from the source computer to the destination computer.
- Internet Protocol(IP) - each device in the network is a sign, an IP address, and this address allows the packets to travel across the networks from the source host to the destination host
- It doesn't know which application process the packet is intended to nor does it know which process sent it and where the response should go to.
- IP address is like the building address

#### Layer 3 Transport
- Take care of delivering the messages end to end from the process on one machine to another process on the other machine.
- The listening port is chosen ahead of time by the destination application but the source port is generated on the fly by the sender.
- Using the port, the operating system knows which process the packet belongs to
- The port is like the appartment address

##### User Datagram Protocol (UDP)
- Connectionless
- Best effort - Unreliable
- Allow Broadcasting - decoupling between the sender and receivers
- Messages can be 
	- Lost
	- Duplicated 
	- Reordered
- Based on a unit called *Datagram* which is limited in size
- Is preferred when the speed and simplicity is more important than reliability
- Example 1 - sending debug information
- Example 2 - Real time data stream service such as video or audio
- Example 3 - Online Gaming

##### Transmission Control Protocol (TCP)
- Reliable - Guarantees data delivery as sent, without any losses
- Connection between 2 points
	- Needs to be created before data is sent
	- Shut down in the end
- Works as a streaming interface
- Each TCP connection is uniquely identified by the four tupo of source IP, port and destination IP and port.

#### Layer 4 - Application
| **Protocol** | **Purpose** |
|:------:|:------:|
|FTP(File Transfer Protocol)|Transfering files through the web|
|SMTP(Simple Mail Transfer Protocal)|Sending and receiving emails|
|DNS(Domain Name System)|Translating host names into IP address|
|HTTP(Hypertext Transfer Protocol)|Transmitting Hypermedia documents, video, sound, images|

### HTTP
#### Request Structure
![](https://raw.githubusercontent.com/Annashuo/hello-world/master/HTTP_Request.png)
##### Method - Get, Head, Post, Put, Delete, Trace, Connect, Options...
- Get
	- Safe - Only retrieval action with no side effect
	- Idempotent - Performing N times is equivalent to performing once
	- **Does not contain a message body**

- Post
	- Contains a message body
	- The operation may heve side effect and we expect the server to perform a complex operation and give us a result

##### Path
```
http://IP:port/path?query_string
http://123.85.46.96:8080/users?userid=123&month=03
```
##### Protocol Version
- HTTP/1.1
	- create a new connection for every request
	- The number of outgoing connections a client can maintain is limited by number of ports and the operating system
	- If one connection breaks, the other stay unaffected
- HTTP/2
	- Interleave multiple requests and responses on the same connection. This interleaving is completely transparent to us and is done by logically breaking the connection into multiple internal streams.

##### HTTP Headers - key/value pairs of strings
```
Header-Name:Value1;Value2;Value3
For example, 
Content-Length:33
Content-Type:xxx
Content-Encoding:xxx
```
- In HTTP/1.1 - plain text key value pair that can be easily inspected by tools like *Wireshark*
- In HTTP/2 - the headers are compressed, save on payload size and harder to inspect or debug

##### Message Body
- Contain anything
- The server and client have to agree on how to parse the data

#### Response Structure
![](https://raw.githubusercontent.com/Annashuo/hello-world/master/HTTP_Response.png)
##### HTTP Status Codes
|Status Code|Group|
:------:|:------:
|1xx|Informational Response|
|2xx|Success|
|3xx|Redirection|
|4xx|Client Errors|
|5xx|Server Errors|


### Curl - Command line Http Client
- --request HTTP_METHOD
- --header HEADER_LINE
- --verbose(-v)
- --data SOME_DATA
- HTTP server address

```
curl --request GET -v localhost:8081/status
curl --request POST -v --data '80,100' localhost:8081/task
curl --request POST -v --header "X-Test: true" --data '80,100' localhost:8081/task
curl --request POST -v --header "X-Debug: true" --data '80,100' localhost:8081/task
```

### Message Delivery Semantics in Distributed System
#### At Most Once Semantics
- The client send a request to the server only once
- If the server never receives a response or the server crashes right before it sends back the response, the client will never redeliver the request to the server

##### Use Cases
- Messages to Logging or Monitoring service
- Sending promotional emails/notifications to users

#### At least Once Semantics
- If the client does not receive a reponse, it will resend the request
- If the server did perform the request action, but failed to respond, the operation will be executed multiple times
- The delivery semactics works only for <u>**idempotent operations**</u>

##### Idempotent operation
An operation that can be performed multiple times, and will have the same result/effect as if it was performed only once.

### Serialization & Deserialization
- **Serialization** - Translation of a data structure or object into a format that can be sent(or stored somewhere) and reconstructed later
- **Deserialization** - Reconstruction of the data back to a data structure or an object

#### Serialization Format
##### Json - JavaScript Object Notation
- Advantages
	- Simple and hyman readable
	- Programming language independent
	- Easy integration with JavaScript/Front-End
- Disadvantages
	- Java does not know how to map a Java class to/from Json object, requires explicit serializtion and deserialization using external libraries. Those libraries need explicit configurations andn annotation to tell them how to serialize and name the fields.
	- Json does not have a schema(source of truth)
	- Plain text parsing and transmission is suboptimal

##### Java Object Serialization
- Advantages
	- Guarantees about corrent state reconstruction without any type ambiguity
	- Very clear source of truth(schema) for the object
	- Native support in all the JVM languages
		- No need for external libraies
		- Simple development process
- Disadvantages
	- Not human readable, harder to test
	- Tight coupling to JVM languages

###### The Serializable Interface
- *Serializable* Interface is an empty "marking" interface
- Objects of classes implementing *Serializable*, can be serialized into a byte stream and recontructed into original object's state
- All the members of the class are going to be automatically serialized except for 
	- Static members
	- Transient members

```
public class Data implements Serializable {
	// non serializable
	static String var1;
	transient int var2;	
	
	// serializable
	String city;
	List<Date> dateRange;		// Date Object must be serializable, or throw InvalidClassException
	Set<String> categories;
	int[] ageRange;
	boolean debug;
}
```
###### Java Object Serialization
```
public byte[] serialize(Data data) {
	ByteArrayOutputStream byteStream = new ByteArrayOutputStream();
	ObjectOutput objectOutput = new ObjectOutputStream(byteStream);
	objectOutput.writeObject(data);
	objectOutput.flush();
	return byteStream.toByteArray();
}
```
###### Java Object Deserialization
```
public Data deserialize(byte[] data) {
	ByteArrayInputStream byteStream = new ByteArrayInputStream(data);
	ObjectInput objectOutput = new ObjectInputStream(byteStream);
	return (Data)byteStream.readObject();
}
```
	
##### Google's Protocol Buffers
- Advantages
	- No type ambiguity
	- Clear and well defined schema
	- Language independent thanks to the 2 step process
		- Proto file definition
		- Language specific stub generation using proto compiler
	- Efficient serialization & deserialization
	- Security
- Disadvantages
	- Not human readable
	- Hard to debug
	- More complex development process

##### Key Considerations
- <u>human readable</u>, <u>language independent</u> with <u>native support in the browser</u> - <font color=red>JSON</font>
- <u>simple</u>, <u>fast to develop</u> with <u>native JVM support</u> - <font color=red>Standard Java Object Serialization</font>
- <u>performance</u>, <u>bandwidth</u> and <u>security</u> are more important - <font color=red>Protocol Buffers</font>




