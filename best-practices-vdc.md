<properties
   pageTitle="Microsoft Azure Virtual Data Center"
   description="Learn how to build your virtual data center in Azure"
   services=""
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/25/2016"
   ms.author="jonor;fabferri;telmos" />

#Microsoft Azure Virtual Data Center
**Microsoft Azure**: Move faster, Save money, Integrate on-premises apps and data

##Overview
Migrating on-premises applications to Azure, even without significant changes (an approach known as “lift and shift”), provides organizations the benefits of a secured and cost-efficient infrastructure. However, to make the most of the agility possible with cloud computing, enterprises should evolve their architectures to take advantage of Azure capabilities. Microsoft Azure delivers hyper-scale services and infrastructure, enterprise-grade capabilities, and many choices for hybrid connectivity. Customers can choose to access these services either via the Internet or with Azure ExpressRoute, which provides private network connectivity. The Microsoft Azure platform allows customers to seamlessly extend their infrastructure into the cloud and build multi-tier architectures. Additionally, non-Microsoft partners provide enhanced capabilities by offering security services and virtual appliances that are optimized to run in Azure. This article provides an overview of patterns and designs that can be used to solve the architectural scale, performance, and security concerns many customers face when thinking about moving en masse to the cloud.

An overview of how to fit the different IT roles in the organization to the management and governance of the system is also discussed, always keeping in mind security requirements and cost optimization.

##What is a Virtual Data Center?
In the early days of public clouds, clouds were designed for small development teams to get single applications, in relative isolation, into the public cloud. This worked well for a few years, but as the benefits of putting workloads into the cloud became apparent, bigger scale (and the problems that come with it) needed to be solved. Problems such as redundant equipment across workloads, or missing devices making workloads out of compliance with corporate standards as shown in the diagram below.

[![0]][0]

It was the necessity of scale from which the Virtual Data Center (vDC) was born.

The inspiration of this paper is to provide architectural patterns and advice on how a vDC can be designed to be secure, reliable, highly scalable, and cost efficient.

A vDC is not just the application workloads in the cloud, but also the network, security, management, and infrastructure (for example, DNS and Directory Services) and usually a private connection back to an on-premise network or data center. As more and more workloads move to Azure it is beneficial to think about the supporting infrastructure and containing objects that these workloads are placed in to avoid hundreds of "workload islands" that must be managed separately with independent data flow, security models, and compliance challenges.

By viewing your workloads as an integrated Virtual Data Center, that is to say separate but related entities with common supporting functions, features, and infrastructure; you can realize reduced cost due to economies of scale, optimized security through component and data flow centralization, and easier operations, management, and compliance audits.

>[AZURE.NOTE] It's important to understand that the vDC is **NOT** a discreet Azure product, but the combination of various features and capabilities to meet your exact requirements. vDC is a way of thinking about your workloads and Azure usage to maximize your resources and abilities in the cloud. The virtual DC is therefore a modular approach on how to build up IT services in the Azure, respecting company roles and responsibilities.
 
The vDC can help enterprises get workloads and applications into Azure for the following scenarios:

 - Hosting multiple related workloads
 - Migrating workloads from an on-premise environment
 - Implementing shared or centralized security and access requirements across workloads
 - Mixing DevOps and Centralized IT appropriately for a large enterprise

As it will be discussed more in this and subsequent articles; the key that unlocks the vDC is a centralized topology (hub and spokes) with a mix of Azure features: Azure VNet, NSG, UDR, VNet peering, and Azure Identity (with Role Base Access Control and policy).

##Who Needs a Virtual Data Center?
Any Azure customer that needs to move in excess of two to three workloads into Azure can benefit from thinking about common resources to be utilized. Given sufficient size, even single applications can benefit from using the patterns and components used to build a vDC.

If your organization has a centralized IT, Network, Security, and/or Compliance team/department, a vDC can help enforce policy points, segregation of duty, and ensure uniformity of the underlying common components while giving the application teams as much freedom and control as is appropriate for your requirements.

Organizations that are looking to DevOps can utilize the vDC concepts to provide authorized pockets of Azure resources and ensure they have total control within that group (either subscription or resource group in a common subscription), but the network and security boundaries stay compliant as defined by a centralized policy in a hub VNet and Resource Group.

##Considerations on Implementing a Virtual Data Center
There some pivotal aspects to considered when designing a vDC:

 - Identity
 - Security
 - Directory Services
 - Connectivity to the cloud
 - Connectivity within the cloud

Identity is a key aspect of a data center. Generally, working in a data center requires cooperation between different company teams and roles to enable the systems to run optimally with good governance.

Identity and access for controlling Azure resources within an Azure subscription is done using Azure Active Directory (Azure AD) and Role-Based Access Control (RBAC).

**<-- Should this Selection be in a next level down document and removed from this high level doc? -->**
**<-- Begin Selection -->**

An Azure subscription grants you access to all Azure resources. An Azure subscription has two aspects:

- The Azure account, through which resource usage is reported and services are billed.
- The subscription itself, which governs access to and use of the Azure services that are subscribed to. The subscription holder (account owner) manages services through the Azure platform.

An Azure subscription grants access to the Azure resource, but it does not define who can access (authentication) to the resource and by which rights (authorization).
The Azure AD is a multi tenant cloud service to manage directory and identity [https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/ "What is Azure Active Directory")
The Azure subscription (using Azure Management portal or Azure Resource Manager API), before get access to the resources, needs to authenticate with that Azure AD.
Every Azure subscription has a trust relationship with an Azure AD tenant. This means that it trusts that directory to authenticate users, services, and devices. Multiple subscriptions can trust the same directory, but a subscription trusts only one directory.
[https://azure.microsoft.com/en-us/documentation/articles/active-directory-how-subscriptions-associated-directory/](https://azure.microsoft.com/en-us/documentation/articles/active-directory-how-subscriptions-associated-directory/ "How an Azure subscription is related to Azure AD")

		fig.2: multiple Azure subscriptions with single AAD tenant  

In Azure Resource Manager all the Azure resources respect a hierarchy:

- Each subscription in Azure belongs to only one Azure AD.
- Each resource group belongs to only one Azure subscription.
- Each resource belongs to only one resource group.
 
When an Azure subscription is created a Service Administrator (SA) is associate with the subscription; this role is authorized to manage all Azure services available in the Azure subscription. A single administrator does not allow to get the work done in a vDC.
Enterprises need to give to a specific department (group of users), the exact permissions they need. Too many permissions exposes an account to attackers, but too few permissions means that employees can't get their work done efficiently. Azure Role-Based Access Control (RBAC) helps to address this problem, by offering fine-grained access management for resources in Azure subscription.
Azure RBAC allows you to grant appropriate access to Azure AD **users**, **groups**, and **services**, by assigning roles to them on a subscription or resource group or individual resource level. The assigned role defines the level of access that the users, groups, or services have on the Azure resource. A role is a collection of actions that can be performed on Azure resources. A user or a service is allowed to perform an action on an Azure resource if they have been assigned a role that contains that action.
[https://azure.microsoft.com/en-gb/documentation/articles/role-based-access-control-what-is/](https://azure.microsoft.com/en-gb/documentation/articles/role-based-access-control-what-is/ "Basics of access management in Azure")

		fig3: RBAC

Azure RBAC comes with built-in roles (that you can't modify) that can be assigned to users, groups, and services. 
see [https://azure.microsoft.com/en-us/documentation/articles/role-based-access-built-in-roles/](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-built-in-roles/ "RBAC: Built-in roles")

There are three special build-in roles: 

- **Owner**: This role has full control over Azure resources. Owner can perform all management operations on a resource including access management. 
- **Contributor**: This role can perform all management operations except access management. So, a contributor can’t grant access to others.
- **Reader**: This role can only view resources. Reader can’t view secrets associated with a resource. 

As it will discussed further, Contributor and Reader are two main roles to control the access to the resources of the vDC. The are two expects need to be considered in usage of RBAC in vDC:

- access granted at parent scopes is inherited at child scopes.
- usage of build-in security roles is the preferred approach to simplify the management

		Fig. 4: RBAC scope

If none of the built-in roles meet your specific access needs, RBAC offers the possibility to use custom roles. Custom roles are stored in an Azure AD tenant and can be shared across all subscriptions that use that tenant as the Azure AD directory for the subscription.
see [https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-custom-roles/](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-custom-roles/ "Custom Roles in Azure RBAC")
This is particular pertinent for vDC, where custom roles can be in some cases stored in different AD tenants.
  
A custom role is a collection of **Actions**, **NotActions** and **AssignableScopes** properties:

- **Actions**: it specifies the Azure operations to which the role grants access. It is a collection of operation strings that identify operations on the Azure resource providers. 
- **NotActions**: it sets of operations that you wish to exclude. The access granted by a custom role is computed by subtracting the **NotActions** operations from the **Actions** operations.
- The **AssignableScopes**: it specifies the scopes (subscriptions, resource groups, or specific resources) within which the custom role is available for assignment. You can make the custom role available for assignment in only the subscriptions or resource groups that require it, or an Azure Resource Provider.

Roles can be assigned to the following types of Azure AD objects:

- **Users**: roles can be assigned to organizational users that are in the Azure AD with which the Azure subscription is associated.
- **Groups**: roles can be assigned to Azure AD security groups. A user is automatically granted access to a resource if the user becomes a member of a group that has access. The user also automatically loses access to the resource after getting removed from the group.. Managing access via groups by assigning roles to groups and adding users to those groups is the best practice, instead of assigning roles directly to users. The ability to assign roles to groups lets an organization extend its existing access control model from its on-premises directory to the cloud, so security groups that are already established to control access on-premises can be re-used to control access to resources. For more information about different options for synchronizing users and groups from an on-premises directory, see [https://azure.microsoft.com/en-gb/documentation/articles/active-directory-aadconnect/](https://azure.microsoft.com/en-gb/documentation/articles/active-directory-aadconnect/ "Integrating your on-premises identities with Azure Active Directory")
- **Service principle**: it is normally used to authenticate process. 

Roles can also be assigned for resource groups as well as for individual resources. 
So if a user, group, or service is granted access to only a resource group within a subscription, they will be able to access only that resource group and resources within it, and not the other resources groups within the subscription. As another example, a security group can be added to the Reader role for a resource group, but be added to the Contributor role for a network within that resource group.

Azure Resource Manager allows also to control access through custom policies. With policies, you can prevent users in your organization from breaking conventions that are needed to manage your organization's resources.
You create policy definitions that describe the actions or resources that are specifically denied. As in RBAC you assign those policy definitions at the desired scope, such as the subscription, resource group, or an individual resource. Policies are inherited by all child resources. So, if a policy is applied to a resource group, it is applicable to all the resources in that resource group.
Policies and RBAC work together; to use policies, you must be authenticated through RBAC. Unlike RBAC, policy is a default allow and explicit deny system. 
Using policies, it is possible to address some scenario of the vDC:

- *billing requirement*: one common scenario is to require departmental tags for billing purpose. An organization might want to allow operations only when the appropriate cost center is associated; otherwise, they deny the request. This policy helps them charge the appropriate cost center for the operations performed.
- *control on Azure region*: An organization might want to control the locations where resources are created. 
- *restrict SKUs*: you might want to restrict the deployment to VMs and/or storage account to specific types.
- *enforce naming convention*. it will be useful to impose a name convention on Azure resources.

see [https://azure.microsoft.com/en-us/documentation/articles/resource-manager-policy/](https://azure.microsoft.com/en-us/documentation/articles/resource-manager-policy/ "Use Policy to manage resources and control access")

**<-- End Selection -->**
**<-- This section started with a bulleted list, we only talked about Identity, we should talk about the other items in the list at a very very high level -->**

##Virtual Data Center Overview
###Topology
A vDC is deployed in Azure region and it is based on hub and spokes architecture.

[![1]][1]

The hub is a central zone to control and inspect the ingress and/or egress traffic in between different zones: internet, on-premises and the spokes. The hub and spokes topology give the central IT department an effective way to enforce security policies in central location while reducing the potential for misconfiguration and exposure.

In the hub reside the common service components required by the spokes. Here few typical examples of common central services: 

- the Windows Active Directory infrastructure (with the related ADFS service) required for user authentication of third parties accessing from untrusted networks before getting access to the workloads in the spoke, 
- a DNS service to resolve naming for the workload in the spokes, to access to specific resource on-premises and to internet
- a PKI infrastructure, to implement a single sign-on on workloads
- a flow control (TCP/UDP) between the spokes and Internet
- a flow control between the spoke and on-premises
- in some seldom case, the traffic control between spoke and spoke.

Compare with traditional approach to the DC, the vDC reduce the overall cost through the multiple utilization of shared hub infrastructure between different spokes.
The role of the spokes is to host the different type of workloads. The spokes provide a modular approach of repeatable deployments (e.g. dev and test, UAT, pre-production and production) of the same workloads. Inside a spoke is possible to deploy a basic workload or a complex multi-tier workload with traffic control between tiers.

A single vDc can scale up easily to large number of spokes; nevertheless as every IT system the the system can reach out a limit present in the platform.
The hub deployment is bound to a specific Azure subscription that have some restrictions and maximum values (e.g. max number of VNet peering-see [https://azure.microsoft.com/en-us/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/en-us/documentation/articles/azure-subscription-service-limits/ "Azure subscription and service limits")); in these circumstances the architecture is still capable to scale up, extending the model by a cluster of hub and spokes, in the same Azure region.

		fig6: cluster of hub-spokes

Introduction of multiple hubs determines an increment of cost and management effort justified only in the case of presence of high  number of spokes. In the vDC the total cost of hub should be marginal compare with the cost of hub, to avoid to come back to the approach described at beginning for the paper with duplication of components in Azure.
It is interesting to debate some interconnection cases:

- **interconnection between hubs.** The vDC model is based on hub containing all the common services, requested to the spokes and in principle there is no benefit to interconnect hubs.
- **interconnection between spokes** (belonging to the same hub or different hubs). Inside a single spoke is possible to implement complex multi-tiers workloads. Multi-tier configurations can be implemented using subnets (one for every tier) in the same VNet and filtering the flows by NSGs. A typical example is the case where multiple application servers deployed (under the responsibility of developer team) are in a spoke while the  database is deployed in a different spoke. In this case it is easy interconnect the spokes to avoid transit in the hub.

		fig.7: spoke-to-spoke

One further point of discussion is the possibility to create spokes interconnected to a spoke that works itself as hub. From topology prospective, the approach increases the level of hierarchy hub-spokes topology as two levels: in this case the spoke in higher level of hierarchy (level 0) become the hub of lower spokes (level 1) of the hierarchy.

		fig.8:  vDC topology with two levels 

Azure offers the possibility to implement complex topologies, but one of principle of vDC is repeatability and simplicity to minimize the management effort; the simple hub-spokes remains at most the vDC reference architecture. 

An vDC architecture can be built properly with resiliency, by duplications and load balancing of the different Azure components, but nevertheless it is not resilient major disaster of Azure region; high resilient services can be created only through multiple vDCs deployed in different Azure regions. 

###Components
A Virtual Data Center (vDC) is made of up four basic component types;  **Infrastructure**, **Perimeter Networks**, **Workloads**, and **Monitoring**. 

Inside each component type are many Azure features and resources that can be used to make up an instance of that component to your exact specification.

Your vDC will be made up of multiple types of components and in most cases multiple, differently structured, instances of the same component type. For instance you may have many different workloads instances that represent different applications and are logically separated. The idea is that you would use the different component types and instance to ultimately build the vDC.

Inside each component type are many Azure features and resources that can be used to make up an instance of that component to your exact specification.

Your vDC will be made up of multiple types of components and in most cases multiple, differently structured, instances of the same component type. For instance you may have many different workloads instances that represents different applications and are logically separated. The idea is that you would use the different component types and instance to ultimately build the vDC.

			Fig9: High Level Diagram for vDC

The high level digram of vDC shows the different component types in the different zones and the hub-spokes topology; the strength of vDc is mostly due with the centralized nature of the architecture. 


			Fig10: subscriptions and RBAC in vDC

As good practice (for on-premises DC or vDC) the rights are set on groups; this allows consistency set of right across the team and avoid reconfigurations due to change of role in the organization or living the company.
The diagram shows different roles for different groups: 

- the *central IT group* have the ownership to control of the infrastructure components and therefore need to have the role of contributor on the subscription (hub) or network contributor in the spokes. Some large organization are used to split up the management respectabilities of the the hub between different teams: network group (with exclusive focus on networking) and security group (responsible of firewall with internet and traffic control). In this specific case two different groups need to be created with assignment of by custom roles.
- the *dev & test (devops) group* have the responsibility to deploy workloads; this group requires at least the role of Virtual Machine Contributor for IaaS deployment and/or one or more PaaS contributors roles (see [https://azure.microsoft.com/en-us/documentation/articles/role-based-access-built-in-roles/](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-built-in-roles/ "RBAC: Built-in roles") ). Optionally the dev& test team need to get visibility on security policies (NSGs) and routing policies (UDR) inside a specific spoke; in this case further to the roles of contributor for workloads, it gets the role of Network Reader.
- *operation and maintenance group* have the responsibility of production; the group needs to be a subscription contributor on workloads in production subscription. To fix potentially misconfiguration and issue with production, the company might evaluate if assign to an escalation support team group the role of subscription contributor in production and in the central hub subscription.
    
One of interesting point to create the structure of the vDC is the Line of  Businesses (LOBs). Enterprises need need to understand how to bind the different company projects to the Azure subscriptions.

		fig11: Users, groups, subscriptions and LOB 

The diagram reports the concept of users, group of user with specific role assignment to access to the Azure resource in the different deployment. Organization can make leverage on unique Azure AD tenant or multiple tenants; the diagram report the case where one tenant is used only for production deployments of different LOBs. The utilization of two Azure AD tenants enforce the separation between devops and production. 

There are some simple cases can be considered to understand the implications:

- every environment (e.g. devops) can share the same Azure subscription between different LOBs. The scenario works well in the case the same teams work on all LOBs. The benefit of this approach is to use a low number of Azure subscriptions but with presence multiple deployments of different LOB in the same subscription. The disadvantage of this approach is the presence of multiple deployments in the same subscription and large numbers of Resource Groups with consequent high probability to make accidental actions in wrong deployment. 
- every LOB have different teams. In this case the best option is to create an Azure subscription for every environment (devops, UAT, PROD). This approach have strong separation of environments and LOBs, but it potentially prone to an excess of proliferation of subscriptions in vDC. (e.g. with with 30 LOBs, each with three different environments, requires 90 subscriptions).
- more common scenario is where Central IT and PROD group are the same for every LOB and devops is arranged in sub-teams based on type of workload. In this case some Azure subscriptions can be shared across a list of LOBs.

In all scenarios, and in particular in all production deployments, is strongly recommended to lock the resource group to avoid human mix-up and consequent accidental deletion of resources.
The diagram below report a basic example of vDC structure with few Azure components in the hub and spokes.

			Fig. 12: example of vDC structure


####Component Type: Infrastructure
This is where major supporting infrastructure will reside, it's also where your centralized IT, Security, and/or Compliance teams will spend most of their time.

Feature list included in Infrastructure:

- Identity & Directory Services					(Green)
- Virtual Network, UDR, and NSG					(Green)
- DNS											(Red)
- Subscription and Resource Group Management	(Yellow)
- RBAC Strategy									(Red)

			fig13: High Level Diagram for Infrastructure

The Infrastructure is pervasive mandatory component type present in different parts of the architecture and provides the interconnection between different components of the vDC, and it is present in the hub and in the spokes.
The responsibility to manage and maintenance the infrastructure components are typically assigned to the central IT and security team.
 
One of primary tasks of IT infrastructure team is to guarantee the consistency of IP address schema across the enterprise: the private IP address space assigned to the vDC need to be coherent and NOT in overlapping with private IP addresses assigned on-premises DC.
Usage of NAT on the on-premises edge routers (or in Azure), to avoid IP address conflicts, it is not a recommended solution, due to collateral effects of missing transparency in communication between applications, and it should be avoided.

Let's describe briefly the different components included in the infrastructure. 

- **Identity and directory services**. Access to every resource type  in Azure is controlled by identity stored in directory service. the directory service store not only the list of users but also the right to access to a resources in a specific Azure subscription. The identity can live only in the cloud or can be synchronized with on-premises identity in Active Directory structure.
- **Virtual Network**. Virtual Network is the main feature of the infrastructure composed by a single or multiple segments, each with a specific IP network prefix.  
Virtual Network is mandatory component of the vDC.
- **UDR**. A custom routing table is a list of routes  a provide a set of routing rule to overwrite the system table behaviour. An UDR can be applied to a subnet. If UDR is an optional component in some specific scenario, it becomes a must to have in the vDC, to guarantee that egress traffics from the spoke transit through Network Virtual Appliances present in the hub.
- **NSG**. In Azure the traffic control is nativity implemented through Network Security Group (NSG). A Network Security Group is a list of security rules that act as filtering; each rule is based on a tuple of 5 values: IP Source, IP Destination, Protocol, IP Source Port and IP Destination port. One powerful expect of NSG that the filtering mechanism is stateful: when a rule is created to allow to establish the flow, the flow related to the answer is automatically accepted. This property of NSG hep to reduce the list of entries in the security policy. The NSG can be applied to a subnet or a Virtual NIC card of an Azure VM. To have an agile management of security policies in vDC is recommended to make leverage of NSGs applied to subnets: this avoid a proliferation of NSGs with possibility to reuse the same NSG for different deployments (dev and test, UART, pre-production and production). In principle NSGs are not a mandatory component, but become essential to implement a correct flows control in the vDC.
- **DNS**. DNS is mandatory component present in all infrastructures, on-premises and in the cloud. If not specified the VNet uses an default Azure DNS (assigned automatically from Network Resource Provider) to resolve naming inside the VNets. The default DNS associate to the VNets normally doesn't satisfy with customer requirements. In most of cases a custom DNS service is part of common services in the hub. The main consumers of DNS reside in the spoke. For example a two tiers application with a web front and a SQL server in backed might requires of SQL server resolution by ALIAS.
- **Subscription and Resource Group Management**. A subscription defines a natural boundary to create multiple type of resources in Azure. A Resource Group is a logical container inside a subscription, that define a set of mix resources. The nature of Resource Group is pure administrative and does not influence how the different components, defined in it, are assembled together. The Resource Group has therefore a pure administrative nature and need to be used in vDC to reflect the logical partition of multiple resources and rights. When a Resource Group are deleted all the associated resource will be removed.
- **RBAC**. As described previously in the article, the RBAC is crucial point of the vDC architecture, to map company role with rights to access to the Azure resources. 

The fundamental component to create the infrastructure of a vDC is the VNet peering, a mechanism that connects two virtual networks (VNets) in the same region through the Azure backbone network (see [https://azure.microsoft.com/en-us/documentation/articles/virtual-network-peering-overview/](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-peering-overview/ "VNet peering")
After two virtual networks are peered, virtual machines attached to the virtual networks have full IP-level connectivity.The VNet peering have some key aspects:

- The two virtual networks that are peered need to belong to the same Azure region. The regional extension of VNet peering impose the boundary of a vDC to a single region. 
- The virtual networks that are peered do not have to overlap IP address spaces.
- VNet peering is between two virtual networks is not a transitive relationship. The intransitive nature of the VNet peering define the hub-spoke single level as the right architecture to pursue.
- peering can be established between virtual networks in two different subscriptions; the hub can be therefore be deployed in Azure subscription different from a spoke (as highlight in the diagram of the infrastructure).
- traffic filtering between VNet peers is implemented by NSGs. To keep the security tasks easy to control, the traffic in egress and ingress to the spokes can be controller through NSGs applied to subnets of the hub. The NSGs can be applied in the spoke to restrict the traffic between workloads deployed in the spoke. This is a typical case where the spoke hosts a multitier application. A classical example is a three tier application with a web front end deployed in subnet1, an application layer (mid-layer) deployed in the subnet2 and a backend layer to host the DBs in subnet3 of the spoke.    
- when virtual networks are peered, it is possible to configure the gateway in the peered virtual network as a transit point to on-premises. The spokes that are peered with the hub are able to transit in the Azure Gateway type ExpressRoute (or VPN) deployed in the hub to reach out the networks on-premises. 
- to configuration of a VNet peering requests a privileged operation of read-write (as Network contributor), under the Virtual Networks namespace 
 
>Note:
>one virtual network can have only one gateway. It can either be a local gateway or a remote gateway (in the peered virtual network).


####Component Type: Perimeter Networks
This is where network connectivity back to you on-prem or physical data center networks, and connectivity to and from the Internet will reside. It's also where your network, and perhaps security, teams will spend most of their time.

Feature list included in Perimeter Networks:

- Virtual Networks, UDR, NSG	(Yellow)
- Network Virtual Appliance		(Yellow)
- Load Balancer					(Yellow)
- Public IPs					(Yellow)

The central IT and security team have the ownership of definition of requirements, operation and of the perimeter networks. 

			fig14: High Level Diagram for Perimeter Networks

The diagram enforce two perimeters with internet and on-premises network, both resident in the hub.
The perimeter network with internet is normally managed through firewalls and Web Application Firewalls (WAFs). 
In the LOBs there is a predominant existence of web applications, it tends to be a lot of different types of vulnerabilities available for attackers to exploit. 
Web Applications Firewalls are a special breed of product used to detect attacks against web applications (HTTP/HTTPS) in more depth than generic firewall. Compare with tradition firewall technology, WAFs have a set of features to protect internal web servers from malicious traffic (i.e. SQL injections, cross-site scripting, session hijacking, parameter or URL tampering, buffer overflows, malware uploads and application DDoS). WAFs are typically deployed in some sort of proxy fashion just in front of the web applications, so they do not see all traffic on our networks. By monitoring the traffic before it reaches the web application, WAFs can analyse requests before passing them on.
WAFs not only detect attacks that are known to occur in web application environments, they also detect (and can prevent) new unknown types of attacks. By watching for unusual or unexpected patterns in the traffic they can alert and/or defend against unknown attacks. For example- if a WAF detects that the application is returning much more data than it is expected to, the WAF can block it and alert to security analyst.
In the vDC, one expect to consider in selection of WAF technology is the capability to reduce false positive. The false positives are very bad because they make already busy security analysts even busier. An overload of false positives can delay response to actual attacks or cause attacks to get accepted as normal because of an analyst trying to reduce the noise. 
For enterprise that need to protect web traffic from the top common web vulnerabilities,  the Azure Application Gateway is a good cost effective options. For more sophisticate protection, customer can access to a large number of WAF solutions offered by different vendors available in Azure marketplace. 
Most of them offer the capability to scale up horizontally running on larger IaaS VMs, to serve a more higher traffic volume.
For resiliency, it is always recommended the utilization at least two Virtual Appliances in the same availability set to guarantee a reasonable SLA in production.
In a single hub, the perimeter network to internet scales up to large number of LOBs, using multiple farm of WAFs and/or firewalls.

To reduce the potential vulnerabilities of the system, some customers are used to implement this layer through  Virtual Appliances of different vendors.
The presence of multiple DMZ zones, where the incoming traffic is inspected in sequence with Virtual Appliance of different brands, do not provide a better security policy,  but reduce the vulnerability risk of the system. In this case the traffic is analysed in cascade from different software of independent vendors and reduce potential exposure of potential vulnerabilities related to the software stack of a specific firewall vendor (a missing patch in the Virtual Appliance might cause an exposure to malicious threats). 
The decision to use multiple Network Virtual Appliances to create different DMZ needs to be evaluated on analysis of security benefits vs operational cost associated with complexity of the management.
The presence of different Network Virtual Appliance of different vendors has the drawback to increase the security operation cost, due to skillset required to the IT central department to use different technologies.

		fig15: firewall farms of different vendors in cascade

Most of large enterprise manage multiple domains; the Azure DNS can be used to host the DNS records for a particular domain. As example, the Virtual IP Address (VIP) of the Azure external load balancer (or the WAFs) can be registered in the A record of Azure DNS. 	

The Central IT can effectively build an hub-spoke type environments, where the hub can host infrastructure components such as network virtual appliances to filter the traffic in ingress and in egress to internet.
In the hub the distribution of incoming traffic in the virtual appliances will be implemented through an external load balancer.
To guarantee that traffic generated from the VMs resident in the spoke transit in the Virtual appliances, a UDR needs to be set in the subnets of the spoke by setting as next-hop the front-end IP address of the internal load balancer that distribute the internal traffic to the Virtual Appliances (back-end pool).

		Fig16: VNet peering and perimeter neworks

Most of LOBs requires an integration with few services on-premsies; a typical example is the Windows AD. For large vDC the ExpressRoute is the right solution to interconnect Azure with on-premises datacenter, for the nature of private link and also for the deterministic delay and latency associated with the connection and high throughput (10Gbps throughput with ulta-performance gateway) required for large deployments. Provisioning of the ExpressRoute configuration in Azure can be done in few minutes, but the end-to-end provisioning requires the process of purchase order to the network service provider responsible to establish the interconnection between the customer datacenter with the IP Microsoft backbone. The provisioning process can requires more than weeks and to speed up the cloud adoption of Azure, enterprises can decide to deploy in the hub an Azure gateway type VPN to implement a site-to-site VPN. The Azure site-to-site VPN creates an IPSec tunnel between the on-premises VPN concentrator and the Azure Gateway, with possibility to exchange the network prefixes via BGP. Azure site-to-site VPN is provides an effective  way to kickoff the vDC, with integration with on-premises services. The site-to-site VPN is not the definitive solution for the vDC: the transit happen in internet, and the cap value on throughput does not allow large traffic exchange with the on-premises datacenter. 
When the ExpressRoute will be successful provisioned, customer can decide to decommission the site-to-site VPN or keep the consistence with ExpressRoute, using the site-to-site VPN as backup path (higher metric) with on-premises site. 

####Component Type: Monitoring
This is where you have visibility and alerting from all of the above components. All related teams should have access to their components and services. If you have a centralized help desk or operations teams, they would be integrated to this data.

Feature list included in Monitoring:

- Audit logs					(Red)
- Event logs					(Red)
- Data path logs				(Red)
- OMS							(Red)

Azure offers different type of logs to track the logs.

The **Audit Logs** (referred also as "Operational Log") provide insight into the operations that were performed on resources in your subscription. The log reports the control-plane events for your subscriptions. Every Azure resource produces audit logs.
the **Azure Diagnostic Logs** are logs emitted by a resource that provide rich, frequent data about the operation of that resource. The content of these logs varies by resource type.

Central IT and security teams are more focus on log analyse the logs related to the traffic flows.

			Fig17: high Level diagram for Monitoring

As reported in the diagram you can monitor NSGs by:

- **Event logs**: you can use this log to view what NSG rules are applied to VMs and instance roles based on MAC address. 
-  **Counter logs**: you can use this log to view how many times each NSG rule was applied to deny or allow traffic.

you can monitor Application Gateway by: 

- **Access logs**: You can use this log to analyse information including caller's IP, URL requested, response latency, return code, bytes in and out.
- **Performance logs**: You can use this log to view how application gateway instances are performing (total request served, throughput in bytes, total requests served, failed request count, healthy and unhealthy back-end instance count).
- **Firewall logs**: You can use this log to view the requests that are logged through either detection or prevention mode that is configured with web application firewall.

 
As general approach all logs can be stored in Azure Storage Accounts for audit, static analysis, or backup. When the logs are stored in the Azure storage account, customer can used different type of framework to retrieve, prep, analyse and visualize data to report the status and healthy of cloud resources.
Large enterprise have already a standard framework for the monitor of the on-premises system
and want to extent the capability to the cloud deployments.
Azure Storage account are exposed through public IP endpoints; if customer want to make the transit in ExpressRoute a public BGP peering would to be used. 

####Component Type: Workloads
This is where your actual application will reside, it's also where your application development teams will spend most of their time.

Feature list included in workloads:

- Internal LOB Applications 	(Green)
- Customer facing web sites 	(Green)
- Big Data/Analytics 			(Red - Research ACOM)
- IoT, Events, and/or Messaging	(Red - Research ACOM)
- IaaS and/or PaaS components 	(Yellow)

**Stateless Applications** 
When users or services interact with an application they perform a series of interactions that set a session. A stateless application is an application that does not need of any knowledge of previous interactions and does not store session information. 
A stateless application can scale easily since any request can be serviced by any of the available pool resources.
In stateless application there is no data shared between resources. A mechanism of load balancing can distribute the load between available resources; when the system in under intensive load, the system can simply add more capacity as needed. When that capacity is not longer required, few resources in the pool can be terminated. In stateless application is quite easy implement mechanism of elasticity.

			High Level Diagram for Workloads

### A Single vDC
- Wiring component types together to make the vDC
- RBAC
- Subscription
- Environment splitting (Dev, Test, UAT/Stage, Prod)


### Multiple vDC
 - Regional/Global presence
 - Disaster Recovery
 - Probe mechanism to check the service healthy
 - mechanism to divert traffic between DC   

Most of LOBs in production require service with high SLA to have the minim impact on their business. Using different measures it is feasible to create a vDC with no single point of failure. Nevertheless the vDC has a regional extension and a major impact in the region can cause an outage an violation of the service SLAs. to rise up the service level enterprise need to evaluate deployments of two vDCs or more in different regions. 



<!--Image References-->
[0]: ./media/best-practice-vdc/redundant-equipment.png "Example of component overlap"
[1]: ./media/best-practice-vdc/vdc-high-level.png "High level example of hub and spoke vDC"

<!--Link References-->
[Example1]: ./virtual-network/Network-Boundary-DMZ-NSG-ASM1.md


