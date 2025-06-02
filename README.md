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
- Caching involves copying data from memory to Cache for future use since its faster. 
- Increases throughput
- Decreases networking cost
- In terms of systyem design and local cache may mean that a server has a local copy of the data on the local disk or in memory so that the server does not need to make a request over 
the network, which would be slower.
- Cache hit is when a data needed by the server is present in the cache
- Cache miss is when the data needed by the server is not present in the cache
- Cache ratio = hit / hit + miss
- Key value in memory stores like Redis is used for caching 
- Caching algorithms:
    - Write around cache: Data is first written to disk / db and then the second request for the data is read first from the db (cache miss), then populate the cache with teh data and then is returned.
    - Write through cache: Data is written to the cache and the disk. Less cache misses. But more unneccessary data getting in to the cache.
    - Write back cache: Data is written to cache only. Cache data is then periodically written to disk in batches. It is faster, but data loss can occur. Use it for data that is fine to be lost sometimes.
- Eviction policy:
    - Cache size is limited so an eviction policy is to be implemented.
    - An eviction policy's sole purpose is to maximize the cache hit ratio.
        - Random: Randomly select data to be evicted. Not very useful.
        - FIFO: Data added to the cache first is evicted first. Not very smart either.
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
    - Forward Proxy:
        - A proxy is a middle server that in forward proxying forwards requests to the primary application server. Client is hidden from the server.
        - Proxy servers can bypass geo restrictions
        - Proxy's can also block specific traffic.
        - VPN's are forward proxy
        - Anonymises user
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
    - Algorithm's:
        - Round robin: This strtegy involves goind round robin and send requests to each of the servers one after the other. Each server gets an even amount of traffic.
        - Weighted Round robin: A weight is given to a server based on its power, The load balancer than performs the round robin forwarding based on those weights.
        - Least connections: Requests are routed to the server which has the least number of current connections.
        - User location: If we have servers in different geo locations we can route user requests based on the users locations and route request to server which is closer to the user.
        - Hashing: 
    - Types:
        - Layer 4: TCP layer load balancer. Faster. Location based balancing. Cna access IP. But cannot access application layer data. Application data is not unpacked.
        - Layer 7: Application layer load balancer. Can access application layer data. slower but more flexible. can see auth, url paths.
    - What happens if the load balancer itself goes down?:
        - Have multiple replicas of load balancer
        - Have a backup load balancer

# Consistent hashing

 




