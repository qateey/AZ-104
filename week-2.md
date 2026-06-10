# Week 2

### Describe the core architectural components of Azure

#### What is Azure?

Azure is Microsoft's cloud computing platform, with a continually expanding _set of cloud services_ that help you meet current and future IT challenges

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

#### Azure accounts

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

#### Azure physical infrastructure

Azure's core architectural components can be broken down into two main groupings:

* the physical infrastructure
* and the management infrastructure

**Datacenters**

Facilities with servers in racks, with dedicated power, cooling and networking.

Datacenters are grouped into regions and availability zones

**Regions**

For example, East US

A geographical region containing one or more nearby data centers connected by a low-latency connection

{% hint style="info" %}
Some services are only available in some regions, some global services, e.g.,g Entra ID do not require one to select a region
{% endhint %}

**Availability Zones**

A collection of one or more physical data centers within a single Azure Region.

They are isolation boundaries; if one zone goes down, the others keep working

Zones are connected via high-speed private fiber-optic networks.

{% hint style="info" %}
To ensure resiliency, a minimum of 3 AZs are available in all availability zone-enabled regions.

Not all regions support AZs
{% endhint %}

You can protect your workloads by spreading them across availability zones within a region.

There is a cost to duplicating workloads and transferring data between zones.

**Service categories for availability zones**

_Zonal services_ - services pinned to a specific availability zone, e.g., a VM, IP address

_Zone-redundant services_ - services that are automatically replicated across zones by the platform, e.g., zone-redundant storage, SQL databases

_Non-regional services (global services)_ - services for which you don't select a region, e.g.,

* _Entra ID_
* _Azure DNS_
* _Azure Traffic Manager_.

They are distributed globally across Microsoft's entire infrastructure.

Since they are regionless, a region or availability zone failure does not affect them

{% hint style="info" %}
"zone" and "availability zone" (AZ) are the same thing. Microsoft just shortens it in places.

* "zone-wide outage" = an entire AZ going down
* "zone-redundant" = replicated across multiple AZs
* "zonal service" = pinned to a specific AZ
{% endhint %}

**Region pairs**

Most Azure regions are paired with another region that is at least 300 miles (482 km) away.

Services are replicated across regions, so that services automaticall fail over to a region pair. e.g., the pair for West US is East US.

This helps reduce the likelihood of interruption due to natural disasters, civil unrest, power outages or physical network outages affecting an entire region.

Region pairs do not have to be in the same country. For example, Brazil south's pair is south central US.

Not all services automatically replicate or fail over; in such cases, recovery must be configured by the customer.

**Advantages of region pairs**

* in case of an extensive Azure outage, restoration is prioritized in one region in each pair to hasten service recovery
* Data residency - data stays within the same geography as its pair for compliance reasons, except for Brazil Southbrazil south
* Planned Azure updates are rolled out to paired regions one at a time

**Sovereign regions**

Instances of Azure that are isolated from the main instance of Azure for compliance or legal reasons e.g US DoD Central

Such regios include additional compliance certifications or are maintained by sovereign countries e.g china

**Hierarchy Summary**

<table><thead><tr><th width="242.800048828125">Level</th><th>What it is</th></tr></thead><tbody><tr><td>Datacenter</td><td>Physical building with servers, power, cooling, networking</td></tr><tr><td>Availability Zone</td><td>One or more datacenters within a region — independent failure boundary</td></tr><tr><td>Region</td><td>Geographic cluster of datacenters/zones connected by low-latency network</td></tr><tr><td>Region Pair</td><td>Two regions 300+ miles apart for disaster recovery</td></tr><tr><td>Geography</td><td>Broad area (US, Europe, Asia) containing multiple regions</td></tr></tbody></table>

{% hint style="info" %}
**availability zones** (within a region, protect against datacenter failure)

**region pairs** (across regions, protects against regional disasters)

Not all services replicate automatically
{% endhint %}

#### Azure management infrastructure

includes

* Azure resources
* Resource groups
* Subscriptions
* accounts

**Azure resources**

Anything you create, provision or deploy is a resource. e.g a VM, virtual network, database

**Azure resource groups**

Groupings of resources

**Properties**

* Each resource must belong to exactly one resource group
* You can move resources between resource groups
* Resource groups cannot be nested
* Resource groups cannot be renamed after creation
* Deleting a resource group deletes everything within it
* Access permissions apply to all resources in a resource group

**Azure subscriptions**

A unit of management, billing, and <mark style="color:purple;">scale</mark>.

A subscription can be (subscription boundaries)

* a billing boundary - separate invoices for separate subscriptions
* an access control boundary - separate access management policies, access rules and spending limits for separate subscriptions
* deployment boundary - a subscription has a limit on the number of resources you can deploy (subscription limits or quotas); you can add more subscriptions to <mark style="color:violet;">scale</mark> out

An account can have multiple subscriptions, but only one is required (an account must have a subscription)

A subscription links to an account.

**Azure management groups**

Similar to AWS organizations

Management groups group multiple subscriptions together, for which governance conditions e.g., access policies or compliance rules, apply

All subscriptions in a group inherit conditions set at the management group.

Management groups can be nested six levels deep.

Each management group and subscription can only have one tenant.

Every Microsoft Entra tenant has a single top-level Tenant root group.

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Azure Policy Initiatives

_Azure policy_ is a service that allows one to create, assign, and manage governance policies that enforce rules and effects over resources to ensure compliance with your IT governance standards or service level agreements.

These policies are described in JSON format and are known as _policy definitions_.

Azure policy initiatives are a collection of Azure policy definitions that are grouped together for a specific goal or purpose.

Azure policy initiatives

* allow centralized control and enforcement of configurations across resources
* aid in customizing deployment to meet established regulatory compliance frameworks and government requirements
* aid in tracking compliance for multiple resources

### Cloud adoption framework for Azure

The cloud adoption framework offers _comprehensive technical guidance_ for Azure.

it includes

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

#### steps for cloud governance

Cloud adoption framework - govern methodology includes five steps

1. (Build) Build a dedicated _cloud governance team_, responsible for defining, maintaining, and reporting on the progress of cloud governance policies
2. (Risk Analysis) Conduct a thorough _risk assessment_ unique to your organization, addressing all risk categories, including regulatory, compliance, security, operations, costs, data, resource management, and AI-related risks.
3. (Document) _Document cloud governance policies,_ including acceptable cloud usage, rules, and guidelines for mitigating identified risks
4. (Enforce) _Enforce cloud governance policies_ - use automated and manual tools to ensure adherence to the cloud governance policies; these tools can be used to set guardrails and monitor configurations.
5. (Monitor) _Monitor cloud governance -_ Regularly monitor cloud usage and the governance teams to ensure compliance with established cloud governance policies

Steps 2 - 5 are done continuously/iteratively

#### Considerations for defining a cloud governance policy

The three key considerations are

* Business risk - document evolving business risks and the business's risk tolerance based on data classification and criticality
* policy and compliance - convert risk decisions into policy statements
* process - establish a process to monitor violations and adherence

#### Disciplines of cloud governance

5 core disciplines of cloud governance

* Cost management - evaluates and monitors costs, including controlling IT expenditures, adjusting resources according to demand to derive greater value from your investment.
* security baseline - applying a security baseline to all adoption efforts.
* resource consistency - ensure consistency in resource configuration, enforce practices for onboarding, recovery, and discoverability
* identity baseline - ensure a baseline for identity and access by applying role definitions and assignments
* deployment acceleration - accelerate deployment of policies through centralization, consistency, and standardization across deployment templates

#### Cloud governance with Azure policies

Azure's primary governance tool is Azure policy

Azure policy

* facilitates the governance of all resources, including current and future resources
* helps enforce organizational standards to ensure compliance at scale by establishing guardrails across resources
* allows for centralized management, allowing you to track compliance
* contains a compliance dashboard that presents an aggregated view of the environment's overall state, as well as a drilldown to detailed information. All compliance data is aggregated into a singular repository.
* ensures consistent adherence to compliance standards and prevents misconfigurations.
* offers bulk remediation for existing resources and automatic remediation for new resources.
* evaluates your resources and highlights non-compliant resources. It can also prevent non-compliant resources from being created.
* It also evaluates all your VMs, including VMs that were created before the policy was created.
* comes with built-in initiative and policy definitions for some resources like storage, compute, networking, security center, and monitoring.
* In some cases, it can automatically remediate non-compliant resources and configurations. You can mark a resource as an exception if you do not want Azure Policy to automatically update/remediate it.
* it integrates with Azure DevOps by applying CI/CD policies to your applications.

### Azure policy design principles

#### Hierarchy for governance

Azure provides 4 levels of management for governance

* management groups
* subscriptions
* resource groups
* resources

You can apply management settings at any level; lower levels inherit settings from upper levels.

#### Azure Resource Manager (ARM)

ARM is the deployment and management service for Azure.

It provides a management layer that allows you to create, update, and delete resources in your Azure account.

All Azure operations go through it; portal, PowerShell, CLI, REST APIs, and SDKs all use the same ARM API for uniform results.

Azure operations are split into

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

#### Operation flows of Azure Resource Manager

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

### Azure policy resources

There are 6 policy resources in Azure

**a) Definitions**

Define policy effect and logic

describes a resource compliant condition and the effect to take if the condition is met.

Definitions are saved or deployed to a _definition location_ (management group or subscription)

two types

* built-in

Generated and provided by Azure resource providers, available by default

a group of built-in definitions is a built-in initiative

* custom

Written by a policy user

a group of custom definitions is a custom initiative

**b) Initiatives (policy sets)**

A group of multiple definitions

An initiative groups multiple policy definitions into a single item to simplify assignment and management.

initiatives are saved or deployed to a _definition location_ (management group or subscription)

**c) Assignments**

Policy assignments define which resources are evaluated by a policy definition or initiative.

they are applied to a specific scope (management group, subscription, resource group) and support several optional properties including

* the resource scope and policy definitions
* optional resource selectors for gradual rollout
* optional overrides to changes a policy's effect without modifying the definition
* enforcementMode can be disabled for 'what-if' scenarios
* optional _excluded_ scopes
* noncompliance messages
* parameters can be assigned values
* deployifnotexists - If you have a policy with the deployIfNotExists effect type, a managed identity can be assigned (system-assigned or user-assigned) to turn on remediation actions.

policies and initiatives are assigned to a specific scope (management group, subscription or resource group)

> **enforcementMode** is particularly useful — you can disable it to simulate policy effects without actually enforcing them, equivalent to audit mode but set at assignment level rather than changing the definition itself.

**d) Attestations**

Attestations are used to _manually set the compliance state of resources_ targeted by manual policies - where automated evaluation isn't possible and a human must assert compliance.

Each applicable resource requires one attestation per manual policy assignment.

**e) Exemptions**

Exemptions exclude a resource or resource hierarchy from policy evaluation.

two exemption categories exist

* Mitigated

the exemption is granted because the policy intent is met through another method.

* Waiver

The exemption is granted because the noncompliance state of the resource is temporarily accepted.

**f) Remediations**

Remediation tasks bring non-compliant resources into compliance for policies with _modify_ or _deployifnotExists_ effects.

Newly created or updated resources that fall under such policies are automatically remediated.

existing non-compliant resources require a remediation task to be triggered.

**Quick Reference**

<table><thead><tr><th width="253.70172119140625">Resource</th><th>Purpose</th></tr></thead><tbody><tr><td>Definition</td><td>The rule — what to check and what to do if it fails</td></tr><tr><td>Initiative</td><td>A bundle of definitions treated as one unit</td></tr><tr><td>Assignment</td><td>Attaches a definition/initiative to a scope</td></tr><tr><td>Exemption</td><td>Excludes a resource from evaluation (mitigated or waiver)</td></tr><tr><td>Attestation</td><td>Manual compliance declaration for manual policies</td></tr><tr><td>Remediation</td><td>Fixes existing non-compliant resources</td></tr></tbody></table>

{% hint style="info" %}
Know the difference between **exclusions** (set at assignment time, resource never evaluated) and **exemptions** (set after assignment, resource evaluated but excluded from enforcement). Also remember that remediation only applies to **modify** and **deployIfNotExists** effects — not Deny or Audit policies.
{% endhint %}

**deployifnotexists**

**A concrete example**

Policy: _"Every VM must have the Log Analytics agent installed."_

Without deployIfNotExists:

* VM is created without the agent
* Policy flags it as non-compliant
* An admin has to manually fix it

With deployIfNotExists:

* VM is created without the agent
* Policy detects the agent is missing
* Policy **automatically deploys the agent** to the VM

**deployIfNotExists vs modify**

These two are often confused:

<table><thead><tr><th width="184.4034423828125">Effect</th><th>What it does</th></tr></thead><tbody><tr><td><code>deployIfNotExists</code></td><td>Deploys a <strong>separate child resource</strong> if it doesn't exist (e.g. install an extension onto a VM)</td></tr><tr><td><code>modify</code></td><td>Changes a <strong>property on the existing resource</strong> itself (e.g. add a tag, change a setting)</td></tr></tbody></table>

### Azure Policy definitions

## Azure Policy Definitions

A policy - describes resource compliance conditions and the action or effects that take place if those conditions are met

A policy consists of two parts

* a condition - that compares a resource property field or value, accessed by using aliases to a required value
* an effect - what happens when the policy rule is evaluated to match the condition

### Anatomy of a Policy definition (JSON)

* displayName - name of the policy
* description - a description of the policy
* policyType - read-only atribute, can be \*\* builtin - microsoft provided \*\* custom - customer created \*\* static - regulatory compliance with Microsoft ownership
* mode - determines what gets evaluated
* version (optional)
* metadata - stores information about the policy definition \*\* version \*\* category - determines under which category on the Azure portal the definition falls under \*\* preview - true/false flag indicating if the policy definition is in review \*\* deprecated - true/false flag indicating if the policy definition is in deprecated \*\* portalReview - determines if the parameters require a review in the portal. \*\* all - evaluates all resource types \*\* Indexed - evaluates only resources that support tags and location
* parameters - reusable data types for passing values at assignment time
* policyRule - if then block: if is the condition, then is the effect

### Logical operators

#### Logical operators supported in the if block

* not - inverts the result of the condition
* allOf - requires all conditions to be true - similar to logical AND
* anyOf - requires one or more conditions to be true - similar to logical OR. These can be nested to create complex evaluation logics

#### Conditions - if statements

Properties like fields, values, or counts can be evaluated within a condition

* fields - conditions that evaluate resource property using aliases, e.g., Name, fullName, kind, type, location, ID, identity.type, tags, tags\['tagName'], property aliases
* value - conditions that evaluate a specific value against criteria
* count - a condition that counts how many array members meet criteria using the current() function

### Policy-specific functions

Beyond ARM template functions, Azure policy adds its own functions. functions can be used to introduce extra logic into a policy rule. They are resolved in a policy rule of a policy definition and in the parameter values that are assigned to the policy definitions in an initiative examples: addDays(dateTime, numberOfDaysToAdd), policy(), current(indexname),ipRangeContains(range, targetRange),requestContext().apiVersion,Field(fieldName)

#### Effect Types (then blocks)

defines what takes place when a condition is matched. More than one effect can be valid for a given policy definition. Examples: deny, disabled, append, modify, denyAction, audit, auditIfNotExists, deployIfNotExists, manual.

Multiple policies can be assigned to a single resource at the same scope or different scopes. Each policy mostly has a different effect defined. The condition and effect for each policy is independently evaluated. The net result of layering policy definitions is considered cumulative, most restrictive - the strictest applicable policy wins (if one policy says Audit and another says Deny for the same resource, Deny wins.)

#### synchronous vs asynchronous evaluation

Synchronous effects are evaluated in real-time during the resource request. The resource is blocked or modified before it completes

Asynchronous effects are evaluated after the resource request completes. The resource is created first, then the policy acts on it.

Manual - no automated evaluation at all, requires human attestation

#### Interchangeable rules

It means they address the same category of problem and can be substituted depending on how strictly you want to enforce it. audit / deny/modify / append are interchangeable. These all deal with the resource itself at creation or update time. They differ only in how aggressively they act: Effect: What it does to the resource audit: Let it through, logs a warningdenyBlocks it entirely, modify / appendLets it through but fixes it So if you have a policy "resources must have a CostCentre tag" you could write it as:

audit — resource is created, flagged as non-compliant in the dashboard deny — resource creation is blocked until the tag is added modify — resource is created, tag is automatically added with a default value

Same rule, three different enforcement stances. That's what interchangeable means here — same condition, choice of effect depends on how strict you want to be.

auditIfNotExists / deployIfNotExists are interchangeable These both deal with a related/child resource that should exist alongside the primary resource: EffectWhat it doesauditIfNotExistsFlags non-compliance if the related resource is missingdeployIfNotExistsFlags non-compliance AND automatically creates the missing resource Example: "Every VM must have the Log Analytics agent installed"

auditIfNotExists — VM is created, policy checks for the agent, flags it if missing. deployIfNotExists — VM is created, policy checks for the agent, installs it if missing

Again — same condition, same check, different level of automation.The&#x20;

manual is not interchangeable; the manual requires a human to attest compliance — there is no automated condition you can write to replace that. It exists for things that genuinely cannot be evaluated programmatically, like "has this system been physically inspected?" No other effect can substitute for that.

disabled is interchangeable with anything disabled. Simply turning the policy off entirely — it can replace any effect when you want to temporarily deactivate a policy without deleting or modifying the definition itself. Useful for testing or rollout phases.

## Evaluation of resources through Azure Policy

### Evaluation triggers

Policy evaluations are triggered by

* a policy or initiative being newly assigned or updated
* a resource being deployed or updated via ARM, REST API, or SDK
* a subscription being moved in a management group hierarchy
* a policy exemption being created, updated or deleted
* the standard 24 hour compliance cycle
* managed resource configuration updates
* on-demand scans

### Evaluation timing

understand the behaviour and timing of compliance scans.

Compliance scans are triggered by various methods

* automatic full scan - automatically, every 24 hours
* manual scan for brownfield scenarios - manually triggered via `az policy state trigger-scan`

delays of upto 30 minutes can occur when assigning a new policy. This is due to the ARM cache holding session data, hence it can take time for the policy to propagate in the same session.

Signing out and back again refreshes the ARM cache.

#### factors influencing how long it takes for a compliance scan to complete

* size and complexity of a policy definition
* number of policies - the more policies applied, the longer the scan time
* scope size - size of the resource scope
* system load - the system prioritizes interactive and high-important operations, of which scans are not
* synchronous scan (Low property execution) - low priority synchronous scans have are given a low priority by the system

### Resource compliance states

Azure policy assigns one of the following states to each evaluated resource, listed in priority (highest ranks wins when there is a state conflict)

* Non-compliant - resource does not meet the policy condition
* Compliant - resource meets the policy condition
* Error - template or an evaluation error
* Conflicting - two policies at the same scope have contradicting rules
* Protected - resource is covered by a denyAction assignment
* Exempted/Unknown - default state for manual effect definitions

Compliance percentage = (Compliant + Exempt + Unknown) resources / total number of resources Total resources include resources with Compliant, Non-compliant, Unknown, Exempt, Conflicting, and Error states. (no protected)

### Enforcement Mode

enforcementMode is a property of policy assignment that lets you deactivate the enforcement of certain policy effects, without disabling evaluation - it is a 'what-if' mode.

enforcementMode: DoNotEnforce still evaluates resources and reports compliance states, but doesn't trigger the effect or write to the Activity Log, unlike the disabled effect, which prevents evaluation entirely.

If not specified, the value 'Default' is used - enforcementMode: Default, which enables enforcement

### Policy enforcement and safe deployment best practices

The best practices framework focuses on minimizing the impact of policy changes while ensuring compliance and it includes 2 aspects

* Start from assignment of new policies with enforcementMode disabled, to observe compliance without triggering effects.
* Deploy policies gradually through deployment rings - starting in dev/test, then expanding to production incrementally.

#### The safe deployment best practices framework for Azure assignments

1. Create the definition - with the scope as root (tenant)
2. Create an assignment - define deployment rings (1-5) by using resource selectors. Assign the policy with enforcementMode Disabled to a specific scope in ring 5. 3a. Compliance check - verify that the policy is assigned correctly and that the desired compliance state is achieved for resources in ring 5 3b. Application health check - Assess the impact of the policy for resources in ring 5 - ensure no side effects exist
3. Repeat for each ring (non-production) - Repeat step 3
4. update assignment (optional) - if necessary, adjust the policy definition or assignment based on the results so far. 6a. Compliance check - reevaluate compliance after making changes (same as step 3a) 6b. application health check
5. Repeat for each ring (non-production) - repeat step 6
6. Repeat for production rings - gradually deploy to production environments

### Reacting to Policy State Changes

Azure policy integrates with Azure Event Grid to push compliance state change events to event handlers such as Azure Functions, Logic Apps, custom HTTP listeners or webhooks, without requiring polling or complex code. Event grid handles routing, filtering, retry policies and dead-letter delivery

## Secure your resources with Azure RBAC

RBAC is about authorisation (what you're allowed to do once authenticated), not authentication (proving who you are). That's Entra ID's job. They work in sequence:

Entra ID authenticates → RBAC authorises → Azure Policy governs

### What is Azure RBAC

It is an authorization system built on ARM that provides fine-grained access management for Azure resources. With RBAC, you can grant the exact access that users need to do their jobs.

Child scopes inherit roles assigned at a higher scope.

The scope of a role assignment can be a management group, subscription, resource group, or a single resource. RBAC helps ensure

* People exiting the organization lose their access to company resources
* the right balance between autonomy and central governance - users can still create and manage resources, e.g., VMs, while centrally controlling the networks those VMs use to communicate with other VMs

### Azure RBAC in the Azure portal

Access control (IAM) pane at a scope level, e.g., resource group, resource

### How does Azure RBAC work?

You control access to resources via role assignments, Combiningrol how permis,sions are enforced.

To create a role assignment, you need 3 elements

* a security principal (who) - a user, group or application to which you grant access
* a role definition (what) - a colle. ction of permissions aka role A role definition lists the permissionto s the role can perform such requires you to as read, write, deleted Azure includes four fundamental built-in roles \*\* Owner - has full access to resources, can delegate to Ithers \*\* Contributor - can create and manrole's  types of Azure resources, but can't grant access to others \*\* Reader - Can view existing resources \*\* User Access Administrator - manages user access to resources You can create your own custom roles
* scope (where) In Azure, you can specify a scope at multiple levels: management group, subscription, resource group, or resource. Scopes are structured in a parent-child relationship. When you grant access at a parent scope, the child scopes automatically inherit those permissions

### Role assignment

Combiniing the who, what and where to grant access.

Role assignment is the process of binding a role to a security principal at a particular scope for the purpose of granting access.

### Azure RBAC is an allow model.

Multiple assignments are additive e.g if one assignment grants you read and another assignment write on the same resource group, you have both.

Azure RBAC uses NotActions permissions. NotActions

* is not a deny/explicit deny
* it is used to subtract permissions from a roles 'Actions'
* Effective permissions = Actions - NotActions
* example \* in actions (can perform all actions at that scope), NotActions subtracts the ability to create/delete role assignments - prevents a contributor from escalating their own privileges
* only subtracts within the same role definition. Cannot subtract from another role's definition.

### Tools for interacting with Azure

* Azure portal - web-based console to build, manage, and monitor cloud resources
* Azure Cloud Shell - Browser-based shell available on the Azure portal. Supports both Azure PowerShell and Azure CLI
* Azure PowerShell - Allows commands to be written in cmdlets
* Azure CLI - Allows commands to be written in bash
* Copilot in Azure - AI assistance for contextual guidance in natural language

### Azure Arc

Azure Arc works with Azure Resource Manager to extend your Azure compliance and monitoring to hybrid and multicloud configurations.

It provides Unified Management across environments, including

* Azure - Native Azure resources managed through ARM
* on-premises - servers, Virtual machines, Kubernetes clusters, SQL server, Azure data services
* other clouds - servers, Virtual machines, Kubernetes clusters, SQL server, Azure data services

> Azure Arc is Microsoft's solution for managing resources that live outside of Azure — on-premises servers, other clouds (AWS, GCP), or edge locations — using the same Azure tools, policies, and governance you'd use for native Azure resources.

A lightweight Arc agent is installed on the external machine. The agent

* registers the resource in ARM
* Makes the resource visible in the Azure portal
* Allows Azure to send policy, configuration, and monitoring instructions to it
* reports compliance state back to Azure

### Azure Resource Manager and Azure ARM templates

Anytime you do anything with Azure resources, Azure Resource Manager is involved. When a user sends a request from any of the Azure tools, APIs, or SDKs

* ARM receives the request
* ARM authenticates and authorises the request
* ARM sends the request to the Azure service, which takes the requested action. You see consistent results and capabilities in all the different tools, because all requests are handled through the same API

Resource manager has inbuilt validation - it checks templates before deployment to ensure deployment succeeds.

Resource Manager orchestrates the deployment of the resources so they're created in the correct order. When possible, resources are created in parallel, so ARM template deployments finish faster than scripted deployments.

#### Infrastructure as Code

managing infrastructure through code and templates, instead of manual configuration.

Fundamentally, this can start by using PowerShell or Azure CLI, then grow into a repeatable environment using ARM templates (JSON) and Bicep (Declarative DSL)

**ARM templates**

Define desired Azure resources in declarative JSON (Javascript Object Notation).

Benefits

* declarative syntax - define what to deploy rather than step-by-step deployment commands
* modularity - split templates into reusable components and nested templates
* extensibility - add deployment scripts when additional setup actions are required
* repeatable results - consistent outcomes
* orchestration - ARM handles dependency order and parallel deployment automatically
* you can store code in a repository and version it
* you can integrate ARM templates into a CI/CD pipeline (Azure Pipelines)
* idempotent - you can deploy the same template many times and get the same resource types in the same state

If your deployment becomes too complex, you can break it down into smaller reusable components, which can be linked together at deployment time.

_**Template File structure**_

* schema (required) - a required section that specifies the JSON schema that describes the structure of JSON data, e.g., [https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#](https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json)
* contentVersion (required) - version of your template, e.g., 1.0.0.1
* apiProfile (optional) - define a collection of API versions for resource types
* parameters (optional) - values that are provided during deployment, can be provided in a parameter file, command line parameters, or in the Azure portal. Upto 256 parameters per template are allowed
* variables (optional) - define values that are used to simplify template language expressions
* functions (optional) - define user-defined functions that are available within the template, useful for expressions used repeatedly
* resources (required) - defines the actual items you want to deploy or update in a resource group or subscription
* output (optional) - specify the values that are returned at the end of the deployment

_**Deploying an ARM template**_

You can deploy an ARM template in the following ways

* Deploy a local template
* Deploy a linked template
* Deploy in a continuous deployment pipeline

**Bicep**

A declarative language for deploying resources through ARM. More concise and simpler than JSON ARM templates

Benefits

* support for current Azure resources - tracks Azure resource types and API versions.
* Simple syntax - Easier to read and write than JSON ARM templates.
* repeatable deployments.
* built-in orchestration.
* modularity - Reuse Bicep modules.

**Declarative vs Imperative**

Declarative - specify the end result - declare what you want to deploy

Imperative - define step-by-step instructions for the end result - write a sequence of instructions for how to deploy a resource

## Control and organize Azure resources with Azure Resource Manager

### Principles of resource groups

Resource group - a logical container of resources

All resources must be in a resource group, and a resource can only be in one resource group

Many resources can be moved across resource groups, others have specific limitations or requirements to move.

Resource groups cannot be nested.

Resource groups provide

* logical grouping - organize resources based on
  * department
  * authorization - who needs to administer them e.g. database team for SQL servers
  * billing - understand where costs are allocated
  * lifecycle - e.g., scratch resource group can be deleted anytime, production should not be deleted
  * environment - prod, qa, dev
  * resource type - e.g. databases, network
  * combination of the above criteria
* Lifecycle management - deletion of a resource group deletes everything in it
* authorization - a scope for applying RBAC

Adopt a naming convention for naming resource groups, so that they are easy to identify. Enforce the naming convention via Azure policy.

### Creating a resource group

via

* Azure portal
* Azure powershell
* Azure CLI
* Azure SDKs
* ARM templates

### Use Tagging to organize resources

Tags are name/value pairs of text data that you can apply to resources and resource groups.&#x20;

Tags allow you to associate custom details about your resource.

#### Tag properties

* A resource can have up to 50 tags.
* The name value is limited to 512 characters for all types of resources except storage accounts, which have a limit of 128 characters.&#x20;
* The tag value is limited to 256 characters for all types of resources.&#x20;
* Tags aren't inherited from parent resources.&#x20;
* Not all resource types support tags
* You can't apply tags to classic resources

You can use Azure Policy to automatically add or enforce tags for resources your organization creates based on policy conditions that you define. e.g., require that a department tag is entered when someone creates a virtual network in a specific group

#### Tagging for organization

You can use tags to organize

* For billing - use tags to group usage by cost center
* to categorize costs  - billing usage for production vs dev VMs
* for resource retrieval - retrieve resources that have a specific tag name or value, as well as their related resources
* to monitor or track down impacted resources - monitoring systems can use tag data with alerts, e.g., VM with tag financedepartment, will help notify the affected system owners.&#x20;
* for dependency mapping - example with a monitoring system, if a virtual network is impacted, then you know which VM might be affected
* for automation - use tags to support automation, e.g., shutdown:6pm

### Use policies to enforce standards

Apply Azure policies on resources having particular tags - associate tags with a policy - using parameters

### Secure resources with RBAC

Best practices for RBAC

* use least privilege rule - only assign permissions required for the task to be done
* use resource locks to ensure critical resources are not modified or deleted

### Use resource locks to protect resources

Resource locks are a setting that you can apply to any resource to block modification or deletion.

You can set resource locks to either **Delete** or **Read-only.**

* Delete - will allow all operations against the resource, but block the ability to delete it.
* Read-only - will only read activities to be performed against it.
  * read-only can prevent some legitimate read-only actions from happening, e.g., a read-only lock on a storage account prevents listing of keys, as this operation is backed by POST

You can apply resource locks to

* subscriptions
* resource group
* resource

Resource locks are inherited when applied at higher levels.

Similar to break glass. - to apply an operation on the locked object, the lock has to be lifted, helping protect the resource from inadvertent actions.

##

