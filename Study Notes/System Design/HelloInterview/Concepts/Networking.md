# Networking 101

Networking is about connecting devices and enabling them to communicate. Networks are built on a layered architecture (the so-called ["OSI model"](https://en.wikipedia.org/wiki/OSI_model))

Network layers are just abstractions that allow us to reason about the communication between devices in simpler terms relevant to our application.

## Networking layers

![[Networking.webp]]

These are the OSI layers.

###### Network Layer (Layer 3)
At this layer is IP, the protocol that handles routing and addressing. It's responsible for breaking the data into packets, handling packet forwarding between networks, and providing best-effort delivery to any destination IP address on the network.

###### Transport Layer (Layer 4)
At this layer, we have [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol), [QUIC](https://en.wikipedia.org/wiki/QUIC), and [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol), which provide end-to-end communication services. Think of them like a layer that provides features like reliability, ordering, and flow control on top of the network layer.

###### Application Layer (Layer 7)
At the final layer are the application protocols like DNS, HTTP, Websockets, WebRTC. These are common protocols that build on top of TCP (or UDP, in the case of WebRTC) to provide a layer of abstraction for different types of data typically associated with web applications. We'll cover them in depth.

#### Example
![[Networking-1.webp]]
1. **DNS Resolution**: The client starts by resolving the domain name of the website to an IP address using DNS (Domain Name System).
2. **TCP Handshake**: The client initiates a TCP connection with the server using a three-way handshake:
    - **SYN**: The client sends a SYN (synchronize) packet to the server to request a connection.
    - **SYN-ACK**: The server responds with a SYN-ACK (synchronize-acknowledge) packet to acknowledge the request.
    - **ACK**: The client sends an ACK (acknowledge) packet to establish the connection.
3. **HTTP Request**: Once the TCP connection is established, the client sends an HTTP GET request to the server to request the web page.
4. **Server Processing**: The server processes the request, retrieves the requested web page, and prepares an HTTP response. (This is usually the only latency most SWE's think about and control!)
5. **HTTP Response**: The server sends the HTTP response back to the client, which includes the requested web page content.
6. **TCP Teardown**: After the data transfer is complete, the client and server close the TCP connection using a four-way handshake:
    - **FIN**: The client sends a FIN (finish) packet to the server to terminate the connection.
    - **ACK**: The server acknowledges the FIN packet with an ACK.
    - **FIN**: The server sends a FIN packet to the client to terminate its side of the connection.
    - **ACK**: The client acknowledges the server's FIN packet with an ACK.

The TCP layer ensures that the data is delivered correctly and in order, and will provide a response to the application if it doesn't arrive. We also never have to concern ourselves with finding a specific server in the world. With DNS, we can look up the IP address, and with IP the various networking hardware between us, our ISP, backbone providers, etc. can route the data to the destination.

While we have one conceptual "request"(sent from the client) and "response"(returned by the server) here, there were many more packets and requests exchanged between servers to make it happen.

Note that the connection between the client and server is a **state** that both the client and server must maintain. Unless we use features like HTTP keep-alive or HTTP/2 multiplexing, we need to repeat this connection setup process for every request - a potentially significant overhead (There are ways to have persistent connections).

## Network Layer Protocols
This layer is dominated by the IP protocol, which is responsible for routing and addressing. I can create a private network with my servers and give them any IP address. These are called **private IPs** and they are not internet facing.

If you want internet traffic to be able to find them you'll need to use IP addresses that are routable and allocated by a Regional Internet Registry(RIR). These are called **public IPs** that a client can connect to over the internet.

## Transport Layer Protocols
The transport layer is where we establish end-to-end communication between applications. They give us some guarantees instead of handing us a jumbled mess of packets. The three primary protocols at this layer are TCP, UDP, and QUIC, each with distinct characteristics that make them suitable for different use cases.

### TCP: Reliable but with Overhead
Transmission Control Protocol (TCP) is the workhorse of the internet. It provides reliable, ordered, and error-checked delivery of data. It **establishes a connection through a three-way handshake** (we saw this illustrated above with the HTTP example) and maintains that connection throughout the communication session.

This connection is called a "stream" and is a **stateful connection** between the client and server — it also gives us a basis to talk about ordering: two messages sent in the same stream/connection will arrive in the same order. TCP will ensure that recipients of messages acknowledge their receipt and, if they don't, will retransmit the message until it is acknowledged.

#### Key Characteristics of TCP

1. **Connection-oriented**: Establishes a dedicated connection before data transfer
2. **Reliable delivery**: Guarantees that data arrives in order and without errors
3. **Flow control**: Prevents overwhelming receivers with too much data
4. **Congestion control**: Adapts to network congestion to prevent collapse

TCP is ideal for applications where data integrity is critical.

### UDP: Fast but Unreliable
User Datagram Protocol (UDP) is the machinegun of protocols. It offers few features on top of IP but is very fast. **Spray and pray** is the right way to think about this. It provides a simpler, connectionless service with no guarantees of delivery, ordering, or duplicate protection.

If you write an application that receives UDP datagrams, you'll be able to see where they came from (i.e. the source IP address and port) and where they're going (i.e. the destination IP address and port). But that's it! The rest is a binary blob.

#### Key characteristics of UDP include:

1. **Connectionless**: No handshake or connection setup
2. **No guarantee of delivery**: Packets may be lost without notification
3. **No ordering**: Packets may arrive in a different order than sent
4. **Lower latency**: Less overhead means faster transmission

UDP is perfect for applications where **speed is more important than reliability**, such as live video streaming, online gaming, VoIP, and DNS lookups. In these cases the application or client is equipped to handle the occasional packet loss or out of order packet.

> [!NOTE]
 Browsers don't have widespread support for UDP yet outside of WebRTC. So it's best used in mobile apps.

### When to Choose Each Protocol
Usually, TCP is the default. UDP can be used when low latency is critical, such as online gaming, or when some data loss is acceptable(like video streaming).

Modern applications often use both protocols. For example, a web-based video conferencing app might use TCP/HTTP for signaling and authentication but UDP/WebRTC for the actual audio/video streams.