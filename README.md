# System Design

## Basic Steps

1) **Clarify and agree on the scope of the system**
* **User cases** (description of sequences of events that, taken together, lead to a system doing something useful)
	* Who is going to use it?
	* How are they going to use it?
* **Constraints** 
	* Mainly identify **traffic and data handling** constraints at scale.
	* Scale of the system such as requests per second, requests types, data written per second, data read per second)
	* Special system requirements such as multi-threading, read or write oriented.
	
2) **High level architecture design (Abstract design)**
* Sketch the important components and connections between them, but don't go into some details.
	* Application service layer (serves the requests)
	* List different services required.
   	* Data Storage layer
   	* eg. Usually a scalable system includes webserver (load balancer), service (service partition), database (master/slave database cluster) and caching systems.

3) **Component Design**
* Component + specific **APIs** required for each of them.
* **Object oriented design** for functionalities.
	* Map features to modules: One scenario for one module.
	* Consider the relationships among modules: 
		* Certain functions must have unique instance (Singletons)
		* Core object can be made up of many other objects (composition).
		* One object is another object (inheritance)
* **Database schema design.**

4) **Understanding Bottlenecks**
* Perhaps your system needs a load balancer and many machines behind it to handle the user requests. * Or maybe the data is so huge that you need to distribute your database on multiple machines. What are some of the downsides that occur from doing that? 
* Is the database too slow and does it need some in-memory caching?	

5) **Scaling** your abstract design
* **Vertical scaling**
	* You scale by adding more power (CPU, RAM) to your existing machine.
* **Horizontal scaling**
	* You scale by adding more machines into your pool of resources. 
* **Caching**
	* Load balancing helps you scale horizontally across an ever-increasing number of servers, but caching will enable you to make vastly better use of the resources you already have, as well as making otherwise unattainable product requirements feasible. 
	* **Application caching** requires explicit integration in the application code itself. Usually it will check if a value is in the cache; if not, retrieve the value from the database.
	* **Database caching** tends to be "free". When you flip your database on, you're going to get some level of default configuration which will provide some degree of caching and performance. Those initial settings will be optimized for a generic usecase, and by tweaking them to your system's access patterns you can generally squeeze a great deal of performance improvement.
	* **In-memory caches** are most potent in terms of raw performance. This is because they store their entire set of data in memory and accesses to RAM are orders of magnitude faster than those to disk. eg. Memcached or Redis.
	* eg. Precalculating results (e.g. the number of visits from each referring domain for the previous day), 
	* eg. Pre-generating expensive indexes (e.g. suggested stories based on a user's click history)
	* eg. Storing copies of frequently accessed data in a faster backend (e.g. Memcache instead of PostgreSQL.
* **Load balancing**
	* Public servers of a scalable web service are hidden behind a load balancer.  This load balancer evenly distributes load (requests from your users) onto your group/cluster of  application servers.
	* Types: Smart client (hard to get it perfect), Hardware load balancers ($$$ but reliable), Software load balancers (hybrid - works for most systems)

<p align="center">
  <img src="http://lethain.com/static/blog/intro_arch/load_balance.png" alt="Load Balancing"/>
</p>

* **Database replication**
	* Database replication is the frequent electronic copying data from a database in one computer or server to a database in another so that all users share the same level of information. The result is a distributed database in which users can access data relevant to their tasks without interfering with the work of others. The implementation of database replication for the purpose of eliminating data ambiguity or inconsistency among users is known as normalization.
* **Database partitioning**
	* Partitioning of relational data usually refers to decomposing your tables either row-wise (horizontally) or column-wise (vertically).
* **Map-Reduce**
	* For sufficiently small systems you can often get away with adhoc queries on a SQL database, but that approach may not scale up trivially once the quantity of data stored or write-load requires sharding your database, and will usually require dedicated slaves for the purpose of performing these queries (at which point, maybe you'd rather use a system designed for analyzing large quantities of data, rather than fighting your database). 
	* Adding a map-reduce layer makes it possible to perform data and/or processing intensive operations in a reasonable amount of time. You might use it for calculating suggested users in a social graph, or for generating analytics reports. eg. Hadoop, and maybe Hive or HBase.
* **Platform Layer (Services)**
	* Separating the platform and web application allow you to scale the pieces independently. If you add a new API, you can add platform servers without adding unnecessary capacity for your web application tier.
	* Adding a platform layer can be a way to reuse your infrastructure for multiple products or interfaces (a web application, an API, an iPhone app, etc) without writing too much redundant boilerplate code for dealing with caches, databases, etc.

<p align="center">
  <img src="http://lethain.com/static/blog/intro_arch/platform_layer.png" alt="Platform Layer"/>
</p>
	
## Key topics for designing a system

1) **Concurrency** 
* Do you understand threads, deadlock, and starvation? Do you know how to parallelize algorithms? Do you understand consistency and coherence?

2) **Networking**
* Do you roughly understand IPC and TCP/IP? Do you know the difference between throughput and latency, and when each is the relevant factor?

3) **Abstraction**
* You should understand the systems you’re building upon. Do you know roughly how an OS, file system, and database work? Do you know about the various levels of caching in a modern OS?

4) **Real-World Performance**
* You should be familiar with the speed of everything your computer can do, including the relative performance of RAM, disk, SSD and your network.

5) **Estimation**
* Estimation, especially in the form of a back-of-the-envelope calculation, is important because it helps you narrow down the list of possible solutions to only the ones that are feasible. Then you have only a few prototypes or micro-benchmarks to write.	

6) **Availability & Reliability**
*  Are you thinking about how things can fail, especially in a distributed environment? Do know how to design a system to cope with network failures? Do you understand durability?


## Web App System design considerations:
* Security (CORS)
* Using CDN
	* A content delivery network (CDN) is a system of distributed servers (network) that deliver webpages and other Web content to a user based on the geographic locations of the user, the origin of the webpage and a content delivery server.
	* This service is effective in speeding the delivery of content of websites with high traffic and websites that have global reach. The closer the CDN server is to the user geographically, the faster the content will be delivered to the user. 
	* CDNs also provide protection from large surges in traffic.
* Full Text Search
	* Using Sphinx/Lucene/Solr - which achieve fast search responses because, instead of searching the text directly, it searches an index instead.
* Offline support/Progressive enhancement
	* Service Workers
* Web Workers
* Server Side rendering
* Asynchronous loading of assets (Lazy load items)
* Minimizing network requests (Http2 + bundling/sprites etc)
* Developer productivity/Tooling
* Accessibility
* Internationalization
* Responsive design
* Browser compatibility

## Working Components of Front-end Architecture
* Code
  * HTML5/WAI-ARIA
  * CSS/Sass Code standards and organization
  * Object-Oriented approach (how do objects break down and get put together)
  * JS frameworks/organization/performance optimization techniques
  * Asset Delivery - Front-end Ops
* Documentation
  * Onboarding Docs
  * Styleguide/Pattern Library
  * Architecture Diagrams (code flow, tool chain)
* Testing
  * Performance Testing
  * Visual Regression
  * Unit Testing
  * End-to-End Testing
* Process
  * Git Workflow
  * Dependency Management (npm, Bundler, Bower)
  * Build Systems (Grunt/Gulp)
  * Deploy Process
  * Continuous Integration (Travis CI, Jenkins)

# Steps to build a better System Design

## Step One: Framing The Problem a.k.a get the MVP
- [ ] Identify the use cases that are in scope
- [ ] Determine constraints based on scoped use cases

`use case` : the things your system needs to be do.

`constraints` : the things your system will have to consider to be able to do stuff

#### Use Cases
1. Break down features into simple tasks to get your use cases.
*E.g: url shortening can be broken down into redirection and shortening*

2. Find some extra use cases to determine what's out of scope.
*E.g: high availability, analytics, custom urls*

Notes: This is a clarifying step. Get as clear as you can on your scope.

#### Constraints
- [ ] Determine what your system needs to handle in terms of traffic and data to clarify direction for abstract design

*First solve for units per month, then per second*

`traffic` : Requests to be handled

`data` : Information you need to store

1. Get some idea of how popular your system might be/can be assumed to be and guess at the ballpark number for overall traffic. Use the Scale numbers below to help.

2. Figure out the % of traffic that goes to each use case. Is it write-heavy or read-heavy? Chances are it will be read-heavy.
 *E.g: 10% of traffic will be shortening, 90% will be redirection.*

3. Calculate the numbers. Try to get rough quantities for each use case per month and per second.
*E.g: 100+ mil requests per month ->  400+ requests per second.*

4. More calculating, now for data. Use your percentages for the ballpark, use knowledge of data format to calculate storage space.
*E.g: if 10% of requests are for shortening and we get 400 requests per second, that means 40 per second are new urls. If urls are made of chars, which are each 1 byte... so on so forth.*


##### Helpful Numbers To Know:
For Scale:
- No. of Facebook users: ~1.3bil
- No. of Twitter users: ~650mil
- No. of tweets per day: 500mil

For Calculations:
- 1 byte -> 8 bits
- `char` -> 1 byte
- `int` -> 4 bytes
- 1kb (kilobyte) -> 1000 bytes
- 1mb (megabyte) -> 1mil bytes
- 1gb (gigabyte) -> 10bil bytes
- 1tb (terabyte) -> 1000bil bytes or 1k bil bytes

## Step Two: Abstract Design
- [ ] Identify the components needed to address all use cases
- [ ] Establish relationships between components
- [ ] (Optional) - Narrow down choices for technologies. Better to leave this for later, as it can lead you into a more granular rabbit hole than is helpful.

You will have two main layers:

`Application/Service Layer` - this is the layer serves up requests

`Data Storage/Persistence Layer` - handles all yo' data

1. Start with one use case and figure out what services it needs to be able to work.  
2. Elaborate on how each service will work, and how it will interact with the data layer.
3. Take this opportunity to clarify how the data layer should behave.
4. Repeat with other use cases.

NOTE: Optional is presentation layer. Not a lot of people will ask for this.

## Step Three(A): Review (and Choose Tech)
- [ ] Review current design and get feedback if possible or direction on where to go next

Go over what you have and clean up the drawings. Now depending on your interviewer, you can either fix what you have, work on more features, or pick a rudimentary tech stack.
You might need to flesh out more specifically how the relationships work, what database you are using, etc.

## Step Three(B): Bottlenecks
- [ ] Identify potential scaling issues

Going back to `traffic` and `data`. Are you read-heavy or write-heavy? Are you bound by the requests(IO-bound) or by processing data (CPU-bound)?
What are the bottlenecks and single points of failure? I.e if something goes down, what is going to cause the most downtime?

If you've picked tech, what are their disadvantages?

**NOTE: Add section on some useful tech and the problems they're good for here**

## Step Four: Scaling
- [ ] Prove that you can scale your design

##### Basic Concepts and the Problems They Solve:
- Redundancy: Having copies of data so you can back shit up. This solves the single point of failure and data loss.
- Distribution: Have more things doing your stuff concurrently. This solves a high amount of load and also availability/downtime problems.
- Caching: Storing stuff so you don't have to go so far to get it again. This solves for speed and high load.


** NOTE: Everything after this is just a draft. Will be cleaned up, but not as organized as it could be.**

#### Application Service Layer (DRAFT)
- Always have a load balancer, or multiple load balancers
  - Load balancers distribute requests among several servers
  - For persistent sessions, load balancers can stick some kind of encrypted server ID in the cookie and parse it on each request to determine if user should be sent to a particular server
  - If you have two load balancers (should be enough), there are two architectures for high availability:
    - `Active - Active`: both LBs are active, share load and send heartbeats to each other. When one goes down, the other notices and assumes full responsibility for requests.
    - `Active - Passive`: one active LB and one passive LB. Active LB sends heartbeats to passive one. When it goes down, the other notices and promotes itself to active.
- Services: General rule of thumb is to split out services according to read/write.
  - Why? Writes take significantly longer than reads and you can't cache writes.
- Reduce how much you touch the data storage layer if at all possible. Use caching.
  - Caches can get full. Get around this by expiring objects (like sessions) or using an LRU cache.
  - You can cache queries, values, even whole files (though you probably don't want to do that.)
  - There are different types of caches.
- A Note on Partitioning: You can partition according to many things: Users names (not that helpful), but most (probably) useful at the macro scale is geographically. Think data centers in different regions.
- Keep in mind how you share state among servers.

#### Data Persistence Layer (DRAFT)
- Rule One: Do not shard unless you absolutely have to.
- Add an index to be able to look things up better in the table.
  - *An index is a data structure*. A data structure. Built from database columns. It lets you look things up faster instead of going through everything. Often a B-Tree.
  - Why don't we just index everything, wouldn't that be better? NOPE. That makes updating the database SUPER expensive, cause then we have to update the index too.
  - So index *smart*.
- Replication - multiple databases. A way of automatic copying of data. **REDUNDANCY!**
  - `Master - Slave`: Master DB is the only one that can be written to. Writes trickle down to slaves DBs, which are read from. This allows reads to be distributed. Note it's still a single point of failure.
    - However, if Master goes down, we can promote a Slave to Master until we fix it. High availability?
  - `Master - Master`: Two masters that can be written to, and they both have Slaves. Some synchronicity problems, but more writes.
  - Partitioning: Different databases for different things?
    - Partition smart. If we do it alphabetically for users, how many users have names starting with 'X'?
    - Some effective segmentation strategies: Domain (Harvard students vs MIT students like Facebook did for the early days), Denormalized Data (User profiles, etc)
    - Some downsides: Locality. It takes longer to go between databases, and what if you need info from a bunch of places? How might we be able to offset this?


### Scalability Conclusion:
- The same problems present themselves over and over again.
- High load, single point of failure, availability, synchronization of data/info
- The same techniques apply in many different ways.

**Links**

[System-Design-vasanthk](https://gist.github.com/vasanthk/485d1c25737e8e72759f)

[System_Design-SunnyG](https://gist.github.com/sunny-g/2acdd69e71a513f5d9cda061bf0a03d1)

[System Design Interviewing](http://www.hiredintech.com/system-design/)

[Scalability for Dummies](http://www.lecloud.net/tagged/scalability)

[Introduction to Architecting Systems for Scale](http://lethain.com/introduction-to-architecting-systems-for-scale/)

[Scalable System Design Patterns](http://horicky.blogspot.com/2010/10/scalable-system-design-patterns.html)

[Scalable Web Architecture and Distributed Systems](http://www.aosabook.org/en/distsys.html)

[What is the best way to design a web site to be highly scalable?](http://programmers.stackexchange.com/a/108679/62739)

[How web works?](https://github.com/vasanthk/how-web-works)
