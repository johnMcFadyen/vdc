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
Migrating on-premises applications to Azure, even without significant changes (an approach known as “lift and shift”), provides organizations the benefits of a secured and cost-efficient infrastructure. However, to make the most of the agility possible with cloud computing, enterprises should evolve their architectures to take advantage of Azure capabilities. Microsoft Azure delivers hyper-scale services and infrastructure, enterprise-grade capabilities, and many choices for hybrid connectivity. Customers can choose to access these cloud services either via the Internet or with Azure ExpressRoute, which provides private network connectivity. The Microsoft Azure platform allows customers to seamlessly extend their infrastructure into the cloud and build multi-tier architectures. Additionally, non-Microsoft partners provide enhanced capabilities by offering security services and virtual appliances that are optimized to run in Azure. This article provides an overview of patterns and designs that can be used to solve the architectural scale, performance, and security concerns many customers face when thinking about moving en masse to the cloud.

An overview of how to fit the different IT roles in the organization to the management and governance of the system is also discussed, always keeping in mind security requirements and cost optimization.

##What is a Virtual Data Center?
In the early days of public clouds, clouds were designed for small development teams to get single applications, in relative isolation, into the public cloud. This worked well for a few years, but as the benefits of putting workloads into the cloud became apparent, bigger scale (and the problems that come with it) needed to be solved. Problems such as redundant equipment across workloads, or missing devices making workloads out of compliance with corporate standards as shown in the diagram below.

[![0]][0]

The diagram shows a case in right corner where the person through the deployment without a firewall that exposes the workload to anyone with potential leaking of corporate data over the internet.

It was the necessity of scale from which the Virtual Data Center (vDC) was born.

The inspiration of this paper is to provide architectural patterns and advice on how a vDC can be designed to be secure, reliable, highly scalable, and cost efficient.

A vDC is not just the application workloads in the cloud, but also the network, security, management, and infrastructure (for example, DNS and Directory Services) and usually a private connection back to an on-premise network or data center. As more and more workloads move to Azure it is beneficial to think about the supporting infrastructure and containing objects that these workloads are placed in, to avoid a proliferation of hundreds of "workload islands" that must be managed separately with independent data flow, security models, and compliance challenges.

By viewing your workloads as an integrated Virtual Data Center, that is to say separate but related entities with common supporting functions, features, and infrastructure; you can realize reduced cost due to economies of scale, optimized security through component and data flow centralization, and easier operations, management, and compliance audits.

>[AZURE.NOTE] It's important to understand that the vDC is **NOT** a discreet Azure product, but the combination of various features and capabilities to meet your exact requirements. vDC is a way of thinking about your workloads and Azure usage to maximize your resources and abilities in the cloud. The virtual DC is therefore a modular approach on how to build up IT services in the Azure, respecting company roles and responsibilities.
 
The vDC can help enterprises get workloads and applications into Azure for the following scenarios:

 - Hosting multiple related workloads
 - Migrating workloads from an on-premise environment
 - Implementing shared or centralized security and access requirements across workloads
 - Mixing DevOps and Centralized IT appropriately for a large enterprise

As it will be discussed more in this and subsequent articles, the key that unlocks the vDC is a centralized topology (hub and spokes) with a mix of Azure features: Azure VNet, NSG,  VNet peering, UDR, and Azure Identity with Role Base Access Control.
As we are going to discuss VNet peering is really the key that unlock the vDC.

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

Identity is a key aspect of every data center on-premises and in the cloud. Any large enterprise needs to define an identity management process that describes the management of individual identities, their authentication, authorization, roles and privileges within or across the vDC with the goals of increase security and productivity while decreasing cost, downtime, and repetitive manual tasks.
Following an approach top-down enterprise business create a demanding of services for the different LOBs, and employees  with different company roles are involved with different responsibilities in deployment and maintenance of projects.
A vDC requires therefore a cooperation between different teams, each with specific role, to get the systems running with good governance. The matrix of responsibilities, access and rights can be therefore extremely complex. The VDC is able to address this complexity by different mechanisms and features. Identity management in Azure resources is implemented through Azure Active Directory (Azure AD) and Role-Based Access Control (RBAC).
The Azure AD is a multi tenant cloud service to manage directory services and identity (an Azure AD stores **users**, **groups** and **services principle**).
A single administrator does not allow to get the work done in a vDC; specific department (group of users or services) need to have the exact permissions. Too many permissions exposes a project to attackers, but too few permissions means that employees can't get their work done efficiently. Azure Role-Based Access Control (RBAC) helps to address this problem, by offering fine-grained access management to Azure resources. 

The vDC is not an isolated island and needs of a connectivity with external networks to offer IT services to customers, partners and/or internal users.
Azure datacenters provide connectivity to internet; customers can build their security polities to control what and how specific vDC services of the different projects are accessible to/from internet by Network Virtual Appliances (with filtering and traffic inspection), custom routing policy and Network Security Groups.
Enterprise need to operate with vDC as in local DC; the connectivity between Azure the on-premises networks is therefore a crucial aspect to build an effective architecture.
Enterprise have two different ways to create interconnection between vDc and on-premises: transit over internet or/and by private connections.
Let's discuss briefly the different connectivity to Azure DCs.
**Azure Site-to-Site VPN**  can be used to create a hybrid solution, a secure encrypted connections tunnels (IPsec/IKE) between your on-premises networks and the vDC with transit in internet. Azure Site-to-Site connection is extremely flexible and have fast deployment time (does not require any procurement effort to order network circuits, because connections transit in internet). 
Azure Site-to-Site VPN can be deployed in multi-sites configuration, with vDC connected with multiple on-premises networks. Azure Site-to-Site VPN is also extremely flexible to have multiple simultaneous connections of offices (headquarters and/or branch offices) to the vDC.
**ExpressRoute** is an Azure connectivity service that lets you create **private connections** between vDC and the on-premises networks. ExpressRoute connections do not go over the public Internet, and offer higher security, reliability and speeds with deterministic low latencies compare with connections over the Internet. ExpressRoute is a **must** to have for a proper effective usage of the vDC. By ExpressRoute customer can get beneficial of high integration with on-premises networks and the satisfaction of compliance rules, associated with private connections.
The deployment of ExpressRoute connection requires an engagement and agreement with ExpressRoute service provider with longer timeframe than Site-to-Site VPN. For customer that need to start quickly, it is quite common to make leverage on Site-to-Site VPN to build up fast the connectivity with vDC, starting in parallel the planning and procurement of the ExpressRoute connection.
ExpressRoute  connection and Azure Site-to-Site VPN are not mutually exclusive, but they can coexist. Having the ability to configure Site-to-Site VPN and ExpressRoute has several advantages; you can configure Site-to-Site VPN as a secure failover path for ExpressRoute, or use Site-to-Site VPNs to connect to sites that are not connected through ExpressRoute.
[https://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-coexist-resource-manager](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-coexist-resource-manager "Configure ExpressRoute and Site-to-Site coexisting connections")
Few customers are used to configure Azure Site-to-Site VPN for management purpose to reach out the deployments in vDC with different path of ExpressRoute connection. The coexistence Site-to-Site VPN and ExpressRoute is not a must of vDC architecture, but only a possible option.

  

##Virtual Data Center Overview
###Topology
A vDC architecture base its consistency on a topology hub and spokes, extended to a single Azure region.

[![1]][1]

The hub is a central zone for the control and inspection of the ingress and/or egress traffic between different zones: internet, on-premises and the spokes. The hub and spokes topology gives to the central IT department an effective way to enforce security policies in central location, while reducing the potential for misconfiguration and exposure.

In the hub reside the common service components consumed by the spokes. Here few typical examples of common central services: 

- the Windows Active Directory infrastructure (with the related ADFS service) required for user authentication of third parties accessing from untrusted networks before getting access to the workloads in the spoke, 
- a DNS service to resolve naming for the workload in the spokes, to access to specific resource on-premises and to internet
- a PKI infrastructure, to implement a single sign-on on workloads
- a flow control (TCP/UDP) between the spokes and Internet
- a flow control between the spoke and on-premises
- in some seldom case, the traffic control between spoke and spoke.

Compare with traditional approach to the DC, the vDC reduce the overall cost through the multiple utilization of shared hub infrastructure between different spokes.
The role of the spokes is to host the different type of workloads. The spokes provide a modular approach of repeatable deployments (e.g. dev and test, UAT, pre-production and production) of the same workloads. Inside a spoke is possible to deploy a basic workload or a complex multi-tier workloads with traffic control between tiers.
In Azure every virtual component (doesn't matter which type is) is deployed in a specific Azure Subscription. The Azure subscription define therefore a natural mandatory boundary to deploy all Azure components (Virtual Networks, custom routing tables (UDR), Network Security Rules, Storage accounts, IaaS and PaaS services, etc.). The property of isolation of Azure components belonging to different Azure subscriptions can be used to satisfy the requirements of different LOBs, setting up the right level of access and authorization. 

A single vDC can scale up easily to large number of spokes; nevertheless as every IT system the the system can reach out a limit present in the platform.
The hub deployment is bound to a specific Azure subscription that have some restrictions and maximum values (e.g. max number of VNet peering-see [https://azure.microsoft.com/en-us/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/en-us/documentation/articles/azure-subscription-service-limits/ "Azure subscription and service limits")); also in these circumstances the architecture is still capable to scale up, extending the model from a single hub-spokes to a cluster of hub and spokes, in the same Azure region.

[![2]][2]

Introduction of multiple hubs determines cost increment and management effort justified only in the case of presence of high  number of spokes. In the vDC the total cost of hub should be marginal compare with the cost of hub, to avoid to come back to the approach described at beginning of the paper with indiscriminate duplication of workload components in Azure.
It is interesting to debate some interconnection cases:

- **interconnection between hubs.** The vDC model is based on hub containing all the common shared services, requested to the spokes. Two hubs should offer the same set of services and for functional prospective, there is no effective benefit to interconnect hubs.
- **interconnection between spokes** (residents in the same hub or different hubs). Inside a single spoke is possible to implement complex multi-tiers workloads. Multi-tier configurations can be implemented using subnets (one for every tier) in the same VNet and filtering the flows by NSGs. A typical example is the case where multiple application servers deployed (under the responsibility of developer team) are in a spoke while the  database is deployed in a different spoke. In this case it is easy interconnect the spokes with VNet peering to avoid transit in the hub.

[![3]][3]

One further point of discussion is the possibility to create spokes interconnected to a spoke that works itself as hub. From topology prospective, the approach increases the level of hierarchy hub-spokes topology as two levels: in this case the spoke in higher level of hierarchy (level 0) become the hub of lower spokes (level 1) of the hierarchy.
The spoke of vDC need to forward the traffic to the central hub to reach out in some cases the on-premises network and internet. An architecture with two level of hub-spoke introduce complex routing that vanish the benefit of simplicity. Azure offers the possibility to implement complex topologies, but one of principle of the vDC is repeatability and simplicity to minimize the management effort; the simple hub-spokes remains at most the vDC reference architecture. 

An vDC architecture can be built properly with resiliency, by duplications and load balancing of the different Azure components, but nevertheless it is not resilient major disaster of Azure region; high resilient services can be created only through multiple vDCs deployed in different Azure regions. 

###Components
A Virtual Data Center (vDC) is made of up four basic component types;  **Infrastructure**, **Perimeter Networks**, **Workloads**, and **Monitoring**. 

Inside each component type we have a variety of Azure features and resources that can be used to make up an instance of that component that match with your specification.

Your vDC will be made up of multiple types of components and in most cases multiple, differently structured, instances of the same component type. For instance you may have many different workloads instances that represent different applications and are logically separated. The idea is that you would use the different component types and instance to ultimately build the vDC.



[![4]][4]

The high level digram of vDC shows the different component types in the different zones and the hub-spokes topology. The diagram shows up the presence of infrastructure in different parts of architecture. 

As good practice (for on-premises DC or vDC) the rights and access are  group-based; the technique to work on groups, instead of "user", help to keep the consistency of the rights across teams and avoid misconfiguration. it is also easy keep up-to-date rights/access in the case a specific person change functional role in the organization or revoke the rights in case leaving the company.
The diagram shows different roles for different groups: 

- the *central IT group* have the ownership to control of the infrastructure components and therefore need to have the role of contributor on the subscription (hub) or network contributor in the spokes. Some large organization are used to split up the management respectabilities of the the hub between different teams: network group (with exclusive focus on networking) and security group (responsible of firewall with internet and traffic control). In this specific case two different groups need to be created with assignment of by custom roles.
- the *dev & test (devops) group* have the responsibility to deploy workloads; this group requires at least the role of Virtual Machine Contributor for IaaS deployment and/or one or more PaaS contributors roles (see [https://azure.microsoft.com/en-us/documentation/articles/role-based-access-built-in-roles/](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-built-in-roles/ "RBAC: Built-in roles") ). Optionally the dev& test team need to get visibility on security policies (NSGs) and routing policies (UDR) inside a specific spoke; in this case further to the roles of contributor for workloads, it gets the role of Network Reader.
- *operation and maintenance group* have the responsibility of production; the group needs to be a subscription contributor on workloads in production subscription. To fix potentially misconfiguration and issue with production, the company might evaluate if assign to an escalation support team group the role of subscription contributor in production and in the central hub subscription.
    
The structure of the vDC need to be partitioned to host multiple projects of the different Line of  Businesses (LOBs). Any project requires different isolated environments (devops, UART, production). The property of isolation of Azure subscriptions result the natural choice to host the environment of a specific project.

[![5]][5]

The diagram reports the relationship between customer's projects, users, groups and environments where the Azure components are deployed.
Typically in IT, an environment (or tier) is a  system in which multiple applications are deployed and executed. The large enterprise use the development environment (where changes are originally made) and production environment (what end users use) and those environments are separated, often with several stages in between, in order to allow phased deployment (rollout), testing, and rollback in case of problems. Deployment architectures vary significantly, but broadly, how the tiers are bookended by starting at development (DEV) and ending at production (PROD). 
A common tier architecture is devops (development and testing), UAT (staging), production.
Organization can make leverage on single or multiple Azure AD tenants to define access and rights to an environment. The diagram shows the case where two different Azure AD tenants are used: one for devops and UAT and the other exclusive for production. The presence of different Azure AD tenant enforce the separation between environments. The same group of users (e.g. Central IT) need to authenticate with different URI to different AD tenant to get access to devop or production environments of a specific project. The presence of different user login to access to different environments, reduce possible outage caused by human errors.

There are few simple cases can be discussed to understand the implications:

- every environment (e.g. devops) can share the same Azure subscription between different projects of the same LOB. The approach help to reduce the number of Azure subscriptions, but do not avoid potential human error due to wrong selection of the resources in the Azure subscription, e.g. wrong selection of the tier that might accidentally cause a wrong action on the project due to human mistakes.   
- every environment (e.g. UAT) of a project is created in a specific Azure subscription. In this case there is no sharing of Azure subscription between different projects and the different environments have a strong demarcation. If the number of projects is high, the approach is potentially prone to a proliferation of Azure subscriptions in vDC. (e.g. in company with 5 LOBs and 4 projects, each with 3 different environments -dev&Test,UART,prod-, requires 5*4*3=60 subscriptions).
- more common scenario is where Central IT and PROD group are the same for every LOB and devops is arranged in sub-teams based on type of workload. In this case some Azure subscriptions can be shared across a list of LOBs.

In all scenarios, and in particular in all production deployments, is strongly recommended to lock down the resource group to avoid human mix-up and consequent accidental deletion of resources.



####Component Type: Infrastructure
This is where major supporting infrastructure will reside, it's also where your centralized IT, Security, and/or Compliance teams will spend most of their time.

Feature list included in Infrastructure:

- Identity & Directory Services					(Green)
- Virtual Network, UDR, NSG and VNet peering	(Green)
- DNS											(Red)
- Subscription and Resource Group Management	(Yellow)
- RBAC Strategy									(Red)

[![6]][6]

The Infrastructure is pervasive mandatory component spanning in different parts of the architecture and provides the interconnection between different components of the vDC: it is present in the hub and in the spokes. The responsibility to manage and maintenance the infrastructure components are typically assigned to the central IT and security team.
 
One of primary tasks of IT infrastructure team is to guarantee the consistency of IP address schema across the enterprise: the private IP address space assigned to the vDC needs to be consistent and NOT in overlapping with private IP addresses assigned on-premises networks.
Usage of NAT on the on-premises edge routers (or in Azure), to avoid IP address conflicts, it is not a recommended solution and it should be avoid because cause headache and usefulness complexities, breaking up one of the main criteria of vDC: simplicity of the management.

Let's describe briefly the different components included in the infrastructure. 

- **Identity and directory services**. Access to every resource type in Azure is controlled by identity stored in directory service. The directory service store not only the list of users, but also the right to access to a resources in a specific Azure subscription. The identity can live only in the cloud or can be synchronized with on-premises identity in Active Directory structure.
- **Virtual Network**. Virtual Network is the main components of the infrastructure composed by a single or multiple virtual network segments, each with a specific IP network prefix.  
Virtual Network is mandatory component of the vDC. The Virtual Network define a internal perimeter area where IaaS virtual machines and PaaS services can establish a private communication.  
- **UDR**. User Define Route is a custom routing table that network administrator can associate to one or more subnets to overwrite the behaviour of system table behaviour. If UDR is an optional component in some specific scenarios, it becomes a fundamental component in the vDC. The presence of UDRs guarantee that egress traffics from the spoke transit through Network Virtual Appliances and load balancers present in the hub and in the spokes.
- **NSG**. In Azure the TCP/UDP flows control is nativity implemented through Network Security Group (NSG). A Network Security Group is a list of security rules that act as traffic filtering; each rule is based on a tuple of 5 values: IP Source, IP Destination, Protocol, IP Source Port and IP Destination port. One powerful expect of NSG is the stateful nature of filtering mechanism: when a rule is created to allow to establish the flow, the flow related to the answer is automatically accepted. This property of NSG help to reduce the list of entries in the security policy. The NSG can be applied to a subnet or a Virtual NIC card of an Azure VM or both. To have an agile management of security policies in vDC is recommended to make leverage of NSGs applied to subnets: this avoid a proliferation of NSGs with possibility to reuse the same NSG for different deployments (dev and test, UART, pre-production and production). The NSGs are essential to implement a correct flow control in the hub and in the spokes.
- **DNS**. If not specified, the VNet uses an default Azure DNS (assigned automatically from Network Resource Provider) to resolve naming inside the VNets. The scope of default DNS is limited to the VNet. Name resolution is therefore not extended to the VNet in peering neither VNet-to-VNet. The default DNS associate to the VNets normally doesn't fit with customer requirements. In most of cases, a custom DNS service need to be deployed in the hub as part of common services. The main consumers of DNS reside in the spoke. An example of name resolution required for a correct implementation of the workload is the case of a two tiers application with a web front that need to communicate with a SQL server in backed by ALIAS.
- **Subscription and Resource Group Management**. A subscription defines a natural boundary to create multiple type of resources in Azure. A Resource Group is a logical container inside a subscription, that define a set of mix resources. The nature of Resource Group is pure administrative and does not influence how the different components, defined in it, are assembled together. The Resource Group has therefore a pure administrative nature and need to be used in vDC to reflect the logical partition of multiple resources and rights. When a Resource Group are deleted all the associated resource will be removed.Resource Group Management is therefore a natural boundary of administration of a set of Azure components.
- **RBAC**. As described previously in the article, through RBAC is possible map company role with rights to access to the Azure resources. 
- **VNet peering**. The fundamental component to create the infrastructure of a vDC is the VNet peering, a mechanism that connects two virtual networks (VNets) in the same region through the Azure backbone network (see [https://azure.microsoft.com/en-us/documentation/articles/virtual-network-peering-overview/](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-peering-overview/ "VNet peering")
After two virtual networks are peered, virtual machines attached to the virtual networks have full IP-level connectivity.The VNet peering have some key aspects:

	- The two virtual networks that are peered need to belong to the same Azure region. The regional extension of VNet peering impose the boundary of a vDC to a single region. 
	- The virtual networks that are peered do not have to overlap IP address spaces.
	- VNet peering is between two virtual networks is not a transitive relationship. The intransitive nature of the VNet peering define the hub-spoke single level as the right architecture to pursue.
	- peering can be established between virtual networks in two different subscriptions; the hub can be therefore be deployed in Azure subscription different from a spoke (as highlight in the diagram of the infrastructure).
	- traffic filtering between VNet peers is implemented by NSGs. To keep the security tasks easy to control, the traffic in egress and ingress to the spokes can be controller through NSGs applied to subnets of the hub. the fine traffic control inside the spoke works by NSGs applied to subnets/NICs in the spoke. A classical example of NSGs working in the spoke is for a three tier application with a web front-end in subnet1, an application layer (mid-layer) deployed in the subnet2 and a back-end layer to host the DBs in subnet3 of the spoke.    
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

[![7]][7]

The diagram enforce two perimeters with internet and on-premises network, both resident in the hub.
The perimeter network with internet is normally managed through firewalls and Web Application Firewalls (WAFs). 
In the LOBs there is a predominant existence of web applications, it tends to be affected by a lot of different types of vulnerabilities and exploits. 
Web Applications Firewalls are a special breed of product used to detect attacks against web applications (HTTP/HTTPS) in more depth than generic firewall. Compare with tradition firewall technology, WAFs have a set of features to protect internal web servers from malicious traffic (i.e. SQL injections, cross-site scripting, session hijacking, parameter or URL tampering, buffer overflows, malware uploads and application DDoS). WAFs are typically deployed in some sort of proxy fashion just in front of the web applications, so they do not see all traffic on our networks. By monitoring the traffic before it reaches the web application, WAFs can analyse requests before passing them on.
WAFs not only detect attacks that are known to occur in web application environments, they also detect (and can prevent) new unknown types of attacks. By watching for unusual or unexpected patterns in the traffic they can alert and/or defend against unknown attacks. For example- if a WAF detects that the application is returning much more data than it is expected to, the WAF can block it and alert to security analyst.
In the vDC, one expect to consider in selection of WAF technology is the capability to reduce false positive. The false positives are very bad, because they make already busy security analysts even busier. An overload of false positives can delay response to actual attacks or cause attacks to get accepted as normal because of an analyst trying to reduce the noise. 
For enterprise that need to protect web traffic from the top common web vulnerabilities,  the Azure Application Gateway is a good cost effective options. For more sophisticate protection, customer can access to a large number of WAF solutions offered by different vendors available in Azure marketplace. 
Most of them offer the capability to scale up horizontally running on larger IaaS VMs, to serve a more higher traffic volume.
For resiliency, it is always recommended the utilization at least two Virtual Appliances in the same availability set to guarantee a reasonable SLA in production.
In a single hub, the perimeter network to internet scales up to large number of LOBs, using multiple farm of WAFs and/or firewalls.

Traffic control from/to perimeter networks can be enforced by one of more firewall farms; a firewall farm is group of firewalls working in tandem under the same common administration with a set of security rule to protect the workloads in the spokes and guarantee the right control of access to on-premises networks. a firewall farm have less specialized software compare with WAF, but has a broad application scope. firewall born to filter and inspect any type of traffic in a serverfarm. Firewall farm are normally implemented in azure through Network Virtual Appliances (NVAs) available in Azure marketplace offering.
Azure marketplace offer a wide range of firewall brands, with possibility for customers to recycle the expertise acquired with on-premises datacenters.

A load balancer in the from of firewall farm help to distribute of egress connections between different firewall, scaling up in throughput.
One technology expect customer might investigate is on the support of high availability (HA) on specific brand of the NVA. Every firewall vendor have (or not) implemented a mechanism in which two firewalls are placed in a group and their configuration is synchronized to prevent a single point to failure on the perimeters network. Normally, a heartbeat connection between the firewall peers ensures seamless failover in the event that a peer goes down. Setting up the firewalls in firewall pairs provides redundancy and allows you to ensure business continuity. The firewalls in an HA pair use dedicated or in-band ports on the firewall to synchronize data—network, object, and policy configurations—and to maintain state information.
 
The firewall in NVA can support stateful active/passive or active/active high availability with session and configuration synchronization. 
firewalls support two HA modes: 
**Active/Passive**— One firewall actively manages traffic while the other is synchronized and ready to transition to the active state, should a failure occur. In this configuration, both firewalls share the same configuration settings, and one actively manages traffic until a path, link, system, or network failure occurs. When the active firewall fails, the passive firewall takes over seamlessly and enforces the same policies to maintain network security.
**Active/Active**— Both the firewalls in the pair are active and processing traffic, and work synchronously to handle session setup and session ownership. In some firewall brands the active/active deployment is only recommended for networks with asymmetric routing.

Some NVAs in Azure do not have yet the support for HA, but still with this limitation the NVAs can be applied to the deployment; the only drawback of missing HA in firewall is the reset of connections, caused from a outage of one firewall, due to miss synch up of connections tables to the firewalls in pair.
For some workloads as e-commerce application, a NVA firewall with HA support is mandatory; a failure in firewall can cause a lost on electronic transactions and/or lost of content in the basket before the end-user can finalize the order. In this case customer should aim to deploy a firewall with HA support in Azure.
 
To reduce the potential vulnerabilities of the system, some customers are used to implement this layer through  Network Virtual Appliances (NVAs) of different vendors.
The presence of multiple DMZ zones, where the incoming traffic is inspected in sequence with NVAs of different brands, do not provide a better security policy,  but reduce the vulnerability risk of the overall system. In this case the traffic is analysed in cascade from different software of independent vendors and reduce potential exposure of potential vulnerabilities related to the software stack of a specific firewall vendor (a missing patch in the Virtual Appliance might cause an exposure to malicious threats). 
The decision to use multiple NVAs to create different DMZ needs to be evaluated on analysis of security benefits vs operational cost associated with complexity of the management (expertise on two different firewall vendors has the drawback to increase the operational costs to manage different technologies).


[![8]][8]

Most of large enterprise manage multiple domains; the Azure DNS can be used to host the DNS records for a particular domain. As example, the Virtual IP Address (VIP) of the Azure external load balancer (or the WAFs) can be registered in the A record of Azure DNS. 	

The Central IT can effectively build an hub-spoke type environments, where the hub can host infrastructure components such as network virtual appliances to filter the traffic in ingress and in egress to internet.
In the hub the distribution of incoming traffic in the virtual appliances is normally implemented through an external load balancer.
To guarantee that traffic generated from the VMs resident in the spoke transit in the Virtual appliances, a UDR needs to be set in the subnets of the spoke by setting as next-hop the front-end IP address of the internal load balancer that distribute the internal traffic to the Virtual Appliances (load balancer back-end pool).

[![9]][9]

Most of LOBs requires an integration with few services on-premises; a typical example is the Windows AD. For large vDC the ExpressRoute is the right solution to interconnect Azure with on-premises datacenter, for the nature of private link and also for the deterministic delay and latency associated with the connection and high throughput (10Gbps throughput with ulta-performance gateway) required for large deployments. Provisioning of the ExpressRoute configuration in Azure can be done in few minutes, but the end-to-end provisioning requires a purchase order to the network connectivity service provider to establish the interconnection between the customer network/datacenter with the Microsoft backbone. The provisioning process can requires weeks and to speed up the cloud adoption of Azure, enterprises can decide to deploy in the hub an Azure gateway type VPN to implement a site-to-site VPN. The Azure site-to-site VPN creates an IPSec tunnel between the on-premises VPN concentrator and the Azure Gateway, with possibility to exchange the network prefixes dynamically via BGP. Azure site-to-site VPN provides an effective way to kick-off the vDC, with integration with on-premises services. The site-to-site VPN is not the definitive solution for the vDC: the transit happen in internet, and the cap value on throughput does not allow large traffic exchange with the on-premises datacenter and deterministic latency.
When the ExpressRoute will be successful provisioned, customer can decide to decommission the Site-to-Site VPN or keep the consistence with ExpressRoute, using the Site-to-Site VPN as backup path (higher metric) with on-premises site. 

####Component Type: Monitoring
This is where you have visibility and alerting from all of the above components. All related teams should have access to their components and services. If you have a centralized help desk or operations teams, they would be integrated to this data.

Feature list included in Monitoring:

- Audit logs					(Red)
- Event logs					(Red)
- Data path logs				(Red)
- OMS							(Red)

Azure offers plenty of different type of logs to track the Azure platform behaviour.
Governance and control of workloads in azure is based on capabilities to collect different type of logs, but also trigger actions based on specific reported events. 

The **Audit Logs** (referred also as "Operational Log") provide insight into the operations that were performed on resources in your subscription. The log reports the control-plane events for your subscriptions. Every Azure resource produces audit logs.
The **Azure Diagnostic Logs** are logs emitted by a resource that provide rich, frequent data about the operation of that resource. The content of these logs varies by resource type.

Central IT and security teams are more focus on log analyse the logs related to the traffic flows.

[![10]][10]

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

Unfortunately most of applications in the datacenter, especially legacy applications, are not stateless and require the ability to record user session activity, making some aspects of the application stateful. This type of application are not written to work in the cloud and requires an extra effort to understand how to guarantee the persistence of their state.


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

So far the discussion has been focus on single vDC; the article has described the different basic components and architecture that concur to create a resilient vDC. Some Azure features as Azure load balancer, NVA in Azure marketplace, Availability Set,  Scale Set an many others mechanisms can contribute to run a system that overall have an acceptable SLA for production, minimizing potential failures and impact on the business. A single vDC has a regional extension and it is not resilient to a seldom major outage that might happen inside the specific region. Customers that want to achieve high SLAs need to protect own services through deployments of the same project in two (or more) vDCs, place in different regions.
Azure datacenters are present in numerous ww regions; in selection of multiple Azure datacenters customers need to consider few factors. One element to consider in selection of different Azure DCs is the Proximity between the vDC and location of end users (internal or external), to offer the best user experience.  Selection of vDCs need to be conform with compliance and regulatory established from the local industries/government that the company have embraced.
One other challenge of multi vDC architecture is the synchronization between workloads located in different vDC. Ideally customers want to synchronize application data  between deployments running in two different vDC,to implement a fast failover mechanism between vDC. Most of applications are sensitive to latency, that can cause potential timeout and delay in data synchronization. Alignment of data in synch mode is a challenge also when the data churn is high and forbidden the commitment of transactions logs to the remote vDC. 
Synchronization of applications in different vDCs require an intercommunication between vDCs. 
Two vDCs in different regions can be connected through the ExpressRoute private peering when the related connections are bind to the same ExpressRoute circuit. One other possibility to interconnect vDC is through VNet-to-VNet. 
In most of cases the Expressroute connection might the preferred  mechanism due higher bandwidth and low latency with transit through the Microsoft backbone. 
There is no magic recipe to validate a generic  application distributed between two (or more) different vDCs located in different regions; customer should run a network qualification test to verify the latency and bandwidth and therefore aim the approach more appropriate with synchronous or asynchronous replication.
One of well know to implement diversion of traffic between vDCs is through Azure Traffic Manager. Azure Traffic Manager uses the Domain Name System (DNS) mechanism to direct end-user requests to the most appropriate public endpoint in specific vDC. Through probes, Traffic Manager track periodically the health of public endpoints in different vDC and, in case of failure of the endpoints, it routes automatically the connections on secondary vDC. Traffic Manager is resilient to failure of an entire Azure region and allows to control the distribution of user traffic for service endpoints in different vDCs based on different criteria (as failure of a service in a specific vDC or selection of vDC with the lowest network latency for the client). Service endpoints supported by Traffic Manager include Azure VMs and Web Apps, but it can be used only to control/divert traffic to Azure public endpoints.



### Conclusion
vDc is an approach to the datacenter migration in the cloud with a combination of various features and capabilities to create a scalable architecture in Azure that maximize the cloud resources, reducing the cost and simplifying the governance of the system.  The vDC is based on topology hub-spokes with common shared services resident in the hub and the specific applications/workloads associated with a LOB in a spoke. A vDC match the structure of company roles, where different departments (Central IT, devops, operation and maintenance) work together each with a specific segregation of duty. A vDC satisfy the requirements of "lift and Shit", but also of project that nativity born in the cloud.
 

<!--Image References-->
[0]: ./media/best-practice-vdc/redundant-equipment.png "Example of component overlap" 
[1]: ./media/best-practice-vdc/vdc-high-level.png "High level example of hub and spoke vDC"
[2]: ./media/best-practice-vdc/hub-spokes-cluster.png "cluster of hub and spoke"
[3]: ./media/best-practice-vdc/spoke-to-spoke.png "spoke-to-spoke"
[4]: ./media/best-practice-vdc/vdc-block-level-diagram.png "block level diagram of the vDC"
[5]: ./media/best-practice-vdc/users-groups-subsciptions.png "users, groups, subscriptions and projects"
[6]: ./media/best-practice-vdc/infrastructure-high-level.png "high level infrastructure diagram"
[7]: ./media/best-practice-vdc/highlevel-perimeter-networks.png "high level infrastructure diagram"
[8]: ./media/best-practice-vdc/firewall-farms-cascade.png "firewall farms of different vendor in cascade"
[9]: ./media/best-practice-vdc/vnet-peering-perimeter-neworks.png "VNet peering and perimeter networks"
[10]: ./media/best-practice-vdc/high-level-diagram-monitoring.png "high Level diagram for Monitoring"

<!--Link References-->
[Example1]: ./virtual-network/Network-Boundary-DMZ-NSG-ASM1.md


