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

