                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         

# Load Balancing

**Load balancing** is defined as the methodical and efficient distribution of network or application traffic across multiple servers in a server farm. Load balancing is vital in enabling our service to scale well, with the increase in the traffic load, as well as stay highly available. Load balancing is facilitated by  load balancers, that makes them a key component in the web application architecture. 

Load balancers distribute heavy traffic load across the servers running in cluster based on several different algorithms. This averts the risk of convergence of all the traffic to a single or a few machines in the cluster. If the entire traffic is converged only to a few machines this will not increase the latency but will also eventually bring them down. Load balancing helps us avoid this. If the server goes down in the middle of processing a user request, the load balancer automatically routes the future requests to other up and running servers in the cluster this enabling the service as a whole to stay available. Load balancers act as a single point of contact for all client requests.

![](./media/LB.jpg)

Load balancers can also be setup to efficiently manage traffic directed towards any component of the application, be it backend, database, message queue or any other. This is done to uniformly spread the request load across the machines in the clusters powering that respective component.

![](./media/LB 2.jpg)

In order to intelligently route all the user requests to the running servers in the cluster, a load balancer should be well aware of their running status. To ensure that the request is always routed to the machine that is up, load balancers regularly perform health checks. A load balancer maintains a list of machines that are running in the cluster. User requests are forwarded only to those machines. If a machine goes down, it is removed from the list. Machines that are up are known as *in-service* whereas machines those are down are known as *out of service* instances.

## Modes of Load balancing

There are basically 3 modes of load balancing -

- DNS Load Balancing
- Hardware-based Load Balancing
- Software-based Load Balancing

## DNS

Every machine on the internet have a unique IP address associated with it which enables the machine to communicate with other machines on the internet. IP in IP address is acronym for *Internet Protocol*. It's a protocol that facilitates the delivery of data packets from one machine to the other using their IP addresses. Typically, IP address is a bunch of numbers that is hard to remember by humans. Since IP addresses are hard to remember, *Domain Names* were introduced. Domain names are an alias for IP addresses. For example, *www.google.com* is a domain name which has an IP address associated with it. The system of finding the IP address associated with a domain name is called **Domain Name System** or DNS.

Whenever a user enters the URL in the browser, the event is known as DNS querying. There are four key components, i.e. a group of servers, that make up the DNS infrastructure. Those are:-

- DNS Resolver
- Root Nameserver
- Top-Level Domain Nameserver
- Authoritative Nameserver

![](./media/DNS 1.jpg)

When a user browser enters a URL in browser, the browser sends a request to the DNS Recursive nameserver, which is also known as DNS Resolver. The DNS Resolver forwards the request to the root nameserver which returns the address of the top level domain name server in response. Once the DNS Resolver receives the address if the top level domain nameserver, it sends the request to fetch the details of the domain name. Top level domain nameservers hold data for domains using their top level domain. A top level domain to the part of domain name that comes after dot, for example com, in, org, etc. are top level domains. The TLD nameserver return the IP address of the authoritative nameserver for the domain. The authoritative nameserver contains information specific to the domain name it serves. It returns the IP address of that server to the DNS Resolver, which returns it to the client so that the client can communicate it.

![](./media/DNS Lookup.jpg)

Often the DNS information is cached in the browser or the DNS Resolver, so the entire process does not need to be repeated every time user enters a domain in the browser.

## DNS Load Balancing

DNS load balancing enables the authoritative server to return different IP addresses of a certain domain to the clients. This enables to spread the user traffic across different clusters in different data centers. Every time the authoritative server received a query for IP, it returns a list of IP addresses of a domain to the client. With every request, the authoritative server changes the order of the IP address in the list in a round-robin fashion. As the client receives the list, it sends out a request to the first IP address on the list to fetch the data from the website. If the first IP address does not return a response within the stipulated time, the client sends request to the second IP address in the list.

## Hardware-based Load Balancers

Hardware load balancers are highly performant physical hardware, that sit in front of the application servers and distribute the load based on the number of the existing open connections to a server, compute utilization and several other parameters. Since these load balancers are physical hardware, they need maintenance and regular updates, and are expensive to setup. Hardware load balancers are primarily used for their top-notch performance.

## Software-based Load Balancers

Software load balancers are software that can be installed on the commodity hardware and VMs. They are more cost-effective and flexible. They can be upgraded and provisioned more easily. They are pretty advanced when compared to DNS load balancers as they consider parameters such as CPU Utilization, memory Utilization and load on the network. They use many algorithms other than round-robin to route traffic effectively.

## Algorithms used by Load Balancers

Load balancers use the following algorithms to route the traffic efficiently across different machines:-

### Round Robin and Weighted Round Robin

*Round Robin algorithm* sends *IP address* of machines sequentially to the clients. Parameters such as *load on the servers*, their *CPU consumption* and so on are not taken into account when sending the *IP addresses* to the clients.

In *Weighted Round Robin*, weights are assigned to each server based on their compute and traffic handling capacity, and then based on these weights, traffic is routed to them using *Round Robin Algorithm*. This approach is pretty useful when the service is deployed in different data centers having different compute capacities. More traffic can be directed to the larger data centers containing more machines.

### Least Connections

In this algorithm, the traffic is routed to the machine that has the least open connections out of all the machines in the cluster. Another approach in this method is to route traffic to machine that has least CPU Utilization and request processing time along with least open connections. The least connections approach comes in handy when the server has long opened connections, for instance, persistent connections in a gaming application.

### Hash

In this approach, the source IP where the request is coming from and the request URL are hashed to route the traffic to the backend servers. Hashing the source IP ensures that the request of client with a certain IP will always be routed to the same server.

This facilitates better user experience as the server has already processed the client request and holds the client's data in its local memory. Hashing also allows the client to re-establish the connection to the same server in case the connection drops. Also, hashing URL will ensure that the request with that URL always hits a certain cache that already has data on it. This also averts the need for duplicating data in every cache and is thus a more efficient way to implement caching.

