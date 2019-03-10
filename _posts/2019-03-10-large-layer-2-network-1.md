---
layout: post
title:  "Large layer 2 network (1) : why we need large layer 2 network"
date:   2019-03-10 11:07:03
categories: cloudnetwork
tags: cloud network
---

* content
{:toc}

As entering into cloud networking area, I've seen a lot of talking about large layer 2 (L2) network. To me, it's one of key technology that applied in modern datacenter, meeting the fast growing cloud service needs. So I decide to post a series of blogs to present what's the idea of large L2 network and how the datacenter architecture move to there. In this first post, I am gonna talking about why the world need a large L2 network.

## New era of IT infrastructure
If you are working at technology industry, you must be aware of  those public cloud companies, like Amazon AWS, Microsoft Azure, etc. In the beginning, these public cloud companies provide IAAS (Infrastructure as Service) to let other companies rent their Virtual Machines. To achieve that, public cloud companies use all kind of virtualization technology (compute/storage/network virtualization) to make one physical sever can run several VMs. 

This new business introduced new requirements for the network. Some basic requirements are BYOA (Bring Your Own Address) and live migration. The customers want to assign ip address to their VM, so it's easy for them to manage their infrastructure. Also to improve availability, cloud provider must support live migrate their VM, as no network connection being interrupted, and customers don't even notice their VM get migrated. 

However, legacy network architecture is not able to support that new business requirement. To understand why legacy can't make it, we have to first understand the legacy data center network architecture.

## Legacy Network Architecture
In legacy network architecture, it has a bunch of mature technology to make sure one packet being delivered to another. 

![image](/_assets/NetworkArchitecture.png) 

L2 switches handles LAN(Local Area Network) or VLAN(Virtual Lan) network.L2 switch forwards packets to the right port by looking up the MAC table which matches the destination mac address. If no matching mac address, L2 switch would broadcast packet to all ports. In real network world, usually there are redundant network path for network L2. One obvious problem is that if every switches broadcast packets when failing to look up its mac table, there would be circles, which can bring down whole network. STP (Spanning Tree Protocol) is created to solve this problem to eliminate cycles in a network. 

If we thinking about a L2 network as small town, the locations within the town can be reached via the local road. However, people must take the highway to reach the another town or even another state. We can thinking about these highway as L3 network to handle cross LAN/VLAN traffic. L3 routers is like the interstate fork in the highway. Each router has a routing table showing what's the next hop should the packet go like what the sign does in the highway. The way that whole network build the routing table is called L3 routing. The routing protocols include BGP, OSPF, IS-IS etc. 

## Problems of legacy network
Like we said, L2 network handles LAN network (like local roads in a town) and L3 network handles cross-lan/vlan traffic (like highways). This network architecture works perfectly for traditional network scenarios, like a company or school network. But for public cloud provider companies, it's not capable of handling a few key scenarios. One important is VM live migration, which requires VM keeping all connections alive during migrating to anther physical sever, which can be anyware in the datacenter. 

Think about in real life, when you move to another place within the same state, you life won't be change too much. Because you still have all records in the government, like your ID, tax, insurance, etc. But if you move to another country, that won't be same situation. You are gonna set up everything all over again. In traditional data center, VM moves  to another VLAN is like move to different country. It must have a new ip address and update the mac address. As we know, we can't keep the existing connection alive when the mac and ip address get updated. 

So modern cloud datacenter needs to have a way to manage large volume of ip and mac address to ensure VMs being removed free as much as possible. However VLAN + STP can't manage a large network more than 1k nodes. Like we mentioned before, one of L2 network is that circular forwarding in the switches. To prevent broadcast not eating too much network bandwidth, VLAN can't manage too many nodes. 

In real cloud datacenter, it requires at least 10k nodes in L2 to meet various cloud network scenarios. That's why we called a large L2 network.

## Summary
The legacy network architecture, VLAN+STP manages L2 network. VLAN limits the broadcast scope and STP breaks circular forwarding path within VLAN.VLAN can only mange less than 1k nodes, however in cloud world, it requires to have > 10k nodes L2 network, to meet VM live migration needs. And this is why we need large L2 network.  In the next post, I will talk about what's the solutions to build the large L2 network.