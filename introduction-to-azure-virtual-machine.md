# Introduction to Azure Virtual Machine

## Required resources for IaaS virtual machines

* Network
  * virtual network
  * subnets
  * network security groups
  * There is a limit to the number of NICs based on your VM's size
* VM name
  * Maximum of 64 characters for a Linux VM, 15 characters on a Windows VM
  * include (deveus-webvm01)
    * environment - dev, prod, qa
    * location - eastus(eus)
    * instance - e.g 01, 02
    * product for service - product, application, or service that the resource supports
    * role, e.g., SQL, web, messaging
* location
  * consider
    * Your users - place the VM in the region closest to your users
    * legal, compliance, or tax requirement
    * available configuration and features - some regions have limited features
    * price - regions have different prices
* size of VM
  * Based on workload, choose from a set of available VM sizes
    * general purpose - a balanced CPU-to-memory ratio
    * compute optimized - high CPU-to-memory ratio
    * memory optimized - high memory-to-CPU ratio
    * storage optimized - high disk throughput and IO, for databases
    * GPU - targeted for heavy graphics rendering and video editing
    * High Performance Compute - fastest and most powerful CPU virtual machines with optional high-throughput network interfaces.
  * can upgrade or downgrade a VM
  * VM size can be changed while the VM is running, as long as the new size is available on the cluster the VM is currently running on
  * changing a running VM size automatically reboots the machine to complete the request
* Disks/Storage
  * All Azure virtual machines have at least 2 virtual hard disks; the first stores the operating system, and the second is used as temporary storage
  * You should add more disks to store application data
  * five disk types
    * ultra disk - SSD, IO-intensive workloads, e.g., databases, can't be used as an OS disk
    * Premium SSD v2 - SSD, production and performance-sensitive workloads, can't be used as an OS disk
    * premium SSD - SSD, production and performance sensitive workloads, can be used as an OS disk
    * Standard SSD - SSD, web servers, lightly used enterprise servers, can be used as an OS disk
    * Standard HDD - HDD, Backup, noncritical, infrequent access, can be used as an OS disk
* Operating system
  * Azure bundles the cost of the OS license into the price
  * options
    * Use the Azure-provided OS images
    * Use an OS image from the Azure marketplace
    * Create your own image

### Pricing

When you create a virtual machine, you're also creating resources that support the virtual machine. These resources come with their own costs that should be considered.

There are two separate costs that the subscription is charged for every VM:&#x20;

* compute
  * Compute expenses are priced on a per-hour basis but billed on a per-minute basis e.g you are only charged for 55 minutes of usage if the VM is deployed for 55 minutes.
  * you are not charged for compute if you stop the VM and deallocate the VM
  * hourly price varies with VM size and the OS you select.
  * Two payment options
    * pay as you go
      * you pay for compute capacity by the second, with no long-term commitment or upfront payments.&#x20;
      * You're able to increase or decrease compute capacity on demand and start or stop at any time.
    * reserved virtual machine instances
      * &#x20;An advance purchase of a virtual machine for one or three years in a specified region.
      * The commitment is made up front, and in return, you get up to 72% price savings compared to pay-as-you-go pricing.&#x20;
      * **RIs** are flexible and can easily be exchanged or returned for an early termination fee.&#x20;
* storage
  * You're charged separately for the storage the VM uses.&#x20;
  * The status of the VM has no relation to the storage charges that are incurred.&#x20;
  * If the VM is stopped/deallocated and you aren’t billed for the running VM, you're still charged for the storage used by the disks.

## Creating and managing Azure Virtual Machines

### Resource manager templates

Assuming you want to create a copy of an existing VM, you can download the configuration of the VM, edit it, and use it to deploy a clone.

**Resource Manager templates** are JSON files that define the resources you need to deploy for your solution.

You can create a resource template for your VM. From the VM menu, under **Automation** select **Export template**.

It is possible to define a policy to prevent this action.

### Azure CLI

It's available for Linux, macOS, Windows, or in a browser using the Cloud Shell.

The Azure CLI can be used with other scripting languages, like Ruby and Python.

```
az vm create --resource-group TestResourceGroup --name test-wp1-eus-vm --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys
```

### Azure Powershell

ideal for one-off interactive tasks and/or the automation of repeated tasks.

Azure PowerShell is an optional add-on package that adds the Azure-specific commands (referred to as **cmdlets**).

```
New-AzVm -ResourceGroupName "TestResourceGroup" -Name "test-wp1-eus-vm" -Location "East US" -Image Debian11 -VirtualNetworkName "test-wp1-eus-network" -SubnetName "default" -SecurityGroupName "test-wp1-eus-nsg" -PublicIpAddressName "test-wp1-eus-pubip" -GenerateSshKey -SshKeyName myPSKey
    -OpenPorts 22,4022
```

### Terraform

Azure also has a Terraform provider, so you can easily use Terraform to create and manage your VMs.&#x20;

Terraform enables the definition, preview, and deployment of cloud infrastructure.&#x20;

Using Terraform, you create configuration files using HCL syntax.&#x20;

The HCL syntax allows you to specify the cloud provider - such as Azure - and the elements that make up your cloud infrastructure. After you create your configuration files, you create an execution plan that allows you to preview your infrastructure changes before they're deployed. Once you verify the changes, you apply the execution plan to deploy the infrastructure.

### Programmatic APIs

When it comes to more complex scenarios, where the creation and management of VMs form part of a larger application with complex logic, APIs can be used.

You can interact with every type of resource in Azure programmatically.

### Azure REST API

The Azure REST API provides developers with operations categorized by resource and the ability to create and manage VMs.&#x20;

Operations are exposed as URIs with corresponding HTTP methods (`GET`, `PUT`, `POST`, `DELETE`, and `PATCH`) and a corresponding response.

The Azure Compute APIs give you programmatic access to virtual machines and their supporting resources.

### Azure Client SDK

Even though the REST API is platform and language-agnostic, most often developers look toward a higher level of abstraction.&#x20;

The Azure Client SDK encapsulates the Azure REST API, making it much easier for developers to interact with Azure.

The Azure Client SDKs are available for various languages and frameworks, including .NET-based languages such as C#, Java, Node.js, PHP, Python, Ruby, and Go.

### Azure VM extensions

Azure VM extensions are small applications that enable you to configure and _automate_ tasks on Azure VMs after initial deployment. For example, you want to configure and install more software on your virtual machine after the initial deployment, automatically.

### Azure automation services

Azure Automation enables you to integrate services that allow you to automate frequent, time-consuming, and error-prone management tasks with ease. These services include process automation, configuration management, and update management.

* **Process Automation**. Process automation enables you to set up watcher tasks that can respond to events that may occur in your datacenter.
* **Configuration Management**.  You can use _Microsoft Endpoint Configuration Manager_ to manage your company's PC, servers, and mobile devices. You can extend this support to your Azure VMs with Configuration Manager.
* **Update Management**. Use this service to manage updates and patches for your VMs. You enable update management for a VM directly from your **Azure Automation** account. You can also enable update management for a single virtual machine from the virtual machine pane in the portal.

### Auto-shutdown

A feature in Azure that allows you to automatically shut down your VMs on a schedule. A useful feature that allows you to save on costs by ensuring that VMs are not running when they are not needed.

Available on the operations blade of the VM resource

## Manage the availability of your Azure VMs <a href="#module-unit-title" id="module-unit-title"></a>

Azure provides a service that helps you improve VM availability, streamlines VM management tasks, and keeps your VM data backed up and safe.

Availability is the percentage of time a service is available for use.

### Availability zones

An Availability Zone is a physically separate zone within an Azure region.&#x20;

There are three Availability Zones per supported Azure region.

Each Availability Zone has a distinct power source, network, and cooling.&#x20;

By designing your solutions to use replicated VMs in zones, you can protect your apps and data from the loss of a data center.&#x20;

If one zone is compromised, then replicated apps and data are instantly available in another zone.

### Virtual machine scale sets

Create and manage a group of load-balanced VMs.&#x20;

The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.&#x20;

Virtual machines in a scale set can also be deployed into multiple availability zones, a single availability zone, or regionally.

### Load balancer

Combine the Azure Load Balancer with an availability zone or availability set to get the most application resiliency.&#x20;

The Azure Load Balancer distributes traffic between multiple virtual machines

### Azure Storage redundancy

Azure Storage always stores multiple copies of your data so that it's protected from planned and unplanned events, including transient hardware failures, network or power outages, and massive natural disasters.&#x20;

Redundancy ensures that your storage account meets its availability and durability targets even in the face of failures.

When deciding which redundancy option is best for your scenario, consider the tradeoffs between lower costs and higher availability.&#x20;

The factors that help determine which redundancy option you should choose include:

* How your data is replicated in the primary region
* Whether your data is replicated to a second region that is geographically distant from the primary region, to protect against _regional disasters_
* Whether your application _requires read access_ to the replicated data in the secondary region if the primary region becomes unavailable for any reason

### Failover across locations

You can also replicate your infrastructure across sites to handle regional failover.&#x20;

**Azure Site Recovery** replicates workloads from a primary site to a secondary location.&#x20;

If an outage happens at your primary site, you can fail over to a secondary location.&#x20;

You can then fail back to the primary location after it's up and running again. Azure Site Recovery is about _replication of virtual or physical machines_; it keeps your workloads available in an outage.

There are at least two significant business advantages to Site Recovery:

* Site Recovery enables the use of Azure as a destination for recovery, thus eliminating the cost and complexity of maintaining a secondary physical data center.
* Site Recovery makes it incredibly simple to test failovers for recovery drills without impacting production environments. This feature makes it easy to test your planned or unplanned failovers.&#x20;

Azure Site Recovery works with Azure resources, or Hyper-V, VMware, and physical servers in your on-premises infrastructure. It can be a key part of your organization’s business continuity and disaster recovery (BCDR) strategy by orchestrating the replication, failover, and recovery of workloads and applications if the primary location fails.

## Backup your virtual machines

**Azure Backup** is a _backup as a service_ offering that protects physical or virtual machines no matter where they reside: on-premises or in the cloud.

### Advantages of using Azure Backup <a href="#advantages-of-using-azure-backup" id="advantages-of-using-azure-backup"></a>

Azure Backup was designed to work in tandem with other Azure services and provides several distinct benefits.

* **Automatic storage management**. Azure Backup automatically allocates and manages backup storage and uses a pay-as-you-use model. You only pay for what you use.
* **Unlimited scaling**. Azure Backup uses the power and scalability of Azure to deliver high availability.
* **Multiple storage options**. Azure Backup offers locally redundant storage, where all copies of the data exist within the same region, and geo-redundant storage, where your data is replicated to a secondary region.
* **Unlimited data transfer**. Azure Backup doesn't limit the amount of inbound or outbound data you transfer. Azure Backup also doesn't charge for the data that is transferred.
* **Data encryption**. Data encryption allows for secure transmission and storage of your data in Azure.
* **Application-consistent backup**. An application-consistent backup means that a recovery point has all the required data to restore the backup copy.&#x20;
* **Long-term retention**. Azure doesn't limit the length of time you keep the backup data.

### Using Azure Backup <a href="#use-azure-backup" id="use-azure-backup"></a>

Azure Backup uses several components that you download and deploy to each computer you want to back up. The component that you deploy depends on what you want to protect.

* Azure Backup agent
* System Center Data Protection Manager
* Azure Backup Server
* Azure Backup VM extension

Azure Backup uses a Recovery Services vault for storing the backup data. Azure Storage blobs back up a vault, making it an efficient and economical long-term storage medium. With the vault in place, you can select the machines to back up, and define a backup policy (when snapshots are taken and for how long they’re stored).

## Configure virtual machine availability

Azure Administrators need to be able to adjust their virtual machine resources to match changing demands.&#x20;

At the same time, they need to keep their virtual machine configuration consistent to ensure application stability.&#x20;

### Plan for maintenance and downtime

An availability plan for Azure virtual machines needs to include strategies for unplanned hardware maintenance, unexpected downtime, and planned maintenance.

* An **unplanned hardware maintenance** event occurs when the Azure platform predicts that the hardware or any platform component associated to a physical machine is about to fail. When the platform predicts a failure, it issues an unplanned hardware maintenance event. Azure uses Live Migration technology to migrate your virtual machines from the failing hardware to a healthy physical machine. Live Migration is a virtual machine preserving operation that only pauses the virtual machine for a short time, but performance might be reduced before or after the event.
* **Unexpected downtime** occurs when the hardware or the physical infrastructure for your virtual machine fails unexpectedly. Unexpected downtime can include local network failures, local disk failures, or other rack level failures. When detected, the Azure platform automatically migrates (heals) your virtual machine to a healthy physical machine in the same datacenter. During the healing procedure, virtual machines experience downtime (reboot) and in some cases loss of the temporary drive.
* **Planned maintenance** events are periodic updates made by Microsoft to the underlying Azure platform to improve overall reliability, performance, and security of the platform infrastructure that your virtual machines run on.

### Create availability sets

An availability set is a logical feature you can use to ensure a group of related virtual machines are deployed together.&#x20;

The grouping helps to prevent a single point of failure from affecting all of your machines.&#x20;

The grouping ensures that not all of the machines are upgraded at the same time during a host operating system upgrade in the data center.

Characteristics of availability sets.

* All virtual machines in an availability set should perform an identical set of functionalities.
* All virtual machines in an availability set should have the same software installed.
* Azure ensures that virtual machines in an availability set run across multiple physical servers, compute racks, storage units, and network switches.
*   You can create a virtual machine and an availability set at the same time.

    A virtual machine can only be added to an availability set when the virtual machine is created. To change the availability set for a virtual machine, you need to delete and then recreate the virtual machine.
* You can build availability sets by using the Azure portal, Azure Resource Manager (ARM) templates, scripting, or API tools.

#### Things to consider when using availability sets <a href="#things-to-consider-when-using-availability-sets" id="things-to-consider-when-using-availability-sets"></a>

* **Consider redundancy**. To achieve redundancy in your configuration, place multiple virtual machines in an availability set.
* **Consider the separation of application tiers**. Each application tier exercised in your configuration should be located in a separate availability set. The separation helps to mitigate a single point of failure on all machines.
* **Consider load balancing**. For high availability and network performance, create a load-balanced availability set by using Azure Load Balancer. A load balancer distributes incoming traffic across working instances of services that are defined in your load-balanced availability set.
* **Consider managed disks**. You can use Azure managed disks with your Azure virtual machines in availability sets for block-level storage.

### Review update domains and fault domains

Azure Virtual Machine Availability Sets implement two node concepts to help Azure maintain high availability and fault tolerance when deploying and upgrading applications: _update domains_ and _fault domains_. Each virtual machine in an availability set is placed in one update domain and one fault domain.

#### Update domains <a href="#things-to-know-about-update-domains" id="things-to-know-about-update-domains"></a>

An update domain is a group of nodes that are upgraded together during the process of a service upgrade (or _roll-out_).&#x20;

An update domain allows Azure to perform incremental or rolling upgrades across a deployment. Here are some other characteristics of update domains.

* Each update domain contains a set of virtual machines and associated physical hardware that can be updated and rebooted at the same time.
* During planned maintenance, only one update domain is rebooted at a time.
* You can specify between 1 and 20 update domains when creating an availability set. If you don't specify a value, Azure defaults to _five update domains._
* The update domain count is immutable after creation; to change it, you must delete and recreate the availability set.

#### Fault domains <a href="#things-to-know-about-fault-domains" id="things-to-know-about-fault-domains"></a>

A fault domain is a group of nodes that represent a physical unit of failure. Think of a fault domain as nodes that belong to the same physical rack.

* A fault domain defines a group of virtual machines that share a common set of hardware (or _switches_) that share a single point of failure. An example is a server rack serviced by a set of power or networking switches.
* Two fault domains work together to mitigate against hardware failures, network outages, power interruptions, or software updates.

<figure><img src=".gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

### Availability zones <a href="#module-unit-title" id="module-unit-title"></a>

Availability zones are a high-availability offering that protects your applications and data from datacenter failures.&#x20;

You can use availability zones to build high-availability into your application architecture by colocating your compute, storage, networking, and data resources within a zone and replicating in other zones.

characteristics of availability zones.

* Availability zones are unique physical locations within an Azure region.
* Each zone is made up of one or more datacenters that are equipped with independent power, cooling, and networking.
* To ensure resiliency, there's a minimum of three separate zones in all enabled regions.
* The physical separation of availability zones within a region protects applications and data from datacenter failures.
* Zone-redundant services replicate your applications and data across availability zones to protect against single points of failure.

| **Zonal services**          | Azure _zonal_ services pin each resource to a specific zone.                                        | <p>- Azure virtual machines<br>- Azure managed disks</p>             |
| --------------------------- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| **Zone-redundant services** | For Azure services that are zone-redundant, the platform replicates automatically across all zones. | <p>- Azure Storage that's zone-redundant<br>- Azure SQL Database</p> |

Standard IP addresses can be configured as zone-redundant (recommended for high availability), zonal (pinned to a specific zone), or non-zonal (regional), depending on your deployment choice.

### Compare vertical and horizontal scaling

#### vertical scaling <a href="#things-to-know-about-vertical-scaling" id="things-to-know-about-vertical-scaling"></a>

Vertical scaling, also known as _scale up and scale down_, involves increasing or decreasing the virtual machine **size** in response to a workload. Vertical scaling makes a virtual machine more (scale up) or less (scale down) powerful.

<figure><img src=".gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

vertical scaling is limited by maximum hardware capacity

#### horizontal scaling <a href="#things-to-know-about-horizontal-scaling" id="things-to-know-about-horizontal-scaling"></a>

Horizontal scaling, also referred to as _scale out and scale in_, is used to adjust the **number** of virtual machines in your configuration to support the changing workload. When you implement horizontal scaling, there's an increase (scale out) or decrease (scale in) in the number of virtual machine instances.

<figure><img src=".gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

When operating in the cloud, horizontal scaling is more flexible. A horizontal scaling implementation allows you to run potentially thousands of virtual machines to manage changes in workload and throughput.

### Implement Azure Virtual Machine Scale Sets <a href="#module-unit-title" id="module-unit-title"></a>

Azure Virtual Machine Scale Sets are an Azure Compute resource that you can use to deploy and manage a set of **identical** virtual machines. When you implement Virtual Machine Scale Sets and configure all your virtual machines in the same way, you gain true _autoscaling_.&#x20;

Virtual Machine Scale Sets automatically increase the number of your virtual machine instances as application demand increases, and reduce the number of machine instances as demand decreases.

As workloads increase, more virtual machine instances can be added. As workloads decrease, virtual machines instances can be removed. The process of adding and removing machines can be manual or automated, or a combination of both.

characteristics of Azure Virtual Machine Scale Sets.

* Virtual Machine Scale Sets support the use of Azure Load Balancer for basic layer-4 traffic distribution, and Azure Application Gateway for more advanced layer-7 traffic distribution and TLS/SSL termination.
* You can use Virtual Machine Scale Sets to run multiple instances of your application. If one of the virtual machine instances has a problem, customers continue to access your application through another virtual machine instance with minimal interruption.
* There are two types of orchestration modes available for Azure virtual machine scale sets: Uniform and Flexible. In **Uniform orchestration mode**, all virtual machine instances are created from the same base operating system image and configuration. In **Flexible orchestration mode**, VMs can use different images, sizes, or configurations within the same scale set. The orchestration mode must be chosen when the scale set is created.
* Customer demand for your application might change throughout the day or week. To meet customer demand, Virtual Machine Scale Sets implements autoscaling to automatically increase and decrease the number of virtual machines.

#### Creating an availability set

There are several settings to configure to create an Azure Virtual Machine Scale Sets implementation.

* **Orchestration mode**: Choose how the scale set manages the virtual machines. Flexible orchestration is the default and recommended mode for new deployments. For most new workloads, accept the Flexible default unless you have a specific requirement for identical instances.
* **Image**: Choose the base operating system or application for the VM.
* **VM Architecture**: Azure provides a choice of x64 or Arm64-based virtual machines to run your applications. x64-based VMs provide the most software compatibility. Arm64-based VMs provide up to 50% better price-performance than comparable x64 VMs.
* **Size**: Select a VM size to support the workload that you want to run. The size that you choose then determines factors such as processing power, memory, and storage capacity.&#x20;
* **Spreading algorithm**: The spreading algorithm determines how VMs in the scale set are balanced across fault domains. With max spreading, VMs are spread across as many fault domains as possible in each zone. With fixed spreading, VMs are always spread across exactly five fault domains. In the case where fewer than five fault domains are available, a scale set using "Max spreading" completes, while a scale set using "Fixed spreading" fails. For this reason, Microsoft recommends using **Max spreading** for your implementation.

### Implement autoscale <a href="#module-unit-title" id="module-unit-title"></a>

An Azure Virtual Machine Scale Sets implementation can automatically increase or decrease the number of virtual machine instances that run your application. This process is known as _autoscaling_.

Autoscaling minimizes the number of unnecessary virtual machine instances that run your application when demand is low. Your customers continue to receive an acceptable level of performance as demand grows and more virtual machine instances are automatically added.&#x20;

Considerations about autoscaling.

* **Consider automatic adjusted capacity**. You can create autoscaling rules to define the acceptable performance for a positive customer experience. When the defined thresholds are met, the autoscale rules act to adjust the capacity of your Virtual Machine Scale Sets implementation.
* **Consider scale out**. If your application demand increases, you can configure autoscale rules to increase the number of virtual machine instances in your implementation.
* **Consider scale in**. On an evening or weekend, your application demand might decrease. If the decreased load is consistent over a period of time, you can configure autoscale rules to decrease the number of virtual machine instances in your implementation.
* **Consider scheduled events**. You can implement autoscaling and schedule events to automatically increase or decrease the capacity of your implementation at fixed times.
* **Consider overhead**. Using Azure Virtual Machine Scale Sets with autoscaling reduces your management overhead to monitor and optimize the performance of your application.

#### Configure autoscale <a href="#module-unit-title" id="module-unit-title"></a>

You can enable manual or autoscaling for Azure Virtual Machine Scale Sets.

For optimal performance, you should define a minimum, maximum, and default number of virtual machine instances to use.

**Scaling mode**

* **Manually update the capacity**: Maintain a fixed instances count. Set the **Instance count** to the number of virtual machines in the scale set (0 - 1000). Configure the **Scale-in policy** which is the order virtual machines are selected for deletion. For example, you could balance across zones and then delete the virtual machine with the highest instance ID.
* **Autoscaling**: Scaling based on a CPU metric, or any schedule.

**Scaling condition**

* **Default instance count.** The initial number of virtual machines deployed in this scale set (0-1000).
* **Instance limit.** The minimum instance count you want this condition to scale down to. The maximum instance count you want this condition to scale up to.
* **Scale out.** The CPU usage percentage threshold for triggering the scale-out autoscale rule. The number of instances to scale out by.
* **Scale in.** The CPU usage percentage threshold for triggering the scale-in autoscale rule. The number of instances to scale in by.
* **Query duration**: This duration is the time the Autoscale engine looks back for the metric usage average. This look back is to allow your metric to stabilize.
* **Schedule**: Specify the start and end dates. You can also repeat the schedule on specific days.

## Implement Desired State Configuration

**Desired State Configuration (DSC)** is a configuration management approach that ensures your infrastructure remains in a specified state and doesn't deviate over time.&#x20;

**DSC** helps prevent configuration drift, maintain compliance, and enforce security standards across your environment.

### Configuration drift <a href="#module-unit-title" id="module-unit-title"></a>

**Configuration drift** is the process of a set of resources changing over time from their original deployment state. It can occur because of changes made manually by people or automatically by processes or programs.

Eventually, an environment can become a **snowflake**

### Snowflake configuration? <a href="#what-is-a-snowflake-configuration" id="what-is-a-snowflake-configuration"></a>

A **snowflake** is a unique configuration that cannot be reproduced automatically and is typically a result of configuration drift. With snowflakes, infrastructure administration and maintenance invariably involve manual processes, which can be hard to track and prone to human error

### Impact of configuration drift <a href="#impact-of-configuration-drift" id="impact-of-configuration-drift"></a>

The more an environment drifts from its original state, the more likely it is for an application to encounter issues.&#x20;

The greater the degree of **configuration drift**, the longer it takes to troubleshoot and rectify problems.

**Configuration drift** can also introduce security vulnerabilities into your environment. Examples include:

* **Open ports:** Ports might be opened that should be kept closed, exposing your systems to unauthorized access.
* **Inconsistent patching:** Updates and security patches might not be applied across environments consistently, leaving some systems vulnerable.
* **Non-compliant software:** Software might be installed that doesn't meet compliance requirements, violating security policies.

### Solutions for managing configuration drift <a href="#solutions-for-managing-configuration-drift" id="solutions-for-managing-configuration-drift"></a>

#### Windows PowerShell Desired State Configuration <a href="#windows-powershell-desired-state-configuration" id="windows-powershell-desired-state-configuration"></a>

Windows PowerShell Desired State Configuration (DSC) is a management platform in PowerShell that enables you to manage and enforce resource configurations.

PowerShell DSC enables you to manage, deploy, and enforce configurations for physical or virtual machines, including Windows and Linux systems.

#### Azure Policy

Azure Policy allows you to enforce policies and compliance standards for Azure resources. Use Azure Policy to prevent configuration changes that would cause drift from your desired state.&#x20;

#### Third-party solutions <a href="#third-party-solutions" id="third-party-solutions"></a>

There are also other third-party configuration management solutions that you can integrate with Azure, such as Chef, Puppet, and Ansible.

### DSC components <a href="#dsc-components" id="dsc-components"></a>

**DSC** consists of three primary components:

#### Configurations <a href="#configurations" id="configurations"></a>

Configurations are declarative PowerShell scripts that define and configure instances of resources. Upon running the configuration, DSC (and the resources being called by the configuration) will apply the configuration, ensuring that the system exists in the state laid out by the configuration.

DSC configurations are idempotent: The Local Configuration Manager (LCM) will ensure that machines are configured in whatever state the configuration declares, regardless of how many times the configuration is applied.

#### Resources <a href="#resources" id="resources"></a>

Resources contain the code that puts and keeps the target of a configuration in the specified state. Resources are in PowerShell modules and can be written to a model as generic as a file or a Windows process or as specific as a Microsoft Internet Information Services (IIS) server or a VM running in Azure.

#### Local Configuration Manager (LCM) <a href="#local-configuration-manager-lcm" id="local-configuration-manager-lcm"></a>

The Local Configuration Manager (LCM) runs on the nodes or machines you wish to configure. It's the engine by which DSC facilitates the interaction between resources and configurations. The LCM regularly polls the system using the control flow implemented by resources to maintain the state defined by a configuration. If the system is out of state, the LCM calls the code in resources to apply the required configuration.

### DSC implementation methods <a href="#dsc-implementation-methods" id="dsc-implementation-methods"></a>

There are two methods of implementing **DSC:**

#### Push mode <a href="#push-mode" id="push-mode"></a>

Push mode: A user actively applies a configuration to a target node and pushes out the configuration. This method is useful for testing and immediate configuration of the application.

#### Pull mode <a href="#pull-mode" id="pull-mode"></a>

Pull mode: Pull clients are automatically configured to get their desired state configurations from a remote pull service. This remote pull service is provided by a pull server that acts as central control and manager for the configurations, ensures that nodes conform to the desired state, and reports on their compliance status.

The pull server can be set up as an SMB-based pull server or an HTTPS-based server. HTTPS-based pull server uses the Open Data Protocol (OData) with the OData Web service to communicate using REST APIs. This is the preferred model, as it can be centrally managed and controlled.&#x20;

## Azure Automation State Configuration (DSC) <a href="#module-unit-title" id="module-unit-title"></a>

Azure Automation State Configuration (DSC) is an Azure cloud-based implementation of PowerShell DSC, available as part of Azure Automation.&#x20;

Azure Automation State Configuration allows you to write, manage, and compile PowerShell DSC configurations, import DSC Resources, and assign configurations to target nodes, all in the cloud.

### Why use Azure Automation State Configuration? <a href="#why-use-azure-automation-state-configuration" id="why-use-azure-automation-state-configuration"></a>

The following outlines some of the reasons why you would consider using Azure Automation State Configuration:

#### Built-in pull server <a href="#built-in-pull-server" id="built-in-pull-server"></a>

Azure Automation State Configuration provides a DSC pull server similar to the Windows Feature DSC service so that target nodes automatically receive configurations, conform to the desired state, and report back on their compliance. The built-in pull server in Azure Automation eliminates the need to set up and maintain your own pull server.

#### Centralized management <a href="#centralized-management" id="centralized-management"></a>

You can manage all your DSC configurations, resources, and target nodes from the Azure portal or PowerShell. This centralized management simplifies configuration deployment and monitoring across your entire infrastructure.

#### Integration with Log Analytics <a href="#integration-with-log-analytics" id="integration-with-log-analytics"></a>

Nodes managed with Azure Automation State Configuration send detailed reporting status data to the built-in pull server. You can configure Azure Automation State Configuration to send this data to your Log Analytics workspace for advanced monitoring, alerting, and compliance reporting.

### How Azure Automation State Configuration works <a href="#how-azure-automation-state-configuration-works" id="how-azure-automation-state-configuration-works"></a>

#### Step 1: Create a PowerShell configuration script <a href="#step-1-create-a-powershell-configuration-script" id="step-1-create-a-powershell-configuration-script"></a>

Create a PowerShell script with the configuration element. This script defines the desired state for your target nodes using DSC syntax.

#### Step 2: Upload and compile the configuration <a href="#step-2-upload-and-compile-the-configuration" id="step-2-upload-and-compile-the-configuration"></a>

Upload the script to Azure Automation and compile the script into a _MOF file_. The MOF (Managed Object Format) file is transferred to the DSC pull server, provided as part of the Azure Automation service.

#### Step 3: Assign configurations to nodes <a href="#step-3-assign-configurations-to-nodes" id="step-3-assign-configurations-to-nodes"></a>

Define the nodes that will use the configuration, and then apply the configuration. The nodes automatically pull the configuration from the DSC pull server and apply it to maintain the desired state.

### DSC configuration elements <a href="#dsc-configuration-elements" id="dsc-configuration-elements"></a>

```
configuration LabConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure = 'Present'
                Name = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }
    }
```

#### Configuration block <a href="#configuration-block" id="configuration-block"></a>

The Configuration block is the outermost script block. In this case, the name of the configuration is LabConfig. Notice the curly brackets to define the block.

Within a **Configuration** block, you can do almost anything that you normally could in a **PowerShell** function. This includes:

* **Variables:** Use variables to store values and make configurations more dynamic.
* **Parameters:** Pass parameters to configurations to make them reusable across different environments.
* **Conditional logic:** Use if/else statements to apply configurations based on conditions.
* **Loops:** Use loops to apply configurations to multiple resources.

```
    Configuration MyDscConfiguration
    {
        param
        (
            [string[]]$ComputerName='localhost'
        )

        Node $ComputerName
        {}
```

#### Node block <a href="#node-block" id="node-block"></a>

There can be one or more Node blocks. The Node block defines the nodes (computers and VMs) that you're configuring. In this example, the node targets a computer called WebServer. You could also call it localhost and use it locally on any server.

#### Resource blocks <a href="#resource-blocks" id="resource-blocks"></a>

There can be one or more resource blocks. The resource block is where the configuration sets the properties for the resources. In this case, there's one resource block called WindowsFeature. Notice the parameters that are defined.&#x20;

## Implement DSC and Linux automation on Azure <a href="#module-unit-title" id="module-unit-title"></a>

DSC for Linux enables you to manage Linux systems using the same Desired State Configuration approach used for Windows systems.

**Supported Linux OS**

* CentOS: 6, 7, and 8 (x64).
* Debian GNU/Linux: 8, 9, and 10 (x64).
* Oracle Linux: 6 and 7 (x64).
* Red Hat Enterprise Linux Server: 6, 7, and 8 (x64).
* SUSE Linux Enterprise Server: 12 and 15 (x64).
* Ubuntu Server: 14.04 LTS, 16.04 LTS, 18.04 LTS, and 20.04 LTS

**DSC for Linux includes built-in resources for managing Linux systems:**

* nxFile: Manage files and directories.
* nxScript: Run scripts to configure resources.
* nxUser: Manage user accounts.
* nxGroup: Manage user groups.
* nxService: Manage services using systemd, upstart, or System V.
* nxPackage: Manage packages using apt, yum, or zypper.

**To implement DSC for Linux on Azure, you can use Azure Automation State Configuration to:**

* Create DSC configurations for Linux systems using PowerShell.
* Upload and compile configurations in Azure Automation.
* Onboard Linux VMs to Azure Automation State Configuration.
* Apply configurations to Linux nodes through the built-in pull server.

This approach provides centralized management and monitoring of both Windows and Linux systems from a single platform.

