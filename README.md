# Module 2

## Describe the core architectural components of Azure

### What is Azure?

Azure is Microsoft's cloud computing platform, with a continually expanding _set of cloud services_ that help you meet current and future IT challenges

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Azure accounts

{% hint style="info" %}
an identity in Microsoft Entra ID or in a directory that Microsoft Entra ID trusts.
{% endhint %}

An Azure account is an account you use to access and manage services on Microsoft Azure. It is linked to a Microsoft account, such as Outlook, Hotmail, or a work/school account

Once an Azure account has been created, an Azure subscription is created.

You can have multiple Azure subscriptions on an Azure account, e.g., for production, dev, and testing, or per department or per resource

A subscription is a billing and resource management container; it separates billing and access control

Each subscription contains one or more resource groups

Each resource belongs to exactly one resource group.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Azure physical infrastructure

Azure's core architectural components can be broken down into two main groupings:&#x20;

* the physical infrastructure&#x20;
* and the management infrastructure

#### Datacenters

Facilities with servers in racks, with dedicated power, cooling and networking.

Datacenters are grouped into regions and availability zones

#### Regions

For example, East US

A geographical region containing one or more nearby data centers connected by a low-latency connection

{% hint style="info" %}
Some services are only available in some regions, some global services, e.g.,g Entra ID do not require one to select a region
{% endhint %}

#### Availability Zones

A collection of one or more physical data centers within a single Azure Region.&#x20;

They are isolation boundaries; if one zone goes down, the others keep working

Zones are connected via high-speed private fiber-optic networks.

{% hint style="info" %}
To ensure resiliency, a minimum of 3 AZs are available in all availability zone-enabled regions.

Not all regions support AZs
{% endhint %}

You can protect your workloads by spreading them across availability zones within a region.&#x20;

There is a cost to duplicating workloads and transferring data between zones.

**Service categories for availability zones**

_Zonal services_ - services pinned to a specific availability zone, e.g., a VM, IP address

_Zone-redundant services_ - services that are automatically replicated across zones by the platform, e.g., zone-redundant storage, SQL databases

_Non-regional services (global services)_ - services for which you don't select a region, e.g.,&#x20;

* _Entra ID_
* _Azure DNS_
* _Azure Traffic Manager_.&#x20;

They are distributed globally across Microsoft's entire infrastructure.&#x20;

Since they are regionless, a region or availability zone failure does not affect them

{% hint style="info" %}
"zone" and "availability zone" (AZ) are the same thing. Microsoft just shortens it in places.

* "zone-wide outage" = an entire AZ going down
* "zone-redundant" = replicated across multiple AZs
* "zonal service" = pinned to a specific AZ
{% endhint %}

#### Region pairs

Most Azure regions are paired with another region that is at least 300 miles (482 km) away.&#x20;

Services are replicated across regions, so that services automaticall fail over to a region pair. e.g., the pair for West US is East US.

This helps reduce the likelihood of interruption due to natural disasters, civil unrest, power outages or physical network outages affecting an entire region.

Region pairs do not have to be in the same country. For example, Brazil south's pair is south central US.

Not all services automatically replicate or fail over; in such cases, recovery must be configured by the customer.

**Advantages of region pairs**

* in case of an extensive Azure outage, restoration is prioritized in one region in each pair to hasten service recovery
* Data residency - data stays within the same geography as its pair for compliance reasons, except for Brazil Southbrazil south
* Planned Azure updates are rolled out to paired regions one at a time

#### Sovereign regions

Instances of Azure that are isolated from the main instance of Azure for compliance or legal reasons e.g US DoD Central

Such regios include additional compliance certifications or are maintained by sovereign countries e.g china

#### **Hierarchy Summary**

<table><thead><tr><th width="242.800048828125">Level</th><th>What it is</th></tr></thead><tbody><tr><td>Datacenter</td><td>Physical building with servers, power, cooling, networking</td></tr><tr><td>Availability Zone</td><td>One or more datacenters within a region — independent failure boundary</td></tr><tr><td>Region</td><td>Geographic cluster of datacenters/zones connected by low-latency network</td></tr><tr><td>Region Pair</td><td>Two regions 300+ miles apart for disaster recovery</td></tr><tr><td>Geography</td><td>Broad area (US, Europe, Asia) containing multiple regions</td></tr></tbody></table>

{% hint style="info" %}
**availability zones** (within a region, protect against datacenter failure)

**region pairs** (across regions, protects against regional disasters)

Not all services replicate automatically
{% endhint %}

### Azure management infrastructure

includes

* Azure resources
* Resource groups
* Subscriptions&#x20;
* accounts

#### Azure resources

Anything you create, provision or deploy is a resource. e.g a VM, virtual network, database

#### Azure resource groups

Groupings of resources

**Properties**

* Each resource must belong to exactly one resource group
* You can move resources between resource groups
* Resource groups cannot be nested
* Resource groups cannot be renamed after creation
* Deleting a resource group deletes everything within it
* Access permissions apply to all resources in a resource group

#### Azure subscriptions

A unit of management, billing, and <mark style="color:purple;">scale</mark>.

A subscription can be (subscription boundaries)

* a billing boundary - separate invoices for separate subscriptions
* an access control boundary - separate access management policies, access rules and spending limits for separate subscriptions
* deployment boundary - a subscription has a limit on the number of resources you can deploy (subscription limits or quotas); you can add more subscriptions to <mark style="color:violet;">scale</mark> out

An account can have multiple subscriptions, but only one is required (an account must have a subscription)

A subscription links to an account.

#### Azure management groups

Similar to AWS organizations

Management groups group multiple subscriptions together, for which governance conditions e.g., access policies or compliance rules, apply

All subscriptions in a group inherit conditions set at the management group.

Management groups can be nested six levels deep.

Each management group and subscription can only have one tenant.

Every Microsoft Entra tenant has a single top-level Tenant root group.

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Azure Policy Initiatives

_Azure policy_ is a service that allows one to create, assign, and manage governance policies that enforce rules and effects over resources to ensure compliance with your IT governance standards or service level agreements.

These policies are described in JSON format and are known as _policy definitions_.

Azure policy initiatives are a collection of Azure policy definitions that are grouped together for a specific goal or purpose.

Azure policy initiatives

* allow centralized control and enforcement of configurations across resources
* aid in customizing deployment to meet established regulatory compliance frameworks and government requirements
* aid in tracking compliance for multiple resources

## Cloud adoption framework for Azure

The cloud adoption framework offers _comprehensive technical guidance_ for Azure.

it includes&#x20;

* best practices
* documentation
* and tools

that Microsoft employees, partners, and customers contribute to formulating and implementing effective business and technology strategies for the cloud.

Within the framework, _Azure policy_ is used to help govern your cloud environment and workloads - thus playing a significant role in cloud governance.

_Cloud governance_ refers to the management of cloud usage in an organization.

The cloud adoption framework - govern methodology, offers a systematic framework for setting up and improving cloud governance in Azure.

Comprehensive cloud governance

* oversees all aspects of cloud use
* minimizes various risks, such as security, compliance, data-related concerns, resource management
* optimizes cloud operations
* is a continuous process, requiring ongoing monitoring, evaluation, and adjustments to adapt to evolving technologies, risk, and compliance requirements

### steps for cloud governance

Cloud adoption framework - govern methodology includes five steps

1. (Build) Build a dedicated _cloud governance team_, responsible for defining, maintaining, and reporting on the progress of cloud governance policies
2. (Risk Analysis) Conduct a thorough _risk assessment_ unique to your organization, addressing all risk categories, including regulatory, compliance, security, operations, costs, data, resource management, and AI-related risks.
3. (Document) _Document cloud governance policies,_ including acceptable cloud usage, rules, and guidelines for mitigating identified risks
4. (Enforce) _Enforce cloud governance policies_ - use automated and manual tools to ensure adherence to the cloud governance policies; these tools can be used to set guardrails and monitor configurations.&#x20;
5. (Monitor) _Monitor cloud governance -_ Regularly monitor cloud usage and the governance teams to ensure compliance with established cloud governance policies

Steps 2 - 5 are done continuously/iteratively

### Considerations for defining a cloud governance policy

The three key considerations are

* Business risk - document evolving business risks and the business's risk tolerance based on data classification and criticality
* policy and compliance - convert risk decisions into policy statements
* process - establish a process to monitor violations and adherence

### Disciplines of cloud governance

5 core disciplines of cloud governance

* Cost management - evaluates and monitors costs, including controlling IT expenditures, adjusting resources according to demand to derive greater value from your investment.
* security baseline - applying a security baseline to all adoption efforts.
* resource consistency - ensure consistency in resource configuration, enforce practices for onboarding, recovery, and discoverability
* identity baseline - ensure a baseline for identity and access by applying role definitions and assignments
* deployment acceleration - accelerate deployment of policies through centralization, consistency, and standardization across deployment templates

### Cloud governance with Azure policies

Azure's primary governance tool is Azure policy

Azure policy

* facilitates the governance of all resources, including current and future resources
* helps enforce organizational standards to ensure compliance at scale by establishing guardrails across resources
* allows for centralized management, allowing you to track compliance
* contains a compliance dashboard that presents an aggregated view of the environment's overall state, as well as a drilldown to detailed information. All compliance data is aggregated into a singular repository.
* ensures consistent adherence to compliance standards and prevents misconfigurations.
* offers bulk remediation for existing resources and automatic remediation for new resources.
* evaluates your resources and highlights non-compliant resources. It can also prevent non-compliant resources from being created.&#x20;
* It also evaluates all your VMs, including VMs that were created before the policy was created.
* comes with built-in initiative and policy definitions for some resources like storage, compute, networking, security center, and monitoring.
* In some cases, it can automatically remediate non-compliant resources and configurations. You can mark a resource as an exception if you do not want Azure Policy to automatically update/remediate it.
* it integrates with Azure DevOps by applying CI/CD policies to your applications.

## Azure policy design principles

### Hierarchy for governance

Azure provides 4 levels of management for governance

* management groups
* subscriptions
* resource groups
* resources

You can apply management settings at any level; lower levels inherit settings from upper levels.

### Azure Resource Manager (ARM)

ARM is the deployment and management service for Azure.

It provides a management layer that allows you to create, update, and delete resources in your Azure account.

All Azure operations go through it; portal, PowerShell, CLI, REST APIs, and SDKs all use the same ARM API for uniform results.

Azure operations are split into&#x20;

* _**The control plane**_

Manages resources in your subscriptions.

ARM manages all control plane operations in Azure, which include template-based deployments, SDKs, Azure RBAC, auditing, monitoring, and tagging (Azure resource providers).

RBAC is evaluated before Azure policy; if you lack permissions, Azure policy is never reached.

Azure policy operates in the control plane and is integrated with ARM.

* _**The data plane**_

Allows you to access the capabilities provided by instances of specific resources.

It is where actual data operations occur.

Data plane operations involve direct interaction with the data stored in a resource, e.g., when you engage with a storage account to upload or download files.

Data operations are not limited to the ARM REST API; requests are made directly to the service endpoints, bypassing ARM and directly managing data through its resource provider.

Examples of service endpoints include (Azure storage, Azure Key Vault, SQL database)

Service-specific access controls, such as RBAC or ACLs, often manage data plane permissions.

The service responds with data or the result of the data operation, ensuring that the requester has the correct permissions.

### Operation flows of Azure Resource Manager

As you deploy resources, ARM understands when to _create new_ resources and when to _update existing_ resources

ARM includes 2 resources for handling Azure requests

* Greenfield (policy first)

A policy exists before the resource. When you create or update a resource, ARM evaluates it against existing policies in real-time.

When creating a new resource, the full resource definition of the new resource is sent to ARM.

* Request hits **ARM**
* ARM checks **RBAC** — do you have permission to create this?
* ARM checks **Azure Policy** — does this resource comply with existing policies? e.g. "all resources must be in West Europe"
* If both pass → resource is **created**
* If Policy fails → request is **denied**, resource is never created

For updates, ARM merges the delta (the changes you are sending to ARM), with the current resource state to get the full target state, then evaluates that against policy.

* Brownfield (resource first)

Resources already exist when a new policy is _assigned_. Policy evaluation happens via a compliance scan, which runs automatically every 24 hours or can be triggered manually.

Existing non-compliant resources are flagged, but not deleted or changed.

Future non-compliant resource creation is blocked.

* Request hits **ARM** with the delta
* ARM **reads the current state** of the resource from Azure
* ARM **merges** delta + current state = full target state
* Policy evaluates the **full target state**
* If compliant → update is applied
* If not → request is denied

## Azure policy resources

