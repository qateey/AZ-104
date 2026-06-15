# Administer Inter-site Connectivity

## Configure Azure Virtual Network peering

Azure Virtual Network peering lets you connect virtual networks in the same or different regions.

After networks are peered,

* they operate as a single network for connectivity purposes;
* The individual virtual networks are still managed as separate resources

2 types of Azure Virtual Network peering

* regional
  * connects Azure virtual networks in the same region
* global
  * connects Azure virtual networks that exist in different regions.
  * mostly used for data replication, database failover

To connect virtual networks, you can use

* VPN gateway
* virtual network peering

### Benefits

* Private network connections
  * When you peer virtual networks, the network traffic between peered virtual networks is private. No internet, gateways or encryption is required between the virtual networks
  * strong performance - use of Azure infrastructure enables use of low-latency, high-bandwidth connections between resources in different virtual networks
  * Simplified network communication - Peering enables resources in one virtual network to communicate with resources in another virtual network
  * seamless data transfer - you can use peered networks to transfer data across subscriptions, deployment models, and regions
  * no resource disruption - no service downtime for resources is required to create the peer or after the peer is created

### Limitations

* non-overlapping address spaces - peered networks must have non-overlapping IP address ranges, otherwise the peering creation fails
* Address space modification restrictions - to change the IP address range of a peered network, you'll have to delete the peering first
* Load balancer considerations - you cannot use a basic load balancer in peered virtual networks that are peered across regions. For cross-region connections, use the standard load balancer
* DNS resolution - built-in DNS resolution does not work across peered networks, use private DNS zones or custom DNS servers for cross-vnet name resolution

### Gateway transit and connectivity

Virtual network peering alone handles Azure-to-Azure traffic, no gateway is needed

But, if any virtual networks need to reach resources outside of Azure (on-prem, remote clients), a VPN gateway is required

And rather than paying for one VPN gateway per spoke, you can put one in a hub and share it via gateway transit

### Four peering settings in Azure

The first 2 options are used for normal peering

* Allow 'vnet-2' to access 'v-net1'
  * **traffic to remote virtual network**
  * controls whether traffic flows from this VNet to the remote VNet
* Allow 'vnet-2' to receive forwarded traffic from 'vnet-1'
  * **Traffic forwarded from remote virtual network**.&#x20;
  * Controls whether forwarded (non-originating) traffic is accepted from the peered VNet.
* Allow gateway or route server in 'vnet-2' to forward traffic to 'v-net1'
  * **Virtual network gateway or Route Server**.&#x20;
  * Enables gateway transit. Lets peered VNets use this VNet's VPN Gateway or Azure Route Server.
* Enable 'vnet-2' to use 'v-net1' remote gateway or route server
  * **Remote virtual network gateway or Route Server**.
  * Enables this VNet to use the remote VNet's VPN Gateway or Route Server.

### Key facts about VPN gateway + peering

* A virtual network can only have one VPN gateway
* Gateway transit works for both [regional ](administer-inter-site-connectivity.md#configure-azure-virtual-network-peering)and [global ](administer-inter-site-connectivity.md#configure-azure-virtual-network-peering)virtual network peering
* When you allow VPN gateway transit, the virtual network can communicate to resources outside the peering
  * use a site-to-site VPN to connect to an on-premises network
  * use vnet-to-vnet connection to another virtual network
    * Azure internal (as opposed to accessing external resources which is the primary use of a gateway)
    * It's an older way of connecting VNets before VNet peering existed, using gateways on both ends with an encrypted tunnel between them.&#x20;
    * You'd use it today mainly for VNets in different Azure tenants or subscriptions where peering isn't available, or for some specific compliance requirement.
  * use a point-to-site VPN to connect to a client
  * gateway transit allows peered virtual networks to share the gateway and access external resources, without the need to deploy a VPN gateway in the peered virtual network
  * Network security groups can be used to block or allow access to other virtual networks or subnets

{% hint style="info" %}
**Differentiate gateway transit and gateway**

**Gateway** is the resource itself — an Azure VPN Gateway deployed into a gateway subnet inside a VNet. It's the thing that terminates VPN tunnels, handles encryption, and manages connections to external networks. It costs money, takes \~30 mins to deploy, and you can only have one per VNet.

***

**Gateway transit** is a _setting_ on a peering link — it's just a permission that says "this peered VNet is allowed to use my gateway." No extra resource is created. It's a checkbox.

***

So the relationship is:

* The **hub VNet** has an actual **gateway** (the resource)
* The **peering link** between hub and spoke has **gateway transit enabled** (the permission)
* The **spoke VNet** then uses the hub's gateway as if it were its own — without deploying one itself
{% endhint %}

### Creating a Virtual Network Peering

#### Pre-requisites

* an account with the network contributor role or a custom role with the right permissions
* two virtual networks
* The second virtual network in the peering is referred to as the _remote network_
* After peering, virtual machines can communicate within the peered network

#### Procedure

bidirectional

#### Peering status

Two peering status conditions are

* initiated
  * When you create the initial peering from network A to network B, the peering status for network A is 'Initiated'
* connected
  * When you now create the peering from network B to network A, the peering status for both virtual networks becomes' ConnectedThe&#x20;

peering configuration is bidirectional, not unidirectional

### Extend peering with user-defined routes(UDR) and service chaining

Virtual network peering is nontransitive.&#x20;

The communication capabilities in a peering are available to only the virtual networks and resources in the peering.

Suppose you have three virtual networks: A, B, and C. You establish virtual network peering between networks A and B, and also between networks B and C. You don't set up peering between networks A and C. The virtual network peering capabilities that you set up between networks B and C don't automatically enable peering communication capabilities between networks A and C.

#### Ways of extending peering

There are a few ways to extend the capabilities of your peering for resources and virtual networks outside your peering network

* hub and spoke network
  * Hub hosts a Network Virtual Appliance or VPN gateway; all spokes peer with the hub and traffic flows through those central resources
* user-defined routes (UDR)
  * Manually defined routes where the next hop is a Virtual Machine or VPN gateway in a peered VNet
* service chaining
  * UDRs configured to point traffic from one virtual network to a virtual appliance or gateway in a peered VNet
* Azure virtual network manager
  * Centrally manages peering topologies at scale without manual per-VNet config (automates peering creation)

{% hint style="info" %}
#### The relationship between UDR and service chaining

The docs list them separately but they're really the same thing at two levels of abstraction:

* A **UDR** is the technical mechanism — a route table entry saying "for this destination, next hop is this IP"
* **Service chaining** is the _pattern_ — using UDRs to deliberately steer traffic through a specific appliance (firewall, NVA, proxy) before it reaches its destination

Service chaining is what you're _doing_; UDRs are _how_ you do it.

***

#### Why this matters in practice

In a hub-and-spoke design, spoke A is peered with the hub. The hub has an NVA. You want spoke A to reach **network C**, which has no peering with anyone.

Without a UDR, traffic from spoke A destined for C has no route — peering only gives you what's directly peered, and C isn't.

With a UDR in spoke A, you define: "for traffic destined to C's address range, next hop is the NVA in the hub." The NVA then forwards it to C, which could be another VNet, an on-premises subnet, or anything else reachable from the hub.

That is service chaining — you chained spoke A → hub NVA → C using a UDR, extending spoke A's reach beyond its own peering without creating a direct peering to C.
{% endhint %}

## Introduction to Azure VPN Gateway

## Introduction to Azure Network Watcher
