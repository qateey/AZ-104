# Administer Virtual Networking

## Configure virtual networks

Virtual networks are an essential component of creating private networks in Azure. They allow different resources to securely communicate with each other, the internet, and on-premises networks.

### Plan Virtual Networks (VNet)

**Characteristics of virtual networks**

* A virtual network is a logical isolation of Azure cloud resources.
* Have their own CIDR block and can be linked to other virtual networks and on-premises networks.
* used in the implementation of virtual private networks (VPNs)
* can be linked to on-premises IT infrastructure to create hybrid or cross-premises solutions, as long as their CIDR subnet blocks do not overlap
* You control the DNS server settings and virtual network segmentation.
* In each subnet, you can provision multiple resources, e.g., VMs with different IPs on the NICs

**Scenarios for using virtual networks**

* Create a dedicated private cloud-only virtual network
* Securely extend your data center with virtual networks - use site-to-site VPNs to provide a secure connection between your corporate gateway and Azure (from on premises outward to the internet)
* Enable hybrid cloud scenarios - securely connect cloud-based applications to your on-premises infrastructure (from cloud application to on-premises - inward)

### Create subnets

Provide a way to implement logical divisions within your virtual network to increase security, improve performance, and enable easier management.

**Properties of subnets**

A subnet

* must contain a range of IPs that fall within the virtual network address space
* can be created on the Azure portal
* Should have a unique IP range within the address space of the virtual network
* cannot overlap with another subnet in the same virtual network
* IP address space must be specified using CIDR notation

#### Reserved addresses

For each subnet, Azure reserves 5 IP addresses, the first 4 and the last 1

for a subnet 192.168.2.0/24

* 192.168.2.0 - identifies the virtual network address space
* 192.168.2.1 - serves as the gateway IP
* 192.168.2.2 - Azure DNS IP address to the virtual network address
* 192.168.2.3 - Azure DNS IP address to the virtual network address
* 192.168.2.255  - Virtual network broadcast address

#### What to consider when using subnets

* Consider service requirements
  * Each service deployed into a virtual network has specific requirements for routing and the types of traffic to be allowed in and out of the address space
  * A service may require or create its own subnet
  * There must be enough unallocated space to meet the service requirements
* Consider network virtual appliances
  * By default, Azure routes network traffic between all subnets in a virtual network (no configuration needed)
  * You can override Azure's default routing
    * to prevent Azure routing between subnets
    * to route traffic between subnets (has to be different subnets, not within a subnet) through a network virtual appliance, e.g., a VM acting as a firewall, a router, IDS/IPS. (There is no routing decision to be made within a subnet; traffic never hits the route table.)
* Consider network security groups
  * You can associate zero or one security group to each subnet in a virtual network
* Consider private links
  * Azure Private Link provides private connectivity from a virtual network to Azure platform as a service (PaaS), customer-owned, or Microsoft partner services.&#x20;
  * Private Link simplifies the network architecture and secures the connection between endpoints in Azure. The service eliminates data exposure to the public internet.

### Creating virtual networks

Requirements for creating a virtual network

* When you create a virtual network, you need to define the IP address space for the network.
* Plan to use an IP address space that's not already in use in your organization.
* The address space for the virtual network can be either on-premises or in the cloud, but not both.
*   To create a virtual network, you need to define at least one subnet.



### Plan IP addressing

2 types of Azure IP addresses

* Private IP addresses
  * enable communication within a virtual network and on-premises network
  * required when you extend your network to Azure via VPN gateway or Express Route

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**VPN Gateway Has Both public and private ip addresses**

| IP type        | Purpose                                                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Public IP**  | Used to establish the IPsec tunnel endpoint — your on-premises VPN device needs a public IP to reach the Azure gateway across the internet |
| **Private IP** | Sits inside the GatewaySubnet (e.g. `10.0.255.4`) — used for routing traffic _within_ the VNet once the tunnel is up                       |

**What Lives in GatewaySubnet**

```
GatewaySubnet  10.0.255.0/27
└── VPN Gateway
    ├── Private IP: 10.0.255.4   ← routes traffic inside the VNet
    └── Public IP:  20.x.x.x     ← tunnel endpoint, faces the internet
```

* Public IP addresses
  * Allow your resource to communicate with the internet or Azure public-facing services

#### IP addresses

* can be statically or dynamically assigned
* Static and dynamic assigned IP addresses can be separated into different subnets

#### Create public IP addressing

Settings

* IP version - IPv4 and IPv6 addresses are charged at the same rate
* SKU - a public SKU must match the SKU of the load balancer with which it is used, e.g., Standard public IP <-> Standard load balancer, Basic public IP <-> Basic load balancer
* Tier - Must match the load balancer tier
* IP address assignment - Static or Dynamic.&#x20;
  * Static addresses are assigned when a public IP address is created and are not released until the public IP address is deleted
  * Dynamic public IPs are assigned when the resource starts up

A public IP address can be associated with

* Virtual machine NICs
* Internet-facing load-balancers
* VPN gateway
* Application gateways

#### Public IP Address SKU features

Azure has two SKUs for public IP addresses — **Basic** and **Standard** — and they differ significantly in capability.

SKU - stock keeping unit, term borrowed by Microsoft for Azure to mean **tier or version of a service** that defines its capability level and price point (product tier selector, e.g., basic vs standard)

**Side-by-Side Comparison**

| Feature                         | Basic                    | Standard                                          |
| ------------------------------- | ------------------------ | ------------------------------------------------- |
| **IP assignment**               | Static or Dynamic        | Static only                                       |
| **Availability zones**          | Not supported            | Zone-redundant or zonal                           |
| **Security (inbound)**          | Open by default          | Closed by default — requires NSG to allow traffic |
| **Load balancer compatibility** | Basic LB only            | Standard LB only                                  |
| **Routing preference**          | Not supported            | Supported (Microsoft network or internet routing) |
| **Global tier**                 | Not supported            | Supported (cross-region LB)                       |
| **Idle timeout**                | 4–30 minutes             | 4–30 minutes                                      |
| **IPv6**                        | Supported (dynamic only) | Supported (static)                                |
| **Retirement**                  | Being retired Sept 2025  | Current recommended SKU                           |

| Public IP address | Standard SKU                                                       |
| ----------------- | ------------------------------------------------------------------ |
| Allocation method | Static                                                             |
| Security          | Secure by default model                                            |
| Available zones   | Supported. Standard IPs can be nonzonal, zonal, or zone-redundant. |

#### Allocate or assign private IP addresses

A private IP address resource can be associated with&#x20;

* virtual machine network interfaces
* internal load balancers
* application gateways

Addresses can be statically or dynamically assigned.

## Configure Network Security groups

How to limit network traffic to resources in your virtual network

**Network security groups**

* a list of security rules that allow or deny inbound or outbound traffic.
* can be assigned to a subnet or a network interface, to control all traffic flowing through the NIC
* can be associated multiple times
* can be used to create a protected screened subnet (DMZ), controlling traffic flow to machines residing in the subnet
* Each subnet can have a maximum of one network security group
* Each network interface that exists in a subnet can have zero or one associated security groups

**Security rules**

* enable you to filter network traffic
* Azure creates a default security rule in each network security group, including inbound and outbound traffic
* Default security groups cannot be deleted
* You can add security rules to a security group by adding conditions:&#x20;
  * source  - Any, IP addresses, My IP address, Service tag, Application security group. Identifies how the security rule controls _inbound traffic_
  * source port ranges
  * destination - Any, IP addresses, Service tag, Application security group. Identifies how the security rule controls _outbound traffic_
  * Service _-_ specifies the destination protocol and port range, can be predefined, e.g., SSH or a port range, e.g., 22
  * protocol - e.g., TCP, UDP, ICMP, Default(Any)
  * action - allow or deny
  * Priority - unique value between 100 and 4096, lower priority rules have higher precedence. it's good practice to leave gaps between numbering, so that you can add new rules without editing existing ones
* To override a default security rule, add a new rule with a lower priority (higher precedence)

Augmented security rules - A single network security group can contain multiple values in the Source, Destination, and Service fields. This prevents security group rule sprawl

{% hint style="info" %}
For example, instead of creating four separate rules for ports 80, 443, 8080, and 8090, create one rule with all the ports.
{% endhint %}

**Default Inbound security rules**

Azure creates 3 default inbound security rules per security group, in order of precedence

* allow inbound traffic from your virtual network&#x20;
  * Source: Virtual Network
  * Destination: Virtual network
  * Action: Allow
* Allow inbound traffic from Azure load balancers
  * Source: Azure load balancer
  * Destination: Any
  * Action: Allow
* Deny all other traffic
  * Source: Any
  * Destination: Any
  * Action: Deny

_Summary: Deny all inbound traffic except from the virtual network and Azure load balancers_

**Default Outbound security rules**

Azure creates 3 default outbound security rules per security group, in order of precedence

* Allow outbound traffic from your virtual network&#x20;
  * Source: Virtual Network
  * Destination: Virtual network
  * Action: Allow
* Allow outbound traffic to the internet
  * Source: Any
  * Destination: Internet
  * Action: Allow
* Deny all other traffic
  * Source: Any
  * Destination: Any
  * Action: Deny

_Summary: Deny all outbound traffic except to the virtual network and the internet_

### Determine network security group effective rules

How Azure ends up applying your defined security rules for a virtual machine determines the overall effectiveness of your rules.

A VM can have two security groups acting on it simultaneously, one on the subnet and one on the NIC, and Azure combines them

**Evaluation order**

Each security group is evaluated independently.&#x20;

Both inbound and outbound rules are considered based on

* priority
* processing order

For inbound traffic, Azure processes the subnet security group first, followed by the NIC security group

For outbound traffic, Azure processes the NIC security group first, followed by the subnet security group

{% hint style="info" %}
**INBOUND**: Internet → \[Subnet NSG] → \[NIC NSG] → VM

**OUTBOUND**: VM → \[NIC NSG] → \[Subnet NSG] → Internet

Both security groups are evaluated _independently_; passing one does not skip the other. Traffic must be allowed by both to get through
{% endhint %}

#### Things to consider when creating effective rules <a href="#things-to-consider-when-creating-effective-rules" id="things-to-consider-when-creating-effective-rules"></a>

1. **Allowing all traffic**

If you do not associate your subnet or NIC with a security group, all network traffic is allowed through the subnet or NIC, according to the default Azure security rules.

2. **Critical rule - Importance of allow rules**

If you have a subnet or NIC in your network security group, you must define an allow rule at each level. Otherwise, traffic is denied for any level that doesn't provide an allow rule definition

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

3. **Intra-subnet traffic**

You can prohibit intra-subnet traffic by defining a rule in the network security group to deny all inbound and outbound traffic.

By default, VMs in the same subnet communicate freely; by locking down intra-subnet traffic, virtual machines in the same subnet cannot talk to one another.

4. **Priority**

Security groups are processed in priority order.

Rules with low priority values have higher precedence

This allows you to add new rules without editing existing ones.

{% hint style="info" %}
Lower number = higher priority. A rule with priority 100 beats one with priority 200.
{% endhint %}

#### Viewing effective security rules

Use the 'effective security rules' link on each security group to verify which security rules are applied to your resources

Network watcher - provides a consolidated view of your infrastructure rules, including both security group rules and Azure Virtual Network Manager security admin rules

IP flow verify feature - evaluates traffic against both security group rules and any security admin rules that may be in effect.

### Application security group

ASG - group your virtual machines by workload, and then determine Network security groups based on your application security groups.

You join your VMs to an application security group, then use the application security group as a source or destination in network security group rules.

An example of an ASG would be WebServers or SQLservers, then you would have network security groups like below

* source: Internet, Destination: WebServers Port: 80,443
* source: Webservers, Destination: SQLServers Port: 1433

#### Things to consider

* IP address maintenance

IP addresses of servers can change; this will not affect your security rules as you are not specifying specific IP addresses.

* no subnets

You don't need to distribute your servers across specific subnets. Just like above, your subnets can change without affecting security rules.

* simplified rules

You don't need to create security rules for each virtual machine, thus consolidating multiple rules into fewer rule sets

* workload support

Application security groups provide logical arrangements for your resources. One can understand the purpose of the security group from the names that follow workload usage, e.g., webservers. _Organizing virtual machines into logical groups that reflect different business functions_

* service tags

They help minimize the complexity of frequent updates on network security rules.

While service tags simplify managing IP addresses for Azure services, ASGs group VMs and manage network security policies for those groups.

## Introduction to Azure Firewall

Azure Firewall is a cloud-based security service that protects your Azure virtual network resources from incoming and outgoing threats.&#x20;

Think of it as a security checkpoint that all traffic must pass through before being allowed in or out.

In most configurations, Azure Firewall is provisioned inside a virtual hub network.

Traffic from the Spoke virtual networks and the on-premises network traverses the firewall within the hub network.

All traffic from the internet is denied by default.

Azure firewall not only filters internet-based traffic, but internal traffic as well, such as spoke-to-spoke traffic, hybrid cloud traffic between your on-premises and Azure virtual network.

**The Three SKUs**

| SKU          | Target                                     | Key limitation                                   |
| ------------ | ------------------------------------------ | ------------------------------------------------ |
| **Basic**    | Small/medium businesses                    | Alert-only threat intel, fixed scale, \~250 Mbps |
| **Standard** | Most organisations                         | Full feature set, no TLS inspection              |
| **Premium**  | Regulated industries (finance, healthcare) | Adds TLS inspection, IDPS, full URL filtering    |

### Key features of Azure Firewall Standard

<table><thead><tr><th width="237.20001220703125">Feature</th><th>Description</th></tr></thead><tbody><tr><td>SNAT (Source Network Address Translation)</td><td>IP address of each source VM is translated to the static public IP of the firewall. To all external destination, your network traffic appears to come from a single public IP address. To internal destinations, SNAT is not needed.</td></tr><tr><td>DNAT (Destination Network Address Translation)</td><td>Traffic from external sources hits the public IP of the firewall, and is then translated to the private IP address of the destination resource in your virtual network e.g., <em>people visiting your website from the internet backed by an internal HTTP server e.g.,itscore.ctbto.org</em></td></tr><tr><td>Application rules</td><td>Limit outbound traffic to a list of FQDNs e.g sqlserver01.example.org, instead of using the IP</td></tr><tr><td>Network Rules</td><td>Rules for incoming and outgoing traffic based on IP, Port, Protocol</td></tr><tr><td>Threat intelligence</td><td>Filters traffic based on Microsoft threat intelligence, which defines known malicious IP addresses and domain names. You can configure it to alert only, or alert and deny</td></tr><tr><td>Stateful</td><td>Examines IP packets in context, not just individually e.g., to prevent a DDoS attack</td></tr><tr><td>Forced Tunnelling</td><td>Routes all outbound traffic to a specific network resource, not directly to the internet e.g., IDS, Web Proxy</td></tr><tr><td>Tag support</td><td>use of service tags for easier rule identification</td></tr><tr><td>Custom DNS</td><td>Allows you to configure Azure Firewall to use your own DNS server, while ensuring the firewall outbound dependencies are still resolved with Azure DNS.</td></tr><tr><td>Web Categories</td><td>The Web categories feature lets administrators allow or deny user access to web site categories such as gambling websites, social media websites, and others.</td></tr><tr><td>Monitoring</td><td>Azure Firewall logs all incoming and outgoing network traffic, and you can analyze the resulting logs using Azure Monitor, Power BI, Excel, and other tools.</td></tr><tr><td>DNS proxy</td><td>With DNS proxy enabled, Azure Firewall can process and forward DNS queries from a Virtual Network(s) to your desired DNS server</td></tr></tbody></table>

### Premium only features

<table><thead><tr><th width="256.4000244140625">Feature</th><th>Description</th></tr></thead><tbody><tr><td>TLS inspection</td><td>Decrypts outbound traffic, processes the data, then encrypts the data and sends it to the destination</td></tr><tr><td>IDPS</td><td>Intrusion detection and prevention system, detect, log, report and block</td></tr><tr><td>URL filtering</td><td>filtering based on an entire URL e.g https://contoso.com/a/c instead of www.contoso.com</td></tr><tr><td>Web Categories</td><td>Use more fine tuned web catgories in premium</td></tr></tbody></table>

### Azure Firewall Basic

Similar to Azure Firewall standard, with the following limitations

* Supports threat intel alert mode only
* Recommended for environments with an estimated throughput of 250 Mbps
* Uses a maximum of 2 VM instances as a backend to run Azure Firewall basic

### Azure Firewall Manager

Provides centralised management and configuration of multiple Azure Firewall Instances, enabling you to create one or more firewall policies and rapidly apply them to multiple firewalls.

#### Feature of Azure Firewall Manager

* centralised management
* manage multiple firewalls
* supports multiple network architectures&#x20;
  * _Hub Virtual network - Azure Virtual Networks_
  * _Secured Virtual Hub - Azure Virtual WAN Hubs_
    * _Azure virtual WAN hub_
      * Microsoft's managed networking service for large organisations — instead of you building and managing your own hub VNet with gateways and peerings, Microsoft provides a pre-built managed hub that handles routing, VPN, and ExpressRoute connectivity automatically. You just connect your spokes and on-premises sites to it.
    * _Secured Virtual Hub_
      * that managed Virtual WAN Hub + Azure Firewall added to it
* automated traffic routing - Network traffic is automatically routed to the firewall (when used with Azure Virtual WAN Hub only).
* Hierarchical policies - support parent-child firewall policies, child policies inherit all the rules and settings from their parent
* Support for 3rd Party security providers - allows integration of 3rd party solutions
* DDoS Protection plan - allows associations of DDOS protection plans to your virtual networks
* Manage WAF policies - centrally create and associate WAF policies for your application delivery platforms, including Azure Front Door and Azure Application Gateway.

|                        | Hub Virtual Network         | Secured Virtual Hub            |
| ---------------------- | --------------------------- | ------------------------------ |
| **Who builds the hub** | You                         | Microsoft (Virtual WAN)        |
| **Routing management** | You configure UDRs manually | Automated by Virtual WAN       |
| **Scale**              | Single region typically     | Global, multi-region by design |
| **Complexity**         | More manual work            | Simpler for large deployments  |
| **Azure Firewall**     | You deploy it yourself      | You add it to the managed hub  |

#### Firewall Policy

An Azure resource that contains one or more collections of NAT, network and application rules. It also contains custom DNS settings, threat intelligence settings and more

It makes the management of multiple firewall rules easier by grouping them into a firewall policy.

#### **NSG vs Azure Firewall — Quick Distinction**

|                            | NSG                        | Azure Firewall                                             |
| -------------------------- | -------------------------- | ---------------------------------------------------------- |
| **Scope**                  | Single subnet or NIC       | Entire hub network                                         |
| **Layer**                  | L3/L4 (IP, port, protocol) | L3–L7 (including FQDN, URL, TLS)                           |
| **Stateful**               | Yes                        | Yes                                                        |
| **Threat intelligence**    | No                         | Yes                                                        |
| **Centralised management** | No                         | Yes (Firewall Manager)                                     |
| **Cost**                   | Free                       | Significant — charged per deployment hour + data processed |

NSGs are your first line of subnet-level defence. Azure Firewall is the centralised, intelligent gateway for your whole network — typically used together, not instead of each other.

#### Network Architecture for Azure Firewall manager

Azure Firewall Manager only supports **two specific architecture types** for deploying and managing firewalls:

| Architecture            | What it is                                                              | Supported by Firewall Manager |
| ----------------------- | ----------------------------------------------------------------------- | ----------------------------- |
| **Hub virtual network** | A standard Azure VNet where Azure Firewall is deployed centrally        | ✅ Yes                         |
| **Secured virtual hub** | An Azure Virtual WAN Hub with Azure Firewall and policies applied to it | ✅ Yes                         |

### How Azure Firewall works

**2 Key IPs**

Any Azure firewall deployment has 2 key IPs

* a public IP address, to which all inbound traffic is sent
* a private IP address, to which all outbound traffic is sent

All traffic, inbound or outbound, goes through the firewall. By default, the firewall denies access to everything, and one has to configure conditions under which traffic is allowed through _firewall rules_.

Commonly, Azure firewall is deployed as a barrier between your Azure Virtual Network and the internet, and uses a _hub and spoke_ network topology.

* a virtual network that acts as a central connectivity point. This network is the hub virtual network,&#x20;
* one or more virtual networks that are peered to the hub. These peers are spoke virtual networks and are used to provision workload servers

```
                   On-premises
                        │
                        │ VPN/ExpressRoute
                        │
              ┌─────────▼─────────┐
              │     Hub VNet      │
              │                   │
              │  Azure Firewall   │
              │  VPN Gateway      │
              │  ExpressRoute GW  │
              │  DNS servers      │
              └──────┬────────────┘
          ┌──────────┼──────────┐
          │          │          │
    ┌─────▼───┐ ┌────▼────┐ ┌──▼──────┐
    │Spoke A  │ │Spoke B  │ │Spoke C  │
    │Dev VNet │ │Prod VNet│ │HR VNet  │
    └─────────┘ └─────────┘ └─────────┘
```

The hub is a central VNet that hosts everything that needs to be **shared across the whole organisation**: It contains

* Azure Firewall
* VPN or ExpressRoute gateway
* DNS servers
* Bastion host - one jump server to manage VMs across spokes
* Network Virtual Appliance
* any other resource serving all the spokes e.g load balancer

The hub never hosts actual workloads

Spokes are connected to the hub via _**VNet peering**_, a fast, low-latency private connection between VNets.

Spokes do not connect directly to one another; traffic between spokes goes through the hub (and therefore the firewall)

#### Steps for setting up an Azure Firewall Instance

1. Create a hub virtual network that includes a subnet for the firewall deployment
2. Create the spoke virtual networks, their networks, and servers
3. Peer the Hub and spoke networks
4. Deploy the firewall to the hub's subnet
5. For outbound traffic, create a default route that sends traffic from all subnets to the firewall's private IP address
6. Configure the firewall with rules to filter inbound and outbound traffic

#### Azure Firewall Rule Types

3 types of rules can be created

1. NAT - translate and filter inbound traffic based on your firewall's public IP address and specified port number
2. Application - filter traffic based on FQDN
3. Network - filter traffic based on IP, port and protocol

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

#### Evaluation of Firewall Rules

Rules are applied in priority order

* Rules based on threat intelligence have the highest priority and are processed first
* Followed by NAT rules
* Followed by Network rules
* Lastly, Application rules

Within each type, rules are processed according to the priority values you assign then when you create the rules, from lowest to highest value

```
1st — Threat intelligence rules  (always wins)
2nd — NAT rules
3rd — Network rules
4th — Application rules
Within each type: lowest priority number processed first
```

#### **Deployment Features**

Tools that make writing firewall rules easier, or shortcuts for rule configuration

<table><thead><tr><th width="166.79998779296875">Feature</th><th>Simply put</th></tr></thead><tbody><tr><td><strong>FQDN</strong></td><td>Domain name in an application rule — supports wildcards like <code>*.google.com</code> Useful because cloud service IPs change constantly — the domain name stays stable.</td></tr><tr><td><strong>FQDN tag</strong></td><td>Pre-built Microsoft bundles of domain names for their own services — e.g., "WindowsUpdate" covers all Windows Update URLs so you don't list them manually</td></tr><tr><td><strong>Service tag</strong></td><td>Pre-built Microsoft bundles of IP range bundles for Azure services — e.g., "AzureBackup" covers all Backup service IPs. Microsoft updates it automatically when IPs change.</td></tr><tr><td><strong>IP groups</strong></td><td>Your own custom reusable grouping of IP addresses — reuse across multiple rules e.g., IP group called <code>WebServers</code></td></tr><tr><td><strong>DNS proxy</strong></td><td>All client DNS requests go through the firewall before going to the DNS server - gives the firewall visibility into DNS queries</td></tr></tbody></table>

| If the exam mentions...                     | Answer involves...       |
| ------------------------------------------- | ------------------------ |
| Microsoft service URLs changing frequently  | FQDN tag                 |
| Azure service IP ranges changing frequently | Service tag              |
| Same IPs repeated across many rules         | IP group                 |
| DNS traffic not visible to firewall         | DNS proxy                |
| Allowing a specific website or domain       | FQDN in application rule |

#### When to use Azure Firewalls

| Scenario                                 | What it means                                                                   | Why Azure Firewall is the answer                                                                                                                                                                                                                                                                                             |
| ---------------------------------------- | ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Protect against infiltration**         | Malicious actors trying to access network resources uninvited                   | Azure Firewall uses stateful inspection of network packets to examine the context of requests. If a request came seemingly out of nowhere — like one sent by a would-be infiltrator — the firewall denies it.                                                                                                                |
| **Protect against user error**           | Users clicking malicious links in emails that lead to attacker-controlled sites | Azure Firewall prevents such attacks by using threat intelligence to deny access to known malicious domains and IP addresses.                                                                                                                                                                                                |
| **E-commerce / credit card payments**    | Businesses that must comply with PCI DSS regulations                            | PCI DSS requires a firewall configuration that restricts all inbound and outbound traffic from untrusted networks and hosts, and denies all traffic except protocols needed to process payment cards. Azure Firewall satisfies this requirement out of the box.                                                              |
| **Spoke-to-spoke connectivity**          | Two spoke VNets need to talk to each other but spokes can't connect directly    | Deploy Azure Firewall in the hub, then configure spoke VNets with UDRs (User Defined Routes) that route data through the firewall and on to the other spoke. The firewall acts as the controlled bridge between spokes. _Peering can be done between spokes directly, but this becomes unmanageable with increase in spokes_ |
| **Monitor inbound and outbound traffic** | Regulatory compliance, enforcing internet usage policies, troubleshooting       | Azure Firewall maintains diagnostic logs of four types of activity: application rules, network rules, threat intelligence, and DNS proxy. Logs can be examined in native JSON format, Azure Monitor, or Azure Firewall Workbook.                                                                                             |
| **Multiple firewalls across regions**    | Company spans multiple Azure regions, each needing its own firewall instance    | Azure Firewall Manager gives you a central management interface for every Azure Firewall instance across all your Azure regions and subscriptions. Changes to a policy are automatically propagated to all firewall instances.                                                                                               |
| **Hierarchical firewall policies**       | Large organisations needing company-wide base rules plus team-specific rules    | A single base firewall policy implements rules enforced company-wide. Local firewall policies inherit the base policy and add rules specific to a particular app, team, or service. Managed centrally via Azure Firewall Manager.                                                                                            |

#### When to use Azure Firewall Premium

| Scenario                                               | What it means                                                                                                              | Why Azure Firewall Premium is the answer                                                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Inspect outbound TLS encrypted traffic**             | HTTPS traffic is encrypted — Standard firewall cannot see inside it to check for threats                                   | Azure Firewall Premium TLS inspection decrypts outbound traffic, processes the data, then re-encrypts it and sends it to the destination. Azure Firewall does the required security functions on the decrypted content before forwarding. Standard cannot do this at all.                                                                                                                             |
| **Signature-based malicious traffic detection (IDPS)** | You need to detect and block known attack patterns, malware, botnets, and exploits at the network level                    | Azure Firewall Premium provides signature-based IDPS to allow rapid detection of attacks by looking for specific patterns such as byte sequences in network traffic or known malicious instruction sequences used by malware. It includes over 55,000 rules across more than 50 categories, with 20 to 40 new rules released each day. IDPS applies to inbound, outbound, and spoke-to-spoke traffic. |
| **Full URL filtering (not just domain)**               | You need to allow `www.contoso.com/allowed` but block `www.contoso.com/blocked` — Standard can only filter at domain level | Azure Firewall Premium can filter on an entire URL — for example `www.contoso.com/a/c` instead of just `www.contoso.com`. When HTTPS traffic is inspected, it uses TLS inspection to decrypt the traffic and extract the target URL to validate whether access is permitted.                                                                                                                          |
| **Fine-grained web category filtering**                | You want to block specific content categories more precisely than just by domain name                                      | As opposed to the Standard SKU which matches the category based on an FQDN, Premium matches the category according to the entire URL for both HTTP and HTTPS traffic. For example, intercepting an HTTPS request for `www.google.com/news` — Standard categorises it as Search Engine based on the domain, while Premium categorises it correctly as News based on the full URL.                      |
| **PCI DSS / regulated environments**                   | Your organisation is in finance, healthcare, or another regulated industry with strict compliance requirements             | The Premium SKU complies with Payment Card Industry Data Security Standard (PCI DSS) environment needs, and can seamlessly scale up to 30 Gbps with availability zone integration to support an SLA of 99.99 percent.                                                                                                                                                                                 |

**Standard vs Premium — One-Line Summary**

|                       | Standard                    | Premium                       |
| --------------------- | --------------------------- | ----------------------------- |
| **Sees inside HTTPS** | No                          | Yes — via TLS inspection      |
| **IDPS**              | No                          | Yes — 55,000+ signatures      |
| **URL filtering**     | Domain only (`contoso.com`) | Full URL (`contoso.com/path`) |
| **Web categories**    | FQDN-based (less precise)   | Full URL-based (more precise) |
| **Compliance**        | General use                 | PCI DSS, healthcare, finance  |

## Host your domain on Azure DNS

DNS uses a global directory hosted on servers around the world. Microsoft is a part of the network that provides a DNS service through Azure DNS

SOA (Start of Authority) and NS (Name server) records are created automatically when you create a DNS zone by using Azure DNS

Record sets - allows for multiple resources to be defined in a single record, e.g., round robin addresses

### What is Azure DNS

A service that allows you to host and manage your domains and related records by using a globally distributed name-server architecture

It allows you to manage all of your domains by using your existing Azure credentials

You can't use Azure DNS to register your domain; this has to be done on a 3rd party domain registrar.

It is built on the Azure Resource Manager service.

Just like a DNS service, it handles translating domain names into IP addresses.

#### Security Features of Azure DNS

* RBAC - Role-based access control - A permission system that controls _who_ can do _what_ to _which_ resources.
* Activity logs - An automatic audit trail of every change made to a resource — who did it, what they did, and when.
* Resource locking - A hard lock you put on a resource to prevent modification or deletion — overrides even admin permissions.

#### Private domains

Private DNS zones allow one to use custom domain names rather than Azure-provided names.

These zones provide name resolution for virtual machines (VMs) within a virtual network and between virtual networks without having to create a custom DNS solution

They support split-horizon DNS - the same domain name can exist in both private and public zones, resolving to the correct one based on where the request originates.

To publish a private DNS zone to your virtual network, you specify the list of virtual networks that are allowed to resolve records within the zone.

{% hint style="info" %}
**Private DNS Zones — Simply Explained**

**The Problem First**

When Azure creates a VM, it gives it an auto-generated name like:

```
vm1.internal.cloudapp.azure.com
```

That name is ugly, hard to remember, and you have no control over it. You can't change it to something meaningful like `webserver.mycompany.local`.

Also by default, VMs in **different VNets** cannot resolve each other's names at all.

***

**What Private DNS Solves**

A Private DNS Zone lets you create **your own domain name system that only exists inside your Azure VNets** — completely invisible to the public internet.

```
Public internet:  mycompany.local  →  doesn't exist, can't be found
Inside your VNet: mycompany.local  →  resolves perfectly to 10.0.1.4
```

You define the zone, you control the names, Azure handles the resolution.

***

**A Concrete Example**

You have three VMs in a VNet:

```
Without private DNS:
vm1 → 10.0.1.4   (no friendly name)
vm2 → 10.0.1.5   (no friendly name)
vm3 → 10.0.1.6   (no friendly name)
```

You create a private DNS zone called `mycompany.local` and link it to your VNet:

```
With private DNS:
webserver.mycompany.local  →  10.0.1.4
database.mycompany.local   →  10.0.1.5
appserver.mycompany.local  →  10.0.1.6
```

Now your VMs can talk to each other using friendly names instead of IP addresses. If an IP changes, you update the DNS record — your applications keep working without reconfiguration.

***

**The VNet Link — Important Detail**

A private DNS zone doesn't work automatically. You have to **link it to a VNet** to tell Azure which VNets are allowed to use it:

```
Private zone: mycompany.local
    └── linked to VNet-A  ✅ VMs here can resolve mycompany.local
    └── linked to VNet-B  ✅ VMs here can resolve mycompany.local
    
VNet-C (not linked)       ❌ cannot resolve mycompany.local
```

This is how you control which networks can see your internal naming.

***

**Autoregistration**

When you enable autoregistration on the VNet link, Azure **automatically creates DNS records** for every VM in that VNet:

```
VM created → DNS record automatically added  ✅
VM deleted → DNS record automatically removed ✅
```

No manual work needed — Azure maintains the records for you.

***

**Split-Horizon DNS**

This is a powerful feature — the **same domain name** can exist in both public and private zones, resolving differently depending on who asks:

```
Query from inside your VNet:
contoso.com  →  10.0.1.4  (private IP — internal server)

Query from the public internet:
contoso.com  →  20.10.20.10  (public IP — public-facing server)
```

Same domain name, two different answers, depending on the source of the request. Useful when you want internal users to hit internal servers directly, while external users hit a public endpoint.

***

**One-Line Summary**

Private DNS zones are your own internal phone book — only your linked VNets can use it, the internet has no idea it exists, and it lets you give meaningful names to your Azure resources instead of relying on auto-generated ones.
{% endhint %}

#### Alias record sets

Alias record sets can point to an Azure resource

The alias record set is supported in the following DNS record types:

* A
* AAAA
* CNAME

### Configure Azure DNS to host your domain

Task: deploy the wideworldimports.com domain by using Azure DNS.

**A. Configure a public DNS zone**

1. _**Create a DNS zone in Azure**_

You used a third-party domain-name registrar to register the wideworldimports.com domain. The domain doesn't point to your organization's website yet.

To host the domain name with Azure DNS, you first need to create a DNS zone for that domain. A DNS zone holds all the DNS entries for your domain.

SOA (Start of Authority) and NS (Name server) are created automatically on creating the DNS Zone.

2. _**Get your Azure DNS name servers**_

This information is obtained from the DNS zone -> Overview blade on the Azure portal

When you bought wideworldimports.com from GoDaddy (or Namecheap, etc.), the registrar pointed that domain at _their own_ name servers by default. The internet currently asks GoDaddy, _"Where is contoso.com?"_ and GoDaddy answers.

3. _**Update the domain registrar setting**_

You need to go into your registrar's control panel and **replace their name servers with Azure's generated name servers**:

Edit the NS record and NS details to match your Azure DNS name server details at the registrar's control panel.

Changing the NS details is called _domain delegation_.

4. _**Verify the delegation of domain name services**_

The next step is to verify that the delegated domain now points to the Azure DNS zone you created for the domain. This process can take as few as 10 minutes, but might take longer.

To verify the success of the domain delegation, query the start of authority (SOA) record. The SOA record is automatically created when the Azure DNS zone is set up. You can verify the SOA record using a tool like nslookup.

The SOA record represents your domain and becomes the reference point when other DNS servers are searching for your domain on the internet.

```bash
nslookup -type=SOA wideworldimports.com
```

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

5. _**Configure your custom DNS settings**_

Right now, the zone exists, but it only has the auto-generated SOA and NS records. Nothing else. If someone types `wideworldimports.com` in a browser, DNS does not know where to send them yet — you need to add records manually.

You can now add A and CNAME records for your web servers or load balancers, so that www.wideworldimports.com now points to a web server or a web server behind a load balancer

You cannot create a CNAME for the root domain itself i.e., no CNAME for wideworldimports.com

B. Configure a private DNS zone

Another type of DNS zone that you can configure and host in Azure is a private DNS zone.&#x20;

Private DNS zones aren't visible on the Internet, and don't require that you use a domain registrar.&#x20;

You can use private DNS zones to assign DNS names to virtual machines (VMs) in your Azure virtual networks.

1. Create a private DNS zone
2. Identify virtual networks that need name resolutions and note down their virtual network names
3. Link your virtual network to a private DNS zone by creating a virtual network link

### Dynamically resolve resource names by using alias records

When you need to point your root domain (`wideworldimports.com`) to an Azure load balancer, neither a CNAME nor a standard A record fully solves the problem.&#x20;

A CNAME is ruled out immediately because the DNS standard forbids its use at the apex domain — this is a hard restriction with no workaround.&#x20;

An A record can be used at the apex, but Azure load balancer IPs are dynamic and can change, meaning any IP you hardcode into an A record could break without warning and has no automatic way to track the resource.&#x20;

Since CNAME fails on the first requirement and A records fail on the second, neither can reliably link the root domain to a load balancer, which is exactly the gap the Alias record was designed to fill.

This is because&#x20;

* You can't use A records for IPs, as such resources often have dynamic IPs, which change often.
* You can't use CNAME because CNAMEs cannot be used for root domains

The solution is to use _alias records_

{% hint style="info" %}
The assumption here is that we want to associate the root domain with the load balancer, which in practice is not done often; sub-domains are used instead

e.g., normally `wideworldimports.com` will not be configured on the load balancer, but `www.wideworldimports.com`  can
{% endhint %}

#### Apex domain

The apex domain is your domain's highest level. In our case, that's wideworldimports.com.&#x20;

The apex domain is also sometimes referred to as the _zone apex_ or _root apex_.&#x20;

The **@** symbol often represents the apex domain in your DNS zone records.

#### Alias records

Azure alias records enable a zone apex domain to reference other Azure resources from the DNS zone. You don't need to create complex redirection policies. You can also use an Azure alias to route all traffic through Traffic Manager.

The Azure alias record can point to the following Azure resources:

* A Traffic Manager profile
* Azure Content Delivery Network endpoints
* A public IP resource
* A front-door profile

Alias records also provide support for load-balanced applications in the zone apex.

The alias record set supports the following DNS zone record types:

* **A**: The IPv4 domain name-mapping record.
* **AAAA**: The IPv6 domain name-mapping record.
* **CNAME**: The alias for your domain, which links to the A record.

**Uses for alias records**

The following are some of the advantages of using alias records:

* **Prevents dangling DNS records**: A dangling DNS record occurs when the DNS zone records aren't up to date with changes to IP addresses. Alias records prevent dangling references by tightly coupling the lifecycle of a DNS record with an Azure resource.
* **Updates DNS record set automatically when IP addresses change**: When the underlying IP address of a resource, service, or application is changed, the alias record ensures that any associated DNS records are automatically refreshed.
* **Hosts load-balanced applications at the zone apex**: Alias records allow for zone apex resource routing to Traffic Manager.
* **Points zone apex to Azure Content Delivery Network endpoints**: With alias records, you can now directly reference your Azure Content Delivery Network instance.

An alias record allows you to link the zone apex (wideworldimports.com) to a load balancer. It creates a link to the Azure resource rather than a direct IP-based connection. So, if the IP address of your load balancer changes, the zone apex record continues to work.

## Network Architecture summary

### Incoming traffic

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### Outgoing Traffic

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
