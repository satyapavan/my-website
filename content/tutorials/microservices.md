---
title: "Microservices"
date: 2019-10-29T10:07:47+06:00
draft: false

# post thumb
image: "images/featured-post/starwars_destroyer.jpg"

# meta description
description: "two microservices and their shared database"

# taxonomies
categories: 
  - "Tutorial"
tags:
  - "microservices"
  - "monolithic"
  - "architecture"

# post type
type: "post"
---

# Microservices: The Big Picture
1> Microservices Architecture by Rag Dhiman

2> Modern Software Architecture by Dino Esposito

## What Are Microservices?

The main difference between the SOA and microservices is that, though they both develop a single application as a suite of small services, in microservices, we deploy each of these services in their own process and communication with each other (in a lightweight manner, i think this is where json/rest comes into picture)

Each microservice live independently, but they rely on each other to achieve the task.
1. Bounded context
2. Autonomously developed
3. Independently deployable
4. Exchange messages (should be light weight)
5. Team more agile
6. Faster to develop (parallel teams)

TODO: Bounded context

## Microservices Elements

### Building a Monolith

Monolith

PRO's
- Simple to develop
- Simple to build
- simple to test
- simple to deploy
- simple to scale

Cons
- Difficult for new team members
- Growing teams
- Code harder to understand
- Difficult for emerging tech
- Scaling - need to scale the whole app and not just the required service's
- Overloaded container
- huge database

### Building Microservices

Sub domains should be independent and separate. One technique is to duplicate the dependencies in both domains. eg. trail or service.

I am not a big fan of this though.
TODO: Look for alternative patterns

### Organization
In MS, teams are more independent and responsible as the sub domains are modeled that way. The team sizes can be different and easily managable

The teams can have their own documentation formats, repo and technologies.

### Data Store
Since services are independent, they have their own data management rules and hence it helps to have their own data source.

Transaction heavy, some are write mostly and some read only. One may need a relational solution and another a noSQL solution. This kind of heterogeneous solution is only possible with MS.

But the concept of individual data sources create the problem of data synchronization (specifically the dependencies that we have duplicated across domains).

There is no distributed transaction concept in MS, as that will lead to low performance. So we will begin with immediate consistent and then move to eventual consistency (this is where event sourcing like kafka comes)

TODO: Read about Debezium for "Capture Data Change"

Data Source : Akka, Kafka, Rabbit MQ

### User Interface

UI: Helps to have a composite UI, which is going to make multiple async calls and displays the data as and when it loads. eg:amazon

### Services

Services: They need to communicate in some way,

- "Remote Procedure Invocation" or RPC.
This is based on request and reply concept and could be sync or async.
REST, SOAP, gRPC
- "Messaging" via a broker or channel using a publish and subscribe.

Async communication helps a lot to maintain things loosely coupled in MS architecture. They also improve the availability since the broker buffers the message till the consumer is able to process them

Messaging : Kafka, Rabbit MQ

Protocol Format Exchange
- TEXT : XML, JSON, YAMLS (human readable and easy to process)
- Binary : gRPC (more compact)

APIs and Contracts
- Each MS need to publish a contract containing the set of services on what is available and how to invoke them
- Depending on the protocol that we are using we will be using WSDL or Swagger
- To suit multiple devices and needs, you can also have multiple API's and different device specific contracts

### Distributed Services

Service Registry
- It is a phone book of services with their locations, letting clients look up services by their logical names.
- We can hardcode the hostnames and their ports, but that's not reliable. To make our app more reliable and scale up for more instances, we need Service Registry
- Each service on start does a self registration to keep the registry uptodate for later lookup
- Eureka, Zookeeper, Consul

CORS
- Cross Origin Resource Sharing
- We can only interact with documents on the same server as the current service
- Origin = protocol + server + port
- In MS, we deploy each service in different server's, so this is a problem
- Need to resolve by using the HTTP Headers "Access-Control-Allow-Origin"
- TODO: Are we not relaxing security here? Is this the right thing to do?

Circuit Breaker
- When we make service chaining (calling multiple services) and when one of the service is down, it leads to a cascade of failure and we call it "Domino Effect"
- One failure in one system can lead the whole system to fail.
- We resolve it by invoking a proxy that deviates the calls if it see failure more than a threshold
- After timeout, it starts allowing calls or reintroducing traffic in limited numbers till the flowthrough is high.
- TODO: Where will it deviate during failure? what will happen to the user action?
- Hystrix, JRugged

Gateway
- Can be used for resolving cross-cutting concerns like security
- Acts as the single entry point to the service
- Can handle authorization and authentication here rather than being duplicated in each service call.
- Unified Interface
- Can handle API Translation
- Zuul, Netty, Finagle

### Security

Security
- Not specific to MS, this is the per-requisite for all now
- Identify and Access Management system can be delegated to take care of authorization and authentication
- Gateway can take care of this and with Single Sign-On, individual services need to replicate the same.
- authentication protocols : Kerberos, OpenID, OAuth 2.0, SAML
- IAM: Okta, Keyclock, Shiro

Access Token
- it securely stores information about a user and is then exchanged between services
- helps to communicate the identity of the requester between services without the need to re-authenticate every time.
- just need to verify the token and see if the user is authorized for the operation (without the need to authenticate)

### Scalability and Availability

Scalability
- Vertical Scaling : More CPU and RAM (i think is this used for monolithic, this is also the CON of monolothic)
- Horizontal Scaling : Service Replication across multiple servers. This is where load balancer comes into picture.
- Ribbon, Meraki

### Monitoring

Monitoring
- Many moving parts and machines to monitor
- Needs to be centralized
- Good to be Visual
- Kibana, Grafana, splunk

Health Check 
- service could be running but incapable of handling requests, due to connection issues?
- need a health check API to perform various checks
- the health check is similar to heart beat and can be called periodically.

Log Aggregation
- Helps to analyze and search the logs with more end-end infor
- logStash, Splunk, PaperTrail

Availability
- probability for the system to be available at a given time.
- apps are usually 99.999% available.
- Single Point of Failures like a single Gatway, a single broker or a single IAM could bring the whole system down

TODO: Check on Dapper, HTrace, Zipkin for Distributed Tracing

### Deployment

- Metrics
- Auditing
- Rate limiting (to save from DDos attacks)
- Alerting
- Distributed Tracing (correlation id and chain of calls)

## Are Microservices Right for Your Organization?

TODO: Very nice slide summary (Microservices: The Big Picture)


# Microservices Architecture

## Introduction

### What Is a Service?

Services are stateless. So the instance of the service doesn't need to remember the previous request/call of the call to that service, it has all the info that it needs in the request. 

### Microservices Introduction

Microservice architecture is SOA done right. Its an evolution of SOA, it builds upon its principles.

SOA led to monolithic, but the concept it right. Microservice is a better implementation of it.

Microservice makes use of lightweight communication, both between,
- client to service
- service to service

Microservices needs to have a Technology Agnostic API, this means they need to follow a open communication protocol so that it does not dictate the tech. This enabled a .NET to talk with a JAVA service.

Independently
- changeable
- deployable

### The Monolithic

In Monolithic, a minor change could result in complete rebuild of the application

The only advantage of a monolithic over microservice is that the whole application runs on a single machine and hence it is easy to replicate and configure environments. 

### Design Principles Introduction

Microservice Design Principles
- High Cohesion
- Autonomous
- Business Domain Centric
- Resilience
- Observable
- Automation

High Cohesion
- Single focus
- Single responsibility
- Do one thing and only one good thing
- Similar to encapsulation principle
- Easily rewritable

Autonomous
- Loose coupling
- Change to a MS should not be the reason for the change in another MS
- Honor contracts and interfaces
- Stateless
- Independently Changeable and Deployable
- Concurrent development

Business Domain Centric
- Bounded context
- scope of service

Resilience
- Embrace failure either by Degrade functionality (like in amazon, show only what is available) or default functionality
- Multiple instances
- Network failures and other kind of failures

Observable
- System Health
- Centralized Monitoring
  - For capacity or scaling
- Centralized logging
  - For debugging distributed transactions

Automation
- Automate everything

## Microservices Design

### High Cohesion
- Identify a single focus
- Split into finer grained services
- Avoid "Is kind of the same"
- Don't get lazy by the additional work in creating/splitting a current service into multiple

### Autonomous
- Loosely coupled
 - Achieved by communicating with network
  - Synch - this has an advantage to know if the call is success before going to the next step. But in reality, i do not think so. These can be achieved easily with async too
  - Asynch - Publish events and others subscribed to event
- Asynch is the most decoupled form of communication
- Avoid client libraries, this increased coupling
- Use  Technology agnostic API (like JSON/REST)
- But DO NOT overdo the communication and create a chatty exchange between services (i think that's where DDD comes in future  for this)
- Avoid sharing things like database between services. It has shortterm benefits, but create high coupling in futures.

### Autonomous: Ownership and Versioning

Versioning
- Create a new version of the service if there are breaking changes on the contract and is not backward compatible.
- Maintain concurrent versioning when we need more time to decom the old version
 - Co-existing endpoints
- Semantic versioning
 - Major.Minor.Patch (e.g.: 15.2.1)
 - Major = non-backward compatible
 - Minor = backward compatible
 - Patch = bug fix

### Resilience


- Design for known failures
- Failure of downstream systems
- Degrade functionality on failure detection
- Default functionality on failure detection
- Design system to fail fast (no value in doing a degrade/default func after 5min, it should be seamless)
- Use timeouts
 - Timeout after a threshold
 - service to service
 - service to other systems
- network outages and latency too, not just system/service issues
- Monitor and log timeouts

### Observable: Centralized Monitoring

- Centralized Monitoring
 - Real time monitoring
 - Monitor the host (CPU, memory, disk usage)
 - Expose metrics within the service
  - Response times
  - Timeouts
  - Exceptions and errors
 - Business data related metrics
  - Number of orders
  - Avg. time for checkout
 - Collect and aggregate monitoring data
 - Visualize the monitoring data for trends
 - Tool to compare data across servers
 - tool that can trigger alerts based on thresholds/inputs

### Observable: Centralized Logging

- Centralized logging
 - When to log
  - Startup or shutdown
  - code path milestones
  - timeouts, exceptions
 - Structured logging
  - Level (Info/debug/error/stat)
  - Data and time
  - Correlation ID
  - Host name
  - Service name and service instance
  - Message
 - Traceable distributed Correlation ID

Observable
- The difference between Centralized Logging and Centralized Monitoring is
 - Monitoring is just the numbers and counts
 - Logging stores the detailed information behind those numbers
 - Monitoring tells that something happened and Logging tells what/why/how that happened.

Summary
- High Cohesion
 - Single thing done well
 - Single focus
 - Approach
  - Keep splitting service until it has only one reason to change
- Autonomous
 - Independently changeable and deploy able
 - Approach
  - Loosely coupled system
  - Versioning strategy
  - Microservice ownership by team
- Business Domain Centric
 - Represent business functions or represent a business domain
 - Approach
  - Course grain business domains
  - Subgroup into functions (CRUD) and areas (get invoice)
- Resilience
 - Embrace failure (design for it)
 - Default or degrade functionality
 - Approach
  - Design for known failures
  - Fail fast and recover fast
- Observable
 - See system health
 - Centralized logging and monitoring
 - Approach
  - Tools for real-time centralized monitoring
  - Tools for centralized structured logging
- Automation
 - Tools for testing and feedback
 - Tools for deployment
 - Approach
  - Continuous Integration Tools
  - Continuous Deployment Tools

## Technology for Microservices

### Synchronous Communication

Drawback of Synchronous Communication is that 
- both parties have to be available, even if one of the service is down, the whole transaction will not be fulfilled.
- Performance is subject to network quality. Service may not be slow, it could be the network that is slow.

### Asynchronous communication
- mitigated the need of client and service availability, there by decoupling them.
- More complicated to implement
- System relies on other platform (Message Q's), this creates another point of failure in the system
- Queuing issues lead to difficulty in debugging

## Moving Forward with Microservices

### Brownfield Micro service : Approach
- Existing system
 - Monolithic system
 - Organically grown
 - Seems too large to split
- Lacks microservices design principles
- Identify seams (the borders in your app along which you want to divide the domains)
 - These seams are the future microservice boundaries
 - Separation that reflects domains
 - Identify bounded contexts
 - These are not going to be clear and there will be lot of spill over. We need to identify the spills and decide where they needs to be added.
- Start modularising the bounded contexts
 - Move code incrementally
 - Tidy up a section per release
  - This is to ensure existing functionality remains intact and usable
 - Write unit and integration tests to verify and validate the changes
 - keep reviewing and correcting

### Brownfield Microservices : Migration
- Convert bounded contexts into microservices
 - Start off with one (to get comfortable)
 - Make it switchable (maintain two versions of the code to switch in case of issues)
- How to prioritize ?
 - By risk (starting with the least risk)
 - By Technology
 - By dependencies
- Incremental Approach
- Integrating with monolithic
 - Monitor both for impact
 - Monitor operations that talk to microservices (to see if the over the network microservice communication is degrading the performance of the monolithic)
 - Review and improve incrementally
 - Incrementally the monolithic will be converted

### Brownfield MS : Database Migration
- Split database across seams
- To be compatable with existing monolithic, create a data link that connects to multiple databases
- For tables that link across seams, we either need to redesign them or make use of a API call to fetch that data.
- TODO: How to ensure data integrity between the services?

### Brownfield MS : Transactions
- Transactions are very easy in monolithic, not the same in microservice

### Greenfield Microservices
- Evolving requirements, so not clear understanding of the domains
- System boundaries will evolve
- Start off with a monolithic design and let the seams evolve into clearer boundaries
 - Modules becomes services

### Microservices Provisos

TODO: "Provisos", what is this?




# Microservices: Getting Started

## Understanding the Benefit of Microservices

### Human Advantages of Microservices

Monolithic demands a huge domain knowledge as you need to have the ability to make changes across all the modules of the application to implement a feature as they are all tightly coupled together. It is a high learning curve.

It talks about the elimination of bottlenecks due to narrowed scope of responsibility and hence has low risk, this need not be true, as we give the ownership of a microservice to a team of two and hence the whole microservice could be potentially impacted. net-net, a debatable concept.

Daniel Pick's 3 elements that are essential to creation motivation
- Autonomy
- Purpose
- Mastery

Microservice helps address Autonomy and Mastery. That's 67% of motivation out of the box.

Prefer a microservice per team, not teams per microservice. Subtle but critical.
TODO: How to keep the team occupied if they are dedicated for a service and the service has no work in this PI?

### Resilience Benefits of Microservices

Module Boundaries can blue over time. Enforcing isolated codebases reinforce boundaries.

## The Challenges of Microservices over Monoliths

### Microservices Are Hard

Cost of Microservice is "Complexity"

### Resilience of Network Communications

IF not handled properly, the resilience challenges can lead to cascading failure, amplification effects and causing whole app downtime and outages all because a single microservice is down there by defeating the whole purpose of microservice.

### Running Microservices in Production

Microservices emphasis strongly on automation as a single manual step in monolithic get amplified to Xtimes (once per each microservice). So the need for automation is more in the microservice.

### Challenges of Independent Governance

TODO: What is the role of a software architect in microservices?

The emphasis in MS architecture is in ensuring the right balance between autonomy and consistency for the teams. They should be autonomous enough to do their task, but not so much that we lose the consistence across the applications. It's a fine balance.

Pulling data reports in monolithic is easy as it's just a single data store. Due to multiple data stores in MS, we have to redo the same across different data stores. 

TODO: How do you do a join query?

Beware of "Distributed Monolith", it has the complexity of microservices and the inflexibility of a monolithic. The worst of both world

Need for a join across multiple data stores may mean that the boundaries of microservices are poorly choose.

### Reviewing Challenges of Microservices

Fancy word - "Module extraction is perilous"

## Synchronous Communication Architectures

### Introduction to RPC

RPC = Remote Procedure Call
- HTTP Traffic, SQL Queries and even SOAP is also a RPC.
- RPC Properties
 - Arguments
 - has a Return Value
 - Executed Immediately
 - Client controls communication (its a pull)
 - Two hosts involved
 - Client waits for server (hence synchronous)
- Very easy to implement

### Service Discovery

Service Discovery
- Network address of service is located dynamically
- DNS of Internet is the same concept.
- This enables each service is still independent and need not change when the IP of another service changes.

Apache Zookeper is a kind of Service Discovery Tool
- Used internally in Hadoop, Kafka
- Has Massive scalability
- High performance
- But comes with the cost of complexity

Hashicorp Consul
- Provide Multi-cloud complexity
- Provided security

### Stateless and Stateful RPCs

Stateless RPCs
- Functional Procedures - Pure functions that only influence return value
 - Eg: Time server and invoice generator
- Conduits - serves as a facade to third party API

Stateful RPC
- More complex than stateless
- Most meaningful work is stateful (huh?)
- State is distributed (very important)
- Need to handle Unreliable network (eg: Payment transactions)

TODO: Network Partition
Where network thinks some task is completed but the task never made through the network and is left waiting for response from the client end. When you do a retry, the stateful system rejects the duplicate.

TODO: Idempotence
State should not be changed due to multiple calls.
In a chat service, the decision to show multiple notifications or not should be left to the notification service rather than the chat service. this enables us to reduce the risk of coupling.

Co-ordination of Stateful RPC's
- All-or-nothing behavior of storing data is impossible to achieve.
- Choose a failure mode and identify whose/which service state is more important to maintain.
- The co-ordination of state across multiple services is often called distributed transaction

### Performance of Synchronous Communications

Performance of Synchronous communication 

Consider two services A and B with availability of 99.9 and 99.5%

Communication between them will result in a net availability of AxB = 99.9 x 99.5 = 99.4% . If you notice, its less than the individual availability of each of these services.

Now, add another service C with availability of 99.5, this leads to a over all availability of 98.9% which is way less for an acceptable SLA.

This example indicates that the more services are chained together in a sync communication, the more it will lead to cascading failures.

Now, add Latency to this calculation and it becomes way more interesting.

Total latency = A + B + C

Synchronous communications are very sensitive towards Availability and latency tradeoff's

## Asynchronous Communication Architectures

### Introducing Asynchronous Communications

Asynchronous Communication
- Makes use of Message Bus Communication
- is a one way communication
- Is a fire and forget mode of communication
- In RPC, there are always 2 hosts, here there could be more than 2 due to the listener events
- The number of hosts could be dynamic without code changes
- The messages wait in a queue and may not be delivered immediately.

Good example of the difference between RPC and async using a chat application

Benefits of Asynchronous communication
- Faster, no need to wait for the response from all the services. just fire and forget
- Simpler redeploys; automatically handle brief downtime. As the messages are in queue, once the service is up, it will read from the queue and start processing.
 - This is the answer to the cascading failures in sync comm
- Absorb spikes in load, helps when the load is more. demand > capacity. This ensures the servies are not overloaded and get the service degraded.
- Isolate slow services (hide the flaws in design? :) ) or could be slow third party operations
- New services could be easily added without changes to other services as the new listener are dynamic.

### Message Bus Fundamentals

Anatomy of a message
- It is the smallest unit of data
- Payload
 - Business-relavent data
- Headers
 - Timestamp
 - Sender Info
 - Correlation Id
 - Error handling information
 - Custom headers

Messages needs to be small
- Large messages harm throughput
- large messages Opportunity for failure
- Size limits needs to be enforced
- Offload to blob storage if need to send large message

Messages are published to a topic in a message bus
- Topic defines semantics
 - eg: post liked, post created, chat message sent

Messages published to a topic are routed to a queue. A listener (client) reads from the queue.
A consumer can subscribe to multiple topics
- All of them could be routed to the same queue
- or they could be routed to individual queues

Message Queue
- Temporary storage location
- Acknowledge to clear (after processing the message)
 - Supports Crash recovery

### Designing Asynchronous Communication Flows

The problem in sync, where we are trying to dea with the failures in notification and chat service can be avoided by having them read to different channels, so that once post is successful then it will post a message to notification and then notification service will decide if this notification needs to be displayed or if it is a duplicate. Even in case of any failures in the chat service, the message is still stored in dead letter queue and can be salvaged later point of time.

Event Driven Architecture - A system that is driven by the production, detection, consumption and reaction to events.

### Distributed Transactions

Distributed Transaction - A set of coordinated state changes spanning across a number of different independent services.

Example is a 
Create Post needs to be processed to
- Sent to timeline
- Facial Recognition
- Photo screening for inappropriate content
- Advertising profile
- Notification service
- Abuse screening
- Payment processing (in case of a promoted tweet/post)

All these events needs to be coordinated together and hence called a distributed transaction

This can be achieved with chaining of events, but it has many pitfalls and is unrealistic

Pitfalls of Event chaining for Distributed Transactions
- Hard to design for consistency at intermediate steps.
 - It gets exponentially more complex to implement as the number of events in the design keep growing. Its not realistic
- Difficult to change routing without unintended consequences. 
 - What is not all of them are relevant? like payment processing or facial recognition?
- Hard to track the pending transactions as there will be many at each stage
 - How do you even cancel a post in such architecture.


Primary Concerns of Distributed Transactions
- Message Routing
 - Can we make a single service responsible for all the routing rather than multiple small stake holders?
 - This can also help us address the conditional routing issues like that of promoted tweets
- Transaction state
 - Helps in centralizing the state and track progress
 - Also solves the cancel problem
 - Can also add resiliency to handle timeouts or invalid processing
- Failure compensation
 - Helps with resolving scattered chained messaging (one transaction fails and the whole message is stuck)
 - You get  failure in step#4, then we can automatically perform rollback or compensating operations at step 1,2,3
 - Helps in achieving all or nothing model (but still not 100% as the rollback can fail too)

### Saga Pattern and Routing Slip Pattern

Saga Pattern
- Allows for distributed transactions
- Originally proposed for LLT's (Long Lived Transactions) that can take hours or days
 - Was originally for same database
 - For compensating operations (rollback)
- Later extended to distributed systems
 - To centralize transaction state
 - For Message Routing
 - Failure compensation

Saga Pattern
- This makes use of a centralized service that takes care of all state changes.
 - Each message will consist of a correlation id, which will be maintained consistent across all the states
 - Sends msg for step-1 and updates the DB with step-1-pending-status against the current correlation id
 - When it receives the response from step-1, then it updates the status of the message and then sends the message to step-2
 - this process continue for all the steps
 - Once the final step is completed, it will delete all the records against the correlation id, implying that the final state is reached.
- This pattern is driven by receiving messages. 
- When they receive a message, they transition state and as part of the state transition, they often publish other messages which will lead to further state transition (when we get the reply)
- Since this is all controlled centralized, we can control the sequence of state changes without any major code changes (we just need to change the sequence in the saga and the whole flow would be updated accordingly.)
 - This helps eliminate the complexity which we would have with a message chaining architecture.
- If there is a failure, then we state running the compensating events for the previous steps, to revert the distributed transaction.
 - This ensures we move the system to a state, where the transaction never happened (remember, all-or-nothing)
 - This is called failure compensation
- This patterns also helps us to handle cancellation without too much complexity
- Timeouts can be scheduled too
- Additionally helps us to handle Parallel processing. But this will increase the complexity exponentially.

Routing Slip Pattern
- Inspired from manufacturing
- Set of instructions attached to a work item
 - Which stations to do
 - What kind of work per station
 - Which order
- At each station
 - They do the work
 - Pass the item to the next station.
- Rather than depend on a centralized transaction service, this believes in de-centralized (or empowering the individual units ) to hand off the work to the next appropriate state.
 - Just like a double linked list. Every node knows what it needs to do and who is its prev and next states.
- At each step, we can add additional information to the message before we handoff to next step. Hence this pattern servers excellently as message routing.

So, 
1> We determine the processing steps in advance
2> Attach them to the message
3> The message gets routed in that sequence

- This do not have a centralized transaction state, so we do not know where the message is in the big pile. so cancel could be difficult.
- Once the initial slip is compiled, it will not be possible to change the sequence. Saga pattern is more dynamic in that case.


TODO: Save this slide, good differences,

### Review: Asynchronous Communications

Asynchronous communications architectures are complex.

They have many benefits (below), but are undoubted complex to maintain. so measure the costs carefully before jumping into.
 - Resiliency
 - Decoupled
 - Load Smoothing
 - Flexibility

Event Driven architecture tend to exhibit "Eventual Consistency", where there are temporary inconsistent states.

## Developing Features in a Microservices Architecture

### Controlling the Quality of a Microservice

In a micro service, if we consider HTTP as the API and the dependencies as Database, message bus and other third party libs. These can be interchanged as the inputs and outputs for a logic bus. i.e. make use of the API's to control the behavior of the dependencies. When we are able to do that, then we will be able to automate the test of each and every part of our service/code and write unit tests accordingly.

System Boundary - A place in the code where control is passes to or from an external system

TODO: Take snippets from the slide here for the test pyramid. Good representation

### Continuous Delivery Techniques

To add new features while the fear of breaking, we can use either
- API Versioning
 - This would be needing more code changes as we need to maintain both code bases. 
 - Used more for non-backward compatible or major changes to the logic.
 - Onus is on the developer to ensure bugs are fixed and propagated across all the versions 
- Differentiated either by the version number in the url or in the messages.
- This defers the decision making to other services (like our svcorder v2/v3)
- Feature toggles
 - Also called feature flags, where we control the old vs. new flow based on DB driven conditional operators.
 - This enables us to only turn this on for specific users or user groups.
 - This is more dynamic compared to API versioning
 - more tuned for experimental features
- Bottom line is, avoid getting stuck waiting for the acceptance from all user groups before we deploy the services. 
- These techniques, help us to avoid the synchronous deployments of services (that breaks the independence of the services)

### Microservice Team Culture

TODO: Nice slide

## Managing Microservices Architecture

### Characterizing Design Improvement

Good Read: Martin Fowler, "Design Stamina Hypothesis" - continuing to modify code without continuously improving design causes the cumulative functionality over time to look flat, where over time, more and more effort is required to deliver the same sized increment of functionality.

Focus more on Intent-Revealing Design
- Emphasizes "why" over "what"
- Hides away the irrelevant design

Good design ensures malleability (TODO)

### Prioritizing Architectural Improvements

TODO: "Winning is the worst ting that can happen in Vegas" - David Heinemeier Hansson

TODO: "Winning is the worst thing that can happen in Vegas" - David Heinemeier Hansson

Pursuit for "flexibility" is often the reason for software becoming inflexible.

TODO: Good slide. First Derivative - Gregor Hohpe

### Influencing Architecture

Prioritizing Architecture
- WE often add speculative functionality under the same of "flexibility"
 - This leads way to maintenance tax and slows down the future delivery
- Pursuit of "flexibility" is often the reason for software becoming inflexible
- The question to ponder is, "How do we invest in architectural improvements and avoid speculative "flexibility"?
- Architect lives in "First Derivative" - read more

## Building Resilient Microservices Architectures

### Resilience Starts with Quality

Software Quality
- External (what can be observed by an user)
 - Visible to end-users
 - Correctness
 - User Experience
 - Reliability
 - Absence of errors
 - Performance
- Internal (the state of the software design or architecture; ease to make changes in application)
 - Visible to programmers
 - Maintainability
 - Testability
 - Observability
 - Simplicity

- Resilience starts with Quality
- Attention to both internal and external quality is the foundation for resilience.
- Understanding the systems limits is essential for managing resilience

### Observable Microservices

Observability - The quality of a system that allows you to understand its internal behavior by inspecting its outputs.

## Evaluating Microservices Readiness

### Microservices Are a Poor Goal

Microservices are a means to an end. They are the solution for certain kind of problems and are not (and should not be) the goal in themselves 

### Problems Suited to Microservices

Key metrics for evaluating software development Capabilities
- Deployment frequency - how frequently can we deploy changes
 - On demand
 - Once a week to a month
 - More than a month
- Lead time for changes - time it takes to build, test and deploy a new increment or functionality
- Mean time to recovery (MTTR) - time taken to restore a service
- Change failure rate - % of changes delivered to production that cause service degradation or require immediate remediation

Microservices are well suited for challenges of 
- Scale 
- Complexity
- Governance