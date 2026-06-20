# Administer Network Traffic

## Manage and control traffic flow in your Azure deployment with routes

### Azure routing

#### System routes

Azure automatically assigns system routes to every subnet, which is why VMs in the same virtual network can communicate with one another by default, and can potentially reach on-premises and the internet.

System routes cannot be deleted, but can be overwritten.

Every subnet has the following default system routes.

* virtual network - a route is created in the address prefix, for each address range. Resources in the same virtual network can communicate, even when on different subnets.
* Internet - The default system route 0.0.0.0/0 routes any address range to the internet, unless you override Azure's default route with a custom route.
* None - by default, private-address prefixes are created: 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16 are not globally routed, traffic gets dropped at the subnet

When the following features are enabled, extra system routes are created;

* Virtual network peering - Connects two virtual networks privately so resources can communicate using private IP addresses, as though they were on one larger network.
* Virtual network gateway - A managed gateway placed in a virtual network that enables encrypted connectivity between networks, such as site-to-site VPN, point-to-site VPN, or connections to an on-premises network. It can also be used for VNet-to-VNet VPN connections.
* Virtual network service endpoints - Virtual network endpoints extend your private address space in Azure by providing a direct connection to your Azure resources. This connection restricts the flow of traffic: your Azure virtual machines can access your storage account directly from the private address space and deny access from a public virtual machine.&#x20;
* Service chaining - Routes traffic through one or more network virtual appliances before it reaches its destination—for example, sending traffic through a firewall, proxy, or inspection appliance using user-defined routes

#### Custom routes

Custom routes are required for ad hoc situations, to route traffic through an NVA or firewall; custom routes should be defined

Two options for defining custom routes

* User-defined routes
  * When creating a UDR, one can define the following hop types
    * Virtual appliance - route traffic to the NIC of an NVA or to the IP of an internal load-balancer
    * virtual network gateway - route a specific address to a virtual network gateway. The virtual network gateway is specified as a VPN for the next hop type.
    * virtual network - override the default system route within a virtual network.
    * Internet - Use to route traffic to a specified address prefix that is routed to the internet.
    * None - Use to drop traffic sent to a specified address prefix.
  * With user-defined routes, you can't specify the next hop type **VirtualNetworkServiceEndpoint**, which indicates virtual network peering.
* Border gateway protocol
  * used to exchange routes dynamically between an on-prem gateway and an Azure VPN gateway, typically over ExpressRoute, but also usable over a site-to-site VPN.
  * BGP offers network stability, because routers can quickly change connections to send packets if a connection path goes down.

#### Service tags for User-defined routes

Instead of typing explicit IP ranges into a UDR, you can reference a service tag (a Microsoft-managed group of IPs for a given Azure service) that updates automatically as the underlying IPs change. Useful so you're not maintaining stale IP lists by hand

#### Route selection and priority

When multiple routes match, Azure picks the longest (most specific) prefix match — e.g., a /24 wins over a /16 for the same destination.&#x20;

You can't configure multiple user-defined routes with the same address prefix.

If there are multiple routes with the same address prefix, Azure selects the route based on the type in the following order of priority:

* user-defined routes&#x20;
* BGP routes
* system routes

So a UDR you create will always beat what Azure auto-generates, by design — that's literally the point of UDRs.

### Network Virtual Appliances

It is a VM combining one or more of

* firewall
* Application delivery controller, e.g., F5
* routers
* load balancers
* WAN optimizer
* proxies
* IDS/IPS

You don't build this from scratch — you deploy a pre-built image from Azure Marketplace, from vendors like Cisco, Check Point, Barracuda, Sophos, WatchGuard, and SonicWall.

An NVA is a third-party alternative to Azure Firewall, sitting in the traffic path to perform inspection.

Network virtual appliances (NVAs) are virtual machines that control the flow of network traffic by controlling routing. You'll typically use them to manage traffic flowing from a perimeter-network environment to other networks or subnets.

#### Deployment patterns

* Simple - put a firewall appliance in a perimeter-network subnet in the virtual network.
* microsegmentation - you can create dedicated subnets for the firewall and then deploy web applications and other services in other subnets. All traffic is routed through the firewall and inspected by the NVAs

The second approach is the more rigorous one - it lets the appliance inspect at OSI Layer 4, and for application-aware appliances, Layer 7, rather than just sitting at the edge.

Some NVAs require multiple network interfaces.&#x20;

One network interface is dedicated to the management network for the appliance.&#x20;

Additional network interfaces manage and control the traffic processing.&#x20;

After you’ve deployed the NVA, you can then configure the appliance to route the traffic through the proper interface. Sensible isolation so management access doesn't compete with or get exposed alongside production traffic.

#### UDRs

You create custom routes mainly for two scenarios:&#x20;

* forced tunneling internet access via on-premises
* using NVAs to control traffic flow

You can create multiple route tables, each associated with one or more subnets.

But a given subnet can only be associated with one route table at a time.&#x20;

That last constraint is a good one to remember — it's a common point of confusion (people expect many-to-many, it's many-to-one)

#### High availability

Since traffic routes through the NVA, it becomes a critical single point of failure — any NVA outage directly breaks service communication, so HA architecture is important.

#### Steps to deploy an NVA

1. **Choose the appliance**\
   Either configure a Windows/Linux VM yourself, or use a partner image from Azure Marketplace (Cisco, Check Point, Barracuda, Sophos, WatchGuard, SonicWall, etc.)&#x20;
2. **Plan the network placement**\
   Decide on architecture — simple perimeter-subnet placement, or microsegmentation with dedicated subnets for the firewall, separate from where web applications and other services live&#x20;
3. **Update subnets**\
   Make sure the relevant subnets exist and are structured to route traffic through the appliance (this is the "subnets have been updated" piece)
4. **Configure routing tables and user-defined routes (UDRs)**\
   Create or update route tables with UDRs pointing traffic toward the NVA's private IP as the next hop — remembering each subnet can only be associated with one route table
5. **Enable IP forwarding on the NVA's network interface(s)**\
   This is the step that turns the VM from "an endpoint that drops non-self-addressed traffic" into "a router that forwards requests between subnets." Some NVAs need multiple NICs — one for management, others for the actual traffic processing — if so, enable forwarding on the data-plane NICs.&#x20;
6. **Connect to the relevant networks**\
   Attach the appliance to both the production and perimeter networks (or whichever subnets are part of the traffic path)
7. **Configure the appliance itself**\
   Set up firewall rules, inspection policies, or whatever the appliance's actual function is (Layer 4 or Layer 7 inspection)
8. **Plan for high availability**\
   Since the NVA becomes a critical piece of infrastructure and any failure directly affects service communication, deploy with redundancy in mind (e.g., paired NVAs behind a load balancer) rather than as a single instance

## Introduction to Azure Load Balancer

Azure Load Balancer distributes inbound traffic across a set of VMs in a back-end pool.&#x20;

The back-end pool can be made up of Azure infrastructure as a service (IaaS) VMs or instances in a Virtual Machine Scale Set.&#x20;

You can configure how incoming traffic is distributed across the back-end pool using load-balancing rules.&#x20;

A pool of computers that have lower levels of resources often responds to traffic more effectively than a single server with higher performance.

You can ensure that traffic isn't directed to unresponsive nodes using health probes.

### Public vs Internal load balancer

**Public** - load balances internet traffic to VMs, mapping the public IP/port of incoming traffc to the private IP/port of backend pool VMs

**Internal** - directs traffic to resources inside a virtual network ot accessed via VPN, it's front-end IP and virtual network are never directly exposed to the internet

#### Internal load balancer use cases

An internal load balancer enables the following types of load balancing:

* **Within a virtual network**: Load balancing from VMs in the virtual network to a set of VMs that reside within the same virtual network.
* **For a cross-premises virtual network**: Load balancing from on-premises computers to a set of VMs that reside within the same Azure virtual network.
* **For multi-tier applications**: Load balancing for internet-facing multi-tier applications where the back-end tiers aren't internet-facing. The back-end tiers require traffic load balancing from the internet-facing tier.
* **For LOB (Line of Business) applications**: Load balancing for LOB applications that are hosted in Azure without added load balancer hardware or software (PaaS - use Azure load balancer that comes with the application, no need to deploy one). This scenario includes on-premises servers that are in the set of computers whose traffic is load-balanced.

### Scale

Both load balancer types support inbound and outbound traffic and scale to millions of TCP/UDP flows.

### How the Azure load balancer works.

Operates at the transport layer of the OSI model, and consists of the following elements

* Front-end IP
  * The IP address that clients use to connect to your web application. It can either be public or private, depending on the type of load balancer (public or internal)
  * can have more than one public or private IP address
* load balancer rules
  * defines how traffic is distributed to the back-end pool
  * The rule maps the front-end IP/port to a set of back-end IP addresses/ports
  * Traffic is managed by a 5-tuple set consisting of
    * source ip
    * source port
    * destination ip
    * destination port
    * protocol type
    * session affinity - ensures that the same pool node handles traffic for a client
* back-end pool
  * The back-end pool is a group of VMs or instances in a Virtual Machine Scale Set that responds to the incoming request.
  * Load Balancer implements automatic reconfiguration to redistribute load across the altered number of instances when you scale instances up or down
* Health probes
  * determine the status of an instance in the backend pool.
  * by default, the load balancer probes your endpoint every _15 seconds_
  * When a probe fails to respond, the load balancer stops sending new connections to the unhealthy instances. A probe failure doesn't affect existing connections. The connection continues until:
    * The application ends the flow.
    * Idle timeout occurs.
    * The VM shuts down.
  * types of probes
    * tcp custom probe - relies on establishing a TCP session successfully
    * HTTP(S) custom probe - checks for HTTP 200 within a defined time period
* session persistence/session affinity/source IP affinity/client IP affinity
  * Session persistence specifies how traffic from a client should be handled. The default behavior (None) is that any healthy VM can handle successive requests from a client.
  * Session persistence is about a **client's repeated connections** going to the same backend
  * When you use session persistence, connections from the same client go to the same back-end instance within the back-end pool.&#x20;
  * With persistence turned on, the load balancer uses a smaller hash — a two-tuple (source IP + destination IP) or three-tuple (source IP + destination IP + protocol) instead of the full five-tuple — so that future connections from the _same client IP_ keep landing on the _same backend VM_, across multiple separate connections, not just within one
  * You can configure one of the following session persistence options:
    * **None (default)**: any healthy VM handles the request (= 5-tuple (src IP, src port, dst IP, dst port, protocol))
    * **Client IP (2-tuple)**: same backend instance handles repeat requests from the same client IP (2-tuple (src IP, dst IP))
    * **Client IP and protocol (3-tuple)**: same, but also matched on protocol
  * This matters for stateful apps — if your app stores session state locally on one VM instead of in a shared cache, you'd want client IP affinity so a user's requests keep landing on the same instance.
  * Session persistence is useful, but ideally, applications should store sessions in a shared service such as Redis, a database, or a distributed cache. Then any backend server can handle the request, improving scalability and resilience
* high availability ports
  * A load balancer rule configured with `protocol - all and port - 0` is known as a _high availability (HA) port rule_.&#x20;
  * This rule enables a single rule to load balance all TCP and UDP flows that arrive on all ports of an internal standard load balancer.
  * The load-balancing decision is made per flow. This action is based on the five-tuple connection (= 5-tuple (src IP, src port, dst IP, dst port, protocol))
  * HA port rules are specifically useful for high availability and scale of NVAs inside virtual networks, especially when a large number of ports need balancing.

{% hint style="info" %}
HA Ports - This is _how_ you actually load-balance a pair of NVAs. A normal load balancer rule binds to specific ports; an NVA inspecting arbitrary traffic needs _all_ ports balanced, which is exactly what this rule type is for.
{% endhint %}

* Inbound NAT rules
  * Combine load balancing with NAT — e.g. map the load balancer's public IP to TCP 3389 on one specific VM, enabling RDP access from outside Azure
* Outbound rules
  * An outbound rule configures Source Network Address Translation (SNAT) for all VMs or instances identified by the back-end pool. This rule enables instances in the back end to communicate (outbound) to the internet or other public endpoints.

### When to use Azure Load Balancer

* best suited for applications needing ultra-low latency and high performance
* replicating the functionality of an on-premise load balancer
* Not suitable for a single VM web app with low traffic - no need to add a load balancer when there is nothing to load balance

#### Alternatives to Azure Load Balancer

* Azure Front Door
  * provides global load balancing and site acceleration service for web applications
  * best suited for load balancing web apps deployed across multiple Azure regions
  * offers layer 7 capabilities for applications such as&#x20;
    * TLS/SSL offload
    * path-based routing
    * fast failover
    * WAF
    * caching
* Azure Traffic Manager
  * DNS-based traffic load balancer that distributes traffic to services across global azure regions
  * It load balances only at the domain level
  * Because of DNS caching and systems not honoring DNS TTL, failover is not as quick as in Front Door
* Azure Application Gateway
  * Provides application delivery controller as a service, with layer 7 load balancing capabilities
  * works within a region rather than globally
  * handles TLS/SSL offloads

#### The four-way comparison

| Service                 | Layer     | Scope                    | Best for                                                                             |
| ----------------------- | --------- | ------------------------ | ------------------------------------------------------------------------------------ |
| **Azure Load Balancer** | Layer 4   | regional, zone-redundant | ultra-low-latency TCP/UDP, millions of requests/sec                                  |
| **Application Gateway** | Layer 7   | regional                 | TLS offload, web farm optimization, **WAF**                                          |
| **Front Door**          | Layer 7   | global                   | multi-region apps, TLS offload, path-based routing, **WAF**, caching, fast failover  |
| **Traffic Manager**     | DNS-based | global                   | domain-level routing only — slower failover due to DNS caching/TTL issues            |

#### The mental model to take away

This is really a **Layer 4 vs Layer 7, regional vs global** 2×2:

* Layer 4 + regional → **Load Balancer**
* Layer 7 + regional → **Application Gateway**
* Layer 7 + global → **Front Door**
* DNS-only + global → **Traffic Manager** (the laggard option, mentioned mainly for completeness/legacy reasons)

## Introduction to Azure Application Gateway

Application Gateway manages client requests to web apps hosted on a pool of web servers. The pool of web servers can be

* Azure VMs
* virtual machine scale sets
* app service
* on-premises servers

{% hint style="info" %}
A load balancer only works with Azure VMs or VM scale sets
{% endhint %}

### Core features

* HTTP traffic load balancing
* WAF - protect against web application vulnerabilities
* TLS/SSL encryption - End to End
* websocket protocol support
* Autoscaling of traffic - dynamically adjust capacity with web traffic
* Connection draining - allowing graceful removal of back-end pool members, e.g., during service updates

### Load balancing method — different from Azure Load Balancer

Application Gateway uses _round-robin_ to distribute requests across the back-end pool. Compare this to Azure Load Balancer's five-tuple hash — round-robin is simpler and inherently Layer 7-aware since it's operating per-HTTP-request rather than per-flow.

#### Session stickiness — same concept, application-layer version

Session stickiness ensures requests within the same session land on the same backend server - important for e-commerce, where you don't want a transaction disrupted by bouncing between servers.&#x20;

The mechanism differs (Application Gateway typically uses a _cookie_ to track the session, since it can actually see HTTP), but the goal is identical: same client, same backend, for the duration of that interaction. Load balancers use a 2 or 3-tuple for stickiness.

### How it works

Azure Application Gateway has a series of components that combine to securely route and load balance requests across a pool of web servers. Application Gateway includes the following components:

* Front-end IP address
  * can be a public or private IP address
  * can't have more than one public or private IP address
* listeners
  * They are the entry point
  * one or more listeners to receive incoming requests
  * A listener is set up to listen for a specific host name, and a specific port on a specific IP address.
  * A listener accepts traffic on a specific combination of _protocol_, _port_, _host_, and _IP address_, and routes it to a back-end pool per your routing rules.
  * Basic listeners route only by URL path;&#x20;
  * Multi-site listeners can also route by hostname.
  * Listeners handle TLS/SSL certs for the user-facing connection
* Routing rules
  * the glue
  * A rule binds a listener to back-end pools, interpreting the hostname/path of the request to pick the right pool — and carries an associated HTTP settings block controlling whether/how traffic is encrypted to the back end, plus protocol, session stickiness, connection draining, timeout, and health probes.

**front-end IP → listener (decides which traffic, terminates TLS) → routing rule (decides which pool, decides re-encryption) → back-end pool**.

Here's the precise relationship:

* The **front-end IP** is a resource you provision on the gateway (e.g. `20.50.1.10`)
* A **listener** is a separate configuration object that you create and explicitly assign:&#x20;
  * which front-end IP to bind to,&#x20;
  * which port to watch,&#x20;
  * which protocol (HTTP/HTTPS),&#x20;
  * and optionally which hostname to match

<figure><img src=".gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

### Load balancing in Application Gateway

Round-robin, operating on Layer 7 routing parameters (hostnames and paths) — versus Azure Load Balancer, which works at Layer 4 based on IP address.

You can configure session stickiness if you need to ensure that all requests for a client in the same session are routed to the same server in a back-end pool.

### Web Application Firewall

* an optional component
* sits in front of the listener and handles requests before they hit the listener
* checks requests against OWASP-based threats: SQL injection, XSS, command injection, HTTP request smuggling, response splitting, remote file inclusion, bots/crawlers/scanners, and protocol violations
* OWASP defines a set of generic rules for detecting attacks. These rules are referred to as the Core Rule Set (CRS). The rule sets are under continuous review as attacks evolve in sophistication.&#x20;
* WAF supports four rule sets: CRS 3.2, 3.1, 3.0, and 2.2.9. CRS 3.1 is the default.&#x20;
* If necessary, you can opt to select only specific rules in a rule set, targeting certain threats.
* Additionally, you can customize the firewall to specify which elements in a request to examine, and limit the size of messages to prevent massive uploads from overwhelming your servers.

### Back-end pools

* A pool can mix fixed VMs, scale sets, App Service, or on-prem servers — all members should be configured identically, including security settings.
* If using TLS, the pool's HTTP setting references a certificate, and the gateway re-encrypts traffic with that certificate before forwarding to a back-end pool.&#x20;
* If you're using Azure App Service to host the back-end application, you don't need to install any certificates in Application Gateway to connect to the back-end pool. All communications are automatically encrypted. Application Gateway trusts the servers because Azure manages them.
* Application Gateway uses a rule to specify how to direct the messages that it receives on its incoming port to the servers in the back-end pool. If the servers are using TLS/SSL, you must configure the rule to indicate:
  * That your servers expect traffic through the HTTPS protocol.
  * Which certificate to use to encrypt traffic and authenticate the connection to a server.

### Application gateway routing

When the gateway routes a client request to a web server in the back-end pool, it uses a set of rules configured for the gateway to determine where the request should go.&#x20;

There are two primary methods of routing this client request traffic:&#x20;

#### Path-based routing

Path-based routing sends requests with different URL paths to different pools of back-end servers.

Different URL paths route to different pools — e.g.,/video/\* to a streaming-optimized pool, /images/\* to an image-retrieval pool.

#### Multiple-site routing

Multiple-site routing configures more than one web application on the same Application Gateway instance.

In a multi-site configuration, you register multiple domain name system names (CNAMEs) for the IP address of the application gateway, specifying the name of each site.

Application Gateway uses separate listeners to wait for requests for each site. Each listener passes the request to a different rule, which can route the requests to servers in a different back-end pool.&#x20;

For example, you could direct all requests for `http://contoso.com` to servers in one back-end pool, and requests for `http://fabrikam.com` to another back-end pool.&#x20;

Multi-site configurations are useful for supporting multitenant applications, where each tenant has its own set of virtual machines or other resources hosting a web application.

Application Gateway routing also includes these features:

* **Redirection**. Redirection can be used to another site, or from HTTP to HTTPS.
* **Rewrite HTTP headers**. HTTP headers allow the client and server to pass parameter information with the request or the response.
* **Custom error pages**. Application Gateway allows you to create custom error pages instead of displaying default error pages. You can use your own branding and layout using a custom error page.

### TLS/SSL Termination

When you terminate the TLS/SSL connection at the application gateway, it offloads the CPU-intensive TLS/SSL termination workload from your servers.

Also, you don’t need to install certificates and configure TLS/SSL on your servers.

If you need end-to-end encryption, Application Gateway can decrypt the traffic on the gateway by using your private key, then re-encrypt it again with the public key of the service running in the back-end pool.

A listener can use an TLS/SSL certificate to decrypt the traffic that enters the gateway. The listener then uses a rule that you define to direct the incoming requests to a back-end pool.

<figure><img src=".gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

In this configuration, your web servers aren't directly accessible from the internet, which reduces the attack surface of your infrastructure.

### Health probes

Health probes determine which servers are available for load-balancing in a back-end pool. The Application Gateway uses a health probe to send a request to a server.&#x20;

When the server returns an HTTP response with a status code between 200 and 399, the server is considered healthy.

If you don't configure a health probe, Application Gateway creates a default probe that waits for 30 seconds before deciding that a server is unavailable.&#x20;

Health probes ensure that traffic isn't directed to a nonresponsive or failed web endpoint in the back-end pool.

{% hint style="info" %}
Load Balancer's HTTP probe wants exactly 200; Application Gateway's wants the whole 2xx–3xx range.
{% endhint %}

### Autoscaling

Application Gateway supports autoscaling and can scale up or down based on changing traffic load patterns. Autoscaling also removes the requirement to choose a deployment size or instance count during provisioning.

### WebSocket and HTTP/2 traffic

The WebSocket and HTTP/2 protocols enable full-duplex communication between a server and a client over a long-running Transmission Control Protocol (TCP) connection.&#x20;

This type of communication is more interactive between the web server and the client, and can be bidirectional without the need for polling as required in HTTP-based implementations.&#x20;

These protocols have low overhead (unlike HTTP) and can reuse the same TCP connection for multiple requests/responses, resulting in more efficient resource utilization.&#x20;

These protocols are designed to work over traditional HTTP ports of 80 and 443.

### When to use Azure Application Gateway

* Can route traffic from an Azure endpoint to a back-end pool containing on-premises servers and app services, with health probes ensuring traffic avoids any server that goes down
* TLS termination at the gateway reduces the CPU load on back-end servers from encryption/decryption
* The web application firewall blocks XSS and SQL injection before it reaches the back-end pool
* Session affinity is supported
* Not suitable for a single VM web app with low traffic - no need to add a load balancer when there is nothing to load balance
