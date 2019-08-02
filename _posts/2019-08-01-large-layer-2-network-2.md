---
layout: post
title:  "Large layer 2 network (2) : How to create large layer 2 network"
date:   2019-08-01 11:17:03
categories: cloudnetwork
tags: cloud network
---

* content
{:toc}

In the previous blog, we've seen that legacy L2 network is not fit for the modern datacenter requirement. Lots of organizations came up with the different solutions  to address this problem. In overall, all solutions can be put into three different categories. In this post, I will go through major ideas about how to build up a large 2 network.

One key reason that makes L2 network cannot scale is STP. Since in most network architecture, there are redundant network topology to improve the network reliability. However, in a redundant topology, there would be cycles. L2 network is not smart as L3 network. There is no routing on L2 switches, and switches forward packets based on the mac table. If the switch sees a unknown mac address, it will broadcast the packet to all the ports. If we don't do anything for L2 network, these broadcast could eat up all the network bandwidth.  And L2 packet doesn't have ttl, that makes packet loop through the L2 network forever. To solve that problem, we introduced the STP protocol, which blocks the redundant ports to prevent the packet dead loop. Since STP can be very expensive, usually it only runs efficiently in a small size network nodes. Here is the VLAN coming into the picture, which helps limit the STP scope.  So how to optimized STP or even prevent using STP would be the pain point to build large L2 network. 

## Network device virtualization
STP solves the problem of breaking the network loop in the redundant L2 network. If we remove the redundant devices, then we don't need the STP. Network device virtualization is using this idea to solve this problem. It will put multiple redundant device together and unite it as one device. This technology combining with link aggregation would make network topology as a tree structure which never generates a cycle. 

The famous solution using this idea is CISCO VSS, Huawei CSS, etc. Customers need to purchase these device and manage L2 devices, and also need to buy maintain service from these vendor companies. Limited to the number of devices that this solution can manage, it usually can support up to 10k - 20k severs. It's feasible that using this solution to manage small and mid size datacenter.

## Layer 2 routing protocol
Another idea to realize big layer 2 problem is to introduce routing in the layer 2. In layer 3, it doesn't need a STP to break down the network loop, because every node in layer 3 know the overall network topology and always make the best forwarding decision. However in layer 2 network, all nodes only know their neighbor, and have to broadcast the packet when seeing unknown destination. In the early generation of network technology, layer 2 switch device is expensive and we only want layer 2 network device doing the simple work .

One of famous layer 2 routing protocol is TRILL(Transparent Interconnection of Lots of Links), which is created by Radia Perlman who came up with STP and IS-IS. With no surprise, TRILL borrow the idea of IS-IS, each node deployed with TRILL will compute the shortest distance to the rest of nodes using SPF (Shortest Path First). It supports ECMP as well when there are multiple equal distance path. With the knowledge of full topology, TRILL can prevent cycle in the network.

Since TRILL can easily solve the traditional layer 2 problem, currently companies like CISCO, Huawei,  Broadcom, Juniper support it.

![image](/assets/DeviceVirtual.png) 

## Overlay 
The previous two solutions are hardware solutions which replying on buying the supported devices. However, in the modern datacenter, Overlay technology is becoming more and more popular and being deployed in the large datacenters. Overlay means encapsulate packets with another header, which is also called tunnel. The most famous encapsulation protocols are VXLAN (MAC in UDP) and NVGRE (MAC in GRE). The packets will be encapsulated when entering the tunnel and decapsulated after leaving the tunnel. 

This solution can be deployed based on the current network device and technology. It's easy to scale and completely support large layer 2 network. The architecture of the Overlay network is like a giant layer 2 switch connecting to all network nodes. As a replacement of VLAN id, VXLAN has 24 bit VLAN ID and NVGRE has 24 NVGRE ID, which can support  16M virtual networks.

![image](/assets/Overlay.png) 

## Summary
In this blog,  we talk about three solutions to solve the large layer 2 problem. Network device virtualization can be used in a small datacenter. Traditional network device companies come up with layer 2 routing solution to scale the layer 2 company, and software companies use overlay solutions based on existed network architecture. Genetically overlay solution supports the scale and flexible Layer 2 network, and nowadays it's majorly used in large global datacenters. 