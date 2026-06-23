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
