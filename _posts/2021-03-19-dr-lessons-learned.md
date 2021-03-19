---
layout: post
title: Some Disaster Recovery Lessons Learned
image: ../img/DR/cloud.jpg
tags: [ERP, DR]
---

Over the last year, I've been working on planning and implementing disaster recovery capabilities from the ground up. This means developing a way to continue to run a complex ERP system if our on-prem data center blew up. This is an on-going project, but since we've just completed our first phase it's a good time to step back and discuss how things are going and the challenges involved.

### Basic Approach

The goal is to get the critical functions of our on-prem ERP system to be able to run through a cloud provider, if everything broke on prem. This involves replicating our on-prem servers to the cloud provider and in the case of a disaster, bringing up those servers in the cloud and running our ERP. Sounds simple, right?

### Moving Data is Easy, Making Everything Run is Hard

After you set up the basics of your cloud environment (mostly allowing it to talk to your on-prem network), you can use a software like [Zerto](https://www.zerto.com/) or [CloudEndure](https://www.cloudendure.com/) to start replicating your servers to the cloud. There can be gotchas here. You may need to specify a machine type for your target machines in the cloud, and there can be poorly-documented operating system incompatibilities. However, this can be solved with some quick guess and check. Can you successfully start the replicated server in the cloud? If not, try a similar machine type until it works.

At this point, you have a bunch of servers that you can now boot up in the cloud. So far, so good...so what now? If you just need to directly interact with a database, you may be able to connect to it and do that. But most applications involve multiple servers working together with some sort of external authentication system; how does that work? This is where the hard questions and brainstorming starts.

### Authentication

Unless your ERP system has built-in authentication, your users log in using SAML, CAS, or some other protocol with an identity and access management system. Is this system on prem? If so, you'll need to make sure you are replicating those servers to the cloud, as well. Is this system looking at a domain controller (or multiple domain controllers) to authenticated users? You'll need to bring those domain controllers with you to the cloud as well. Grappling with these issues led us to consider permanently moving our authentication provider off premise, so we would have one less thing to worry about in a DR scenario.

### Load Balancing + DNS

If your ERP configuration depends on a load balancer, then that is yet another thing that you will have to replicate to the cloud or you'll need to script out how to remove these dependencies if you fail over to the cloud. Similarly, do you have an on-prem DNS server managing your ERP? If so, that has to also get replicated. This is another area where you can consider permanently moving to the cloud by maintaining DNS with a service like [Amazon's Route 53](https://aws.amazon.com/route53/) or [Azure DNS](https://azure.microsoft.com/en-in/services/dns). All of these pieces can make testing difficult if you want to test out your DR solution in the cloud, while still keeping your ERP running on prem.

### Summary

Disaster recovery for any sort of complicated application is hard. Although a cloud is often the obvious choice for a DR location, there are challenges with moving back and forth between your data center and the cloud. Cloud-only applications have the advantage of easier DR and resiliency, but migrating a system to a cloud is a large business and process change - and a topic for a future post!
