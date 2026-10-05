**Table of Contents**

* [What is a DDoS attack](#what-is-a-ddos-attack)
* [How does a DDoS attack work](#how-does-a-ddos-attack-work)
* [What are common types of DDoS attacks](#what-are-common-types-of-ddos-attacks)
* * [Volumetric attacks](#volumetric-attacks)
* * [Protocol attacks](#protocol-attacks)
* * [Application layer attacks](#application-layer-attacks)
* [How is a DDoS attack mitigated](#how-is-a-ddos-attack-mitigated)

[<sub>Source</sub>](https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/)

---

# What is a DDoS attack?

A distributed denial-of-service (DDoS) attack is a malicious attempt to disrupt the normal traffic of a targeted server, service, or network by overwhelming the target or its surrounding infrastructure with a flood of Internet traffic.

DDoS attacks achieve effectiveness by utilizing multiple compromised computer systems as sources of attack traffic. Exploited machines can include computers and other networked resources such as IoT devices.

>**From a high level, a DDoS attack is like an unexpected traffic jam clogging up the highway, preventing regular traffic from arriving at its destination.**

# How does a DDoS attack work?

DDoS attacks are carried out with networks of Internet-connected machines. These networks consist of computers and other devices (such as IoT devices) which have been infected with malware, allowing them to be controlled remotely by an attacker. These individual devices are referred to as **bots** (or zombies), and a group of **bots** is known as a **botnet**.

Once a **botnet** has been established, the attacker is able to direct an attack by sending remote instructions to each **bot**. When a victim's server or network is targeted by the **botnet**, each **bot** sends requests to the target's IP address, potentially causing the server or network to become overwhelmed, resulting in a denial-of-service to normal traffic.

# What are common types of DDoS attacks?

Different DDoS attack vectors target varying components of a network connection. A network connection on the Internet is composed of many different components or "layers."

## Volumetric attacks

This category of attacks attempts to create congestion by consuming all available bandwidth between the target and the larger Internet. Large amounts of data are sent to a target by using a form of amplification or another means of creating massive traffic, such as requests from a botnet.

## Protocol attacks

Protocol attacks cause a service disruption by over-consuming server resources and/or the resources of network equipment like firewalls and load balancers. Protocol attacks utilize weaknesses in layer 3 and layer 4 of the protocol stack to render the target inaccessible.

## Application layer attacks

These attacks are aimed at the layer where web pages are generated on the server and delivered in response to HTTP requests. The goal is to overwhelm the target with requests, since a single HTTP request can be expensive for the target server to respond to.

# How is a DDoS attack mitigated?

The key concern in mitigating a DDoS attack is differentiating between attack traffic and normal traffic. Several strategies exist:

**1. Rate limiting:** Limiting the number of requests a server will accept over a certain time window
**2. Web Application Firewall (WAF):** A tool that filters traffic based on a series of rules
**3. Anycast network diffusion:** Using an Anycast network to scatter the attack traffic across a distributed network
**4. Blackhole routing:** Creating a blackhole route and funneling traffic into it