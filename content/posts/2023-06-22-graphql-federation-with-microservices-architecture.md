---
template: post
title: GraphQL Federation with Microservices Architecture
slug: GraphQL Federation with Microservices Architecture
socialImage: /media/0_dvg8k-wtsfrd6x20.jpg
draft: false
date: 2023-06-22T13:18:14.747Z
description: Introducing GraphQL Federation to Microservices Architecture
category: SOFTWARE ENGINEERING
---
## Introduction

Let's get the introduction out of the way.

This article discusses my experience with graph granularity, what led us to adopt GraphQL Federation with Microservices architecture, and how we overcame some limitations of self-managed GraphQL Federation. In this article, I will provide an overview sum of multiple projects that were based on RESTful and GraphQL, and how we scaled them through various requirement stages to meet increasing demand.

To get started, the architecture is backed by a swarm of microservices hosted in the cloud, interfacing with customers through the web, Android, and iOS. Initially, microservices were directly exposed to the client application, much like in a monolith architecture. However, as our system grew in size, this approach proved insufficient. As a result, we used the [Backend For Frontend pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends), which orchestrates our microservices. An additional level of indirection was introduced between the client and API, and due to certain complexities (which I will discuss in the following section), we need a better-suited approach, which lead us to use [Apollo GraphQL federation](https://www.apollographql.com/docs/federation/) which has stabilized quite well [over the years](https://www.apollographql.com/docs/federation/enterprise-guide/federation-case-studies/). This article will delve into this topic.



![Microservices with BFF](/media/1_bx4gcqjqgfuv7edp2sgdgw.webp "Microservices with BFF")

When it comes to GraphQL specification, has grown in popularity over the last few years. If your company has product-heavy UIs and clients that aggregate data from multiple sources, GraphQL allows you to act not only as an aggregation layer but also as a query language for your APIs, allowing the client to write a single query to fetch all the data it needs to render. Because you can ask for only the data you need, GraphQL allows you to fetch exactly the data you want without over-fetching or under-fetching.

## The Need for Federated Services

There were several reasons for switching to a federated service.

* With the large size of the codebase, maintaining the applications becomes an ever-increasing challenge.
* Over-fetching/ under-fetching of data.
* To meet their requirements, many teams expended significant effort in developing duplicative data-fetching code and aggregation layers. Problems with ownership.
* The problems associated with monolithic setups like scaling, and handling traffic were difficult.

With Federation, we can simplify the client by employing its API Gateway pattern. The client’s role in calling multiple services will be transferred to the gateway. Client roundtrips will be reduced as well. Not only that, but it is also possible to implement user authentication here among many other features.

## One Graph to Rule Them All

Typically, in order to obtain all the data an application needs, a client may have to interface with several APIs. With Federation, however, clients can use a single endpoint via which we present a [unified supergraph](https://principledgraphql.com/integrity) that contains all the data they require. The implementation can also be divided thanks to this. The one schema and one API remain in place, but the schema is divided across numerous teams, each of which implements the data fetchers for its own portion of the schema.

![Supergraph](/media/1_rfi02r3jfyktyf8bks4mla.webp "Supergraph")

Apollo Federation embraced the API Gateway pattern. One graph, a supergraph, is composed of subgraphs. Each service is responsible for creating a subgraph, and the combined supergraph is exposed by the API Gateway. The API Gateway serves as a single point of entry for client applications as I mentioned.

In addition, there’s a Gateway component that acts as the single entry point for consumers and orchestrates these subgraphs based on the information that the consumer expects. To guarantee that network calls to these subgraphs are as efficient as feasible, the Gateway first devises a Query Plan.

## Be Cautious

GraphQL isn’t perfect; it’s neither the best nor the worst solution available. In some cases, GraphQL is a better fit than other solutions such as REST or gRPC. Let me share a few common pitfalls with you.

Let’s start with the performance issue. Consider retrieving both customers (User Service) and products (Store Service) with a single query, assuming a user has one-to-many relationships with products. To fulfill the query, the initial query would load customers and then call the products resolver each time. This brings us to the [N+1 problem](https://www.apollographql.com/blog/backend/performance/optimizing-your-graphql-request-waterfalls/) issue. There are a few workarounds depending on your technology, such as using a [DataLoader](https://github.com/graphql/dataloader).

Another difficulty is that ONE graph may grow to be too large for collaboration. Since it is being built by so many different teams and contains so many different types, queries, and mutations. You cannot just stand by and watch it get out of control. If you are not careful, it will bite you back. So maintaining your schemas and getting rid of the RESTful mindset is important.

While this article didn’t cover everything about the Federation, you now have many reasons to give it a try.

Happy coding!

<!--EndFragment-->