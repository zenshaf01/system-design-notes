# system-design-notes
A repo for system design notes

# Concerns
System design is mainly concerned about the 3 key things that a system must efficiently perform
- Store Data
- Move Data
- Transform data / Or perform some logic on data

# What is good system design
A system design is good if it caters to the following
- High availability
- Fault tolerance
- Reliability
- Redundancy
- Scalability
- Throughput

# Networking
- Computers communicate through IP
- Each computer has an IP address

# API Transfer 
- JSON: Very human readable but slower to parse as it is text 

# API Paradigms 
- REST:
    - GET, POST, PUT, DELETE -> Return HTTP Status code with content. 
    - Is stateless. You can use pagination to address statelessness through including information in query parameters.
    - Built on top of HTTP
    - Good for public API's. More developer friendly.
    - Use when
- GraphQL:
    - You can specify what fields you want from the server for a particular resource. And only those fields will be returned.
    - Addresses REST's over fetching issue 
    - Built on top of HTTP
    - Only Uses POST
    - Query is included in the POST request
    - Is more flexible than REST
    - Has pagination
    - Has 2 operations:
        - Query
        - Mutation
    - GraphQL queries cannot be cached since it uses HTTP POST.
- gRPC
    - Built on top of HTTP2 which is bidirectional and makes web sockets obselete.
    - There is not native support to use gRPC from the browser. YOu need to go through a proxy for that.
    - Ideal for server to server communications. Like microservices communicating with each other.
    - It is faster and more efficient than REST.
    - It is faster because it sends information in protocol buffers instead of JSON. Proto buffs are schema objects that are serialized into binary format instead of JSON. Binary is always faster than JSON.
    - Can also handle Streaming
    - gRPC is bidirectional
    - Less standardised.
    - Less developer friendly
    - No status codes

# API Design:
Api design is concerned with how the API will be interacted with. What actions will the API allow. What does the API allow on its service, Does it allow to create things ? etc.
- What are the api endpoints
- What do the endpoints accept
- Api's should be backwards compatible
- Api's should be versioned so that api's are backward compatible
- POST: https://api.twitter.com/v1.0/tweet
- GET:  https://api.twitter.com/v1.0/tweet/:id
- GET ALL FOR USER: https://api.twitter.com/v1.0/users/:id/tweets?limit=10&offset=0 -> Has pagination

# Caching:
- Cache reads are faster than RAM and disk
- But Cache's are limited in space
- Cache's live between the application and the main data source (Persistent DB)
- Caching involves copying data from memory to Cache for future use since its faster. 
- Increases throughput
- Decreases networking cost
- In terms of systyem design and local cache may mean that a server has a local copy of the data on the local disk or in memory so that the server does not need to make a request over 
the network, which would be slower.
- Cache hit is when a data needed by the server is present in the cache
- Cache miss is when the data needed by the server is not present in the cache
- Cache ratio = hit / hit + miss
- Key value in memory stores like Redis is used for caching 
- Cache invalidation is to be acheived to mnake sure consistency and accuracy.
    - Purge: removes cached content for a specific object.
    - Refresh: New content is fetched even if present in cache and then cache is updated with new content.
    - TTL: A time to live is defined for the content, at expiration of ttl, the conent is refreshed.
    - Stale while revalidate: Stale content is served on request and the content is refreshed immidiately
- Caching write algorithms:
    - Write around cache: Data is first written to disk / db and then the second request for the data is read first from the db (cache miss), then populate the cache with teh data and then is returned.
    - Write through cache: Data is written to the cache and the disk. Less cache misses. But more unneccessary data getting in to the cache.
    - Write back cache: Data is written to cache only. Cache data is then periodically written to disk in batches. It is faster, but data loss can occur. Use it for data that is fine to be lost sometimes.
- Caching read algorithms:
    - Read through cache: If a cache miss occurs its the cache's responsibility to fetch the data from the origin data source and store it in the cache. 
        - simplified application logic code coes the application is not responsible for coordinating.
    Read aside cache: If a cache miss occurs its teh applicatons responsibility to fetchg data from the data source.
- Eviction policy:
    - Cache size is limited so an eviction policy is to be implemented.
    - An eviction policy's sole purpose is to maximize the cache hit ratio.
        - Random: Randomly select data to be evicted. Not very useful.
        - FIFO: Data added to the cache first is evicted first. Not very smart either.
        - LIFO: Data added to cache last will be evicted first.
        - LRU (Least recently used): The least recently used data will be evicted first.
        - LFU (Least frequently used): The least frequently used data will be evicted first.

# CDN's (Content delivery networks)
- Caching data close to the user
- Requires an origin server and multiple cdn servers.
- Origin server is the server that has the primary data which is then copied to cdn server.
- Only static content is copied to cdn's. You cannot have dynamic content on cdn's.
- Pictures, videos and static code files are a good candidate for cdn storage.
- CDN's decreases the latency
- Increases the availability of the origin server
- HTTP Header cache control having a public value means the CDN can cache the static file. This header is set by the origin server
- Types:
    - Push CDN: Origin server pushes the uploaded static content to the cdn servers.
    - Pull CDN: Request will hit the closes CDN, if a cache miss, the CDN will act as proxy and request the content from the origin server. Once origin server returns the content to CDN as a response,
    The CDn will cache the response content and then respond the original request from the client with the content. 

# Proxies and Load Balancing:
- Proxies:
    - An intermidiate server taht sits between the client and the server
    - Forward Proxy:
        - A proxy is a middle server that in forward proxying forwards requests to the primary application server. Client is hidden from the server.
        - Proxy servers can bypass geo restrictions
        - Proxy's can also block specific traffic.
        - VPN's are forward proxy
        - Anonymises user
        - Can cache data
        - Can apply request transformation
        - Compress / decompress an resource
    - Reverse Proxy:
        - A reverse proxy forwards client request to the application server while not hiding the client but hiding the application server.
        - Reverse proxy's hide the destination server.
        - Client does not know the existence of the application server. The applicatiion serve does know about teh client.
        - CDN's are a reverse proxy.
        - Load Balancer is a reverse proxy.
        - Anonymises server
        - Protects servers from DDOS
- Load Balancer (Nginx):
    - It is a type of reverse proxy. 
    - A load balancer receives request from the client and then forwards the request to a fleet of applications server while balancing the load on each of those servers.
    - Load balancers are needed when a single server is not enough to server all users and we have a fleet of horizontrally scaled servers which need to get a balanced level of traffic.
    - You can add load balancers between client - server, server - server, server - database.
    - Load balancers should only forward traffic to healthy servers. Server health can be established using health checks.
    - Algorithm's:
        - Round robin: This strtegy involves goind round robin and send requests to each of the servers one after the other. Each server gets an even amount of traffic.
        - Weighted Round robin: A weight is given to a server based on its power, The load balancer than performs the round robin forwarding based on those weights.
        - Least connections: Requests are routed to the server which has the least number of current connections.
        - Least Response time: requests are forwarded to servers which have the least average response time.
        - User location: If we have servers in different geo locations we can route user requests based on the users locations and route request to server which is closer to the user.
        - IP Hashing: 
    - Types:
        - Layer 4: TCP layer load balancer. Faster. Location based balancing. Cna access IP. But cannot access application layer data. Application data is not unpacked.
        - Layer 7: Application layer load balancer. Can access application layer data, slower but more flexible, can see auth, url paths.
    - What happens if the load balancer itself goes down?:
        - Have multiple replicas of load balancer
        - Have a backup load balancer whichj is passive
        - Each load balancer should check each others health.

# Consistent hashing
    - Hashing can be used for load balancing
    - A hash of IP address is modded (Modulo operator %) with the number of servers we have. The value we get will then be used to forward request to the server with that value.
    - Hashed load balancing allows the load balancer to send the request from the same user to the same server since the IP address will be the same.
    - Hashed balancing is beneficial if servers are performing some type of caching. So a server caching data for user 1 will perform better.
    - It is used when we want users to be consistently mapped to specific servers.
    - The problem with this is that if the number of servers change the hashing result will change and the user to server mapping will also change, negating the benefits of caching at the server level.

# Storage
- RDMS / SQL:
    - Data is persistent
    - Stored on disk
    - Uses B+ Tree. B+ tree is like a binary tree but instead of being binary (a node having 2 children) it has n children.
    - Data is kept in tables which satisfy's a schema
    - Schema design is important
    - Each table can have a relationship with another table using foriegn key constraints
    - Allows data and referential integrity
    - You can scale RDMS vertically.
    - Mysql and Postgres do not have native sharding support
    - ACID:
        - Atomicity: 
            - Every transaction is all or nothing.
        - Consistency:
            - Data consistency.
            - Data will adhere to the defined contraints
        - Isolation:
            - Concurrent transactions are isolated. 
            - Transactions will be serilialized, meaning theyy will be commited in a specific order to avid dirty reads.
            - Isolation 
        - Durability: 
            - Database is persitent since it is stored on disk. MYSQL is ACID compliant but REDis is not since it6 is not saved on disk.
            - Transactions: Every transaction commited is durable. it should be persisted.
- NoSQL / Non relational DB:
    - Hase BASE properties:
        - Eventual consistency: Multiple NoSQL db's of the same schema will follow a leader and follower configuration where the follower db has a copy of the data in teh leader db.
        Writes will take place on the leader db, and the leader db will eventually at some time later will write that to the follower db's. 
        - Stale data is still possible, but eventually the replica will have the updated value.
        - This allows higher read count.
        - You can break up the DB using sharding
    - Type:
        - Key value stores like redis and memcached
        - Document stores / dbs. The Document database would be a collection of JSON objects. Has flexible schema. MongoDB.
        - Wide Column DBs: Ideal for write heavy workloads. Cassandra
        - Graph DB: Relational NoSQL databases. Ideal to represent and store graph based relations like social media sites.
    - Why NoSQL:
        - Allows eventual consistency
        - More scalable than RDMS. RDBMS's ACID properties limit its scalability. You can vertical scale a RDMS db but cannot do it horizontally.
        - You can split the data in multiple DB's since there is no referential issues in NoSQL Databases.
    - Has native support for sharding

# Replication and Sharding
- Replication:
    - Replication is the practice of maintaining more than one copy of the database.
    - Replication is beneficial for instances where a single database is unable to handle the read / write load.
    - Replication allows us to have high availbility. Since a leader db can go down, the follower can become the leader since it has the same data. eventual or immidiately consistent.
    - Types: 
        - Leader / Follower configuration: The leader db is the primary db. It is replicated and copied to create another follower db. The leader db gets the writes and then replicates that data to the followers. The follower is read only. Replication is a way to scale up the read.
        - Leader / Leader replication: Both db nodes are leaders and can get writes and reads. Each leader will replicate the data written to itself to the other leader. This strategy is ideal for if leaders have to serve separate parts of the world.
    - Replication strategies:
        - Async: The leader asynchronously (Eventually at some point) writes data to follower nodes. Reads before the eventual consistency might be stale / inconsistent.
        - Sync: The leader immidiately replicates data to the follower nodes. This introduces latency.
        - Semi-Sync: The leader immidiately replicates data to one replica (synchronously) and then the other replicas are updated asynchronously
- Sharding:
    - Sharding involves breaking up / splitting the db into different dbs. Meaning some rows from a table are present in shard1 and other rows in shard2.
    - Sharded dbs are put on different machines.
    - Sharding allows better throughput and query performance.
    - The data is split up using the shard key. Shard key can be different based on the sharding strategy.
    - Mysql and PostgreSQL do not have native sharding support.
    - Sharding method:
        - Range based sharding: The shard key is a range of values. Say primary key 1 - 100 would go in shard 1 and primary key 101 - 200 will go0 in shard 2.
        - Hash based sharding: consistent sharding
    - Problems:
        - Sharding get really complicated when you have to do joins.
    - Types:
        - Horizontal sharding: Each shard contains a subset of rows
        - Vertical Sharding: Each shard contains a subset of columns
    - Cons:
        - Joins are not feasible
        - referential integrity is not feasible

# Indexing
Database indexes are used to speed up database reads.
- Indexes are basically pointers to a specific row in db
- Create indexes for columns that you think will be used to read data from db.
- Indexes increase read performance but decrease write performance. Since the dindex needs to be update on write, update and delete.
- As indexes decrease write performance, having indexes on a write heavy db should be avoided.


# CAP Theorem
- In the presense of a network partition, a system must choose either consistency or availability
- The CAP theorem applies to distributed databases which have replicas.
- The theorem dictates that a database can satisfy any of the 2 out of the 3. A database can satisfy consistency and availbility or, can have partition tolerance and availbility but not all 3.
- Consistency: Data consistency between the leader and replicas. Every single read will get the latest write.
- Availability: If the distriubuted system gets disconnected, meaning leader is no longer able to replicate to the follower, user reads are routed to the leader. This is consistency being gained by sacrifcing availbility. Availbility is being sacrificed because we are disallowing the users to go to the partitiobned (disconnected) follower.
- Partition (Network partition) Tolerance: The ability of the system to keep functioning even if the distributed replicas / leaders stop talking to each other.
- PACELC:
    - When there is a partition, choose A or C.
    - If there isnt a partition, Favour latency or consistency.

# Object Storage:
- Object storage is flat storage structure unlike the File system which is hierarchical.
- AWS S3 is a block storage.
- Ideal for storing large files for long term.
- Metadata for these block storage files are kept in a db.

# Message Queue's:
- Message queue's are for async message processing
- FIFO
- Push and Pull strategies
- Server sends back an Ack when the message is processed
- If the queue does not receive an Ack from the service, it will not drop the message 
- Allows for service decoupling
- Pub / Sub: Publishers publish messages to subscriber services. This is a many (publishers) to many (subscribers) model. Topics are the message receivers for publichsed messages to queues and the topics will then feed multiple subscribers (services). each of the subsribers could be doing something different with teh message. This is also called a Fan out strategy.
- RabbitMQ
- Kafka

# Heartbeat
- Server's part of the distributed system send each other heartbeat requests telling everyone in the fleet that they are alive.
- A server not getting a heartbeat request from other service for a specified time can mean that the server has crashed so that the central server can work on a replacement.

# Checksum
- A hash of the data is stored with the data in the db, When the client fetches data, the checksum is sent to client with the data. Then the clien
can calculate its own checksum and compare it with the stored value of the checksum to determine if the data got corrupted or tampered with during transit. If it happens the client can decide to
fetch data from another replica.

# Strong consistency vs Eventual consistency
Strong:
- Ensures strong consistency throughout the replicas
- Achieves it through sync replication
- transaction is only successful when it goes through all replicas
- Trade off is that it provides consistency at the expense of increased latency and decreased availability.
- Use cases: Use this when correctness is critical at any given moment
    - financial transactions
    - banking systems
    - booking systems
    - patient information systems

Eventual:
- ensures that all replicas will eventually get teh latest write after some time.
- achieves it through async replication
- stale data will be read until the replica's are updated.
- Performance is better
- Trade off is that it provides better performance and decreased latency and increased availability at the expense of inconsistent data across replica's.
- Use cases: Use this when slight delays in synchronization is tolerable and you need super low latency
    - social media feeds or likes
    - messaging apps
    - cdn's

# Latency and Throughput
Latency:
- Measures delay
- The amount of time it takes for a task to go from start to finish.
- measured in milliseconds or seconds.
- lower value is better
- is a crucial factor where real time or near-real time interaction is needed like gaming, video conferencing
- Improve it by:
    - Use CDN's where applicable
    - improve hardware
    - cache frequently used content
    - use faster protocols like http2
    - db optimization
    - load balancing

Throughput:
- the capacity of a system to do X work in Y time.
- the amount of data transferred over a network or processed by the system in a given amount of time.
- measures work done over time.
- measured in data per time (megabits per second)
- higher value is better
- is a crucial factor needing high throughput. bulk datya processing, video streaming
- improve it by:
    - increase network bandwidth
    - scale horizontally
    - cache
    - batch processing

Some times optimizing for one of the above might negatively impact the other.

# ACID vs BASE
- Both are consistency and reliability models which give different benefits
- ACID: 
    - provides strong consistency
    - provides immidiate consistency
    - transactions are all or nothing
    - vertical scalling allowed
    - hard to scale horizontally
    - Use cases:
        - use where strong consistency is needed
        - health care
        - banking
        - inventoy management
    - Examples:
        - MySQL
        - PostgreSQL
        - Oracle
- BASE 
    - provides high availability
    - provides eventual consistent
    - allows partial updates
    - allows horizontal scalling
    - Use cases:
        - Ideal for distributed systems
        - social media feeds
        - gaming
    - Example:
        - Cassandra
        - MongoDB,
        - DynamoDB

# API Gateway:
- Used for API management
- Sits between the client and a collection of backend services.
- Acts as a reverse proxy
- Routes requests, transforms request, performs auth and permissions, rate limiting
- Provides a uniqfied and centralized access for microservices



# Approach:
What approach should be followed to address these questions:
1. Ask questions to scope out the problem
2. Gather functional requirements
    - These are the main features of the application. Things that a user should be able to do with an application. What happens when the feature is used. 
3. Gather non functional requirements
    - How many DAU's ?
    - What throughput ?
    - Storage capacity ?
    - Latency ?
    - Topics being touched on:
        - Latency / performance
        - Availability: non error response / total requests
        - Scalability
        - Reliability
        - Fault tolerance
4. Perform some basic calculations (Back of the envelop calculations)
    - How many DAU's
    - How many reads and writes per day, per hour, per second
    - Throughput
    - 1 second, 1 millisecond, 1 micro second, 1 nano second
    - 1 byte, 1 kilobyte, 1 megabyte, 1 gigabyte, 1 tersbyte, 1 petabyte

# Example designs

# Rate Limiter
Functional Requirements: 
1. Is the rate limiter for some backend API ?
2. Is it just for a single API or multiple ?
3. The rate limiter would be a speparate service.
4. What should happen when the limit is reached ? 
5. How many requests should be allowed in what time ? (Rate limiting rules)
6. 429 error code
7. Client side rate limiting could be accomplished as well by having code in the front end application but it can be bypassed by hitting a service endpoint without the client side code runnnig. Like making an infinite amount of requests from curl.
8. How do we identify users for rate limiting ? User ID ? IP address ? 

Non Functional Requirements:
1. Latency requirements ? Because the rate limiter is an extra service in between the client and the server adding to the latency.
2. Throughput ? 
3. Storage ? What data is being stored in the rate limiter for rate limiting. 132 bytes per user.
4. No of Users ?
5. Availability ? What if the rate limiter goes down. Depends upon what the application is.
    - Fail Open: System functions fine as if the rate limiter never existed. Most users will be able to use the app but also others can abuse the system
    - Fail Closed: Entire system goes down.

High level design:
1. System has a reverse proxy
2. Has rate limiting rules in a database of choice (SQL or NoSQL, does not matter since rate limiter wont be storing that much data and does not need scale)
3. Has an in-memory store to keep track of how many requests a user has already made. This is for read and write performance.
4. 429 is sent if limit is reached.

Detailed Design:
1. What algorithm will be used for rate limiting ? 
    - Fixed window: 100 request per min. This defines a fixed window.
        - Not very accurate and can be abused.
        - less accurate
        - trade off is that it does not require any storage for timestamp stoage like the sliding window approach.
    - Sliding window: every time we get a request we check the last 60 seconds and see how many requests has been made by the user. Every second we shift the window forward by 1. 
        - Is very accurate
        - But needs us to store the timestanmp of every request.
        - trade off is that timsetamp storage will take much more space. 
        - Accomplished with sorted sets in redis
2. Data Schema:
    - What data schema will be used
    - How does that schema mutate and update
    - Schema for rule could be id, target api for the rule, time unit for the rule, operation, number of requests.
3. Caching can be used to improve rules read and write performance. Caching can be done by a separate worker service.

# Steps
At every step, clarify and justify why you have chosen one approach over the other.
1. Clarify functional requirements. (Spend 5 minutes at max)
2. Clarify non functional requirements (Spend 5 minutes at max)
    - What’s the impact if a user sees stale data for a moment? Will it harm the experience or business?
3. Propose High level design and confirm if thats what they want.
4. Update high level design after feedback for 3.
5. Dig into Detailed design.
    - API design
    - Algorithms used in the design and discuss trade offs
    - Data Schemas for persistent and in memory store, write out the schema
6. Discuss any bottlenecks.





