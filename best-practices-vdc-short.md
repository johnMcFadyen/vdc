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

In right corner of the diagram we see a case where the administrator through the deployment without a firewall that exposes the workload to anyone with potential leaking of corporate data over the internet.

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

As it will be discussed more in this and subsequent articles, the key that unlocks the vDC is a centralized topology (hub and spokes) with a mix of Azure features: Azure VNet, NSG,  VNet peering, UDR, and Azure Identity with Role Base Access Control (RBAC).
As we are going to discuss VNet peering is really the key that unlock the vDC.

##Who Needs a Virtual Data Center?
Any Azure customer that needs to move in excess of two to three workloads into Azure can benefit from thinking about common resources to be utilized. Given sufficient size, even single applications can benefit from using the patterns and components used to build a vDC.

If your organization has a centralized IT, Network, Security, and/or Compliance team/department, a vDC can help enforce policy points, segregation of duty, and ensure uniformity of the underlying common components while giving the application teams as much freedom and control as is appropriate for your requirements.

Organizations that are looking to DevOps can utilize the vDC concepts to provide authorized pockets of Azure resources and ensure they have total control within that group (either subscription or resource group in a common subscription), but the network and security boundaries stay compliant as defined by a centralized policy in a hub VNet and Resource Group.

##Considerations on Implementing a Virtual Data Center
There some pivotal aspects to considered when designing a vDC:

 - Identity and Directory Services
 - Security infrastructure
 - Connectivity to the cloud
 - Connectivity within the cloud


#####Identity and Directory Service
Identity and Directory services are a key aspect of every data center on-premises and in the cloud. Security Identity is related to the aspect of access and authorization to the services of the vDC.To help ensure that only authorized users and processes access your Azure Account and resources, Azure uses several types of credentials for authentication. 
These include passwords (to access to the Azure account), cryptographic keys, digital signatures, and certificates.
*Azure Multi-Factor Authentication* (MFA) is an additional layer of security for accessing Azure services. When enabled the authentication of user is required not only with username and password, but also you can require users to verify their sign-ins via mobile app, phone call, or text message.

Any large enterprise needs to define an identity management process that describes the management of individual identities, their authentication, authorization, roles and privileges within or across the vDC with the goals of increase security and productivity while decreasing cost, downtime, and repetitive manual tasks.
Following an approach top-down enterprise business create a demanding of services for the different LOBs, and employees  with different company roles are involved with different responsibilities in deployment and maintenance of projects.
A vDC requires therefore a cooperation between different teams, each with specific role, to get the systems running with good governance. The matrix of responsibilities, access and rights can be therefore extremely complex. Identity management in vDC is implemented through Azure Active Directory (Azure AD) and Role-Based Access Control (RBAC).

A Directory Service is a shared information infrastructure for locating, managing, administering and organizing everyday items and network resources, which can include volumes, folders, files, printers, users, groups, devices and other objects. Each resource on the network is considered an object by the directory server. Information about a particular resource is stored as a collection of attributes associated with that resource or object. All Microsoft online business services rely on Azure Active Directory (AAD) for sign-in and other identity needs.
Azure Active Directory is a comprehensive, highly available identity and access management cloud solution that combines core directory services, advanced identity governance, and application access management. AAD can be integrated with on-premises Active Directory to enable single sign-on for all cloud-based applications. The user attributes of on-premises Active Directory can be automatically synchronised to AAD.
A single administrator does not allow to get the work done in a vDC; specific department (group of users or services in the Directory Service) need to have the exact permissions. Too many permissions exposes a project to attackers, but too few permissions means that employees can't get their work done efficiently. Azure Role-Based Access Control (RBAC) helps to address this problem, by offering fine-grained access management to vDC resources. 

#####Security Infrastructure
Security concerns many expects and components of the vDC. Here we are going to focus only on some specific security aspects related the security infrastructure of the vDC, without pretend to discuss the security aspects related to the Azure platform.
Security infrastructure is mainly related to the segregation of traffic in specific virtual network segment and control of ingress and egress flows control of flow to the vDC. Azure is based on multitenant architecture that prevents unauthorized and unintentional traffic between deployments, using Virtual Network (VNet) isolation, access control lists (ACLs), load balancers, and IP filters, along with traffic flow policies; network address translation (NAT) separates internal network traffic from external traffic. The Azure Fabric Controller allocates infrastructure resources to tenant workloads and manages  communications from/to virtual machines (VMs). The Azure hypervisor enforces memory and process separation between VMs and securely routes network traffic to guest OS tenants. 

#####Connectivity to the cloud
The vDC is not an isolated island and needs of a connectivity with external networks to offer services to customers, partners and/or internal users. A vDC requires a connectivity with internet but also a connectivity with on-premises networks/DCs.  
Customers can build their security polities to control what and how specific vDC services of the different projects are accessible to/from internet by Network Virtual Appliances (with filtering and traffic inspection), custom routing policy and network filtering (Network Security Groups).
Enterprises need to operate with a vDC as a local DC; the connectivity between Azure the on-premises networks is therefore a crucial aspect to build an effective architecture.
Enterprise have two different ways to create interconnection between vDc and on-premises: transit over internet or/and by private connections.
An **Azure Site-to-Site VPN** is an interconnection service over internet between on-premises networks and the vDC established  by secure encrypted connections (tunnels IPsec/IKE). Azure Site-to-Site connection is flexible, fast to deliver and does not require any further procurement, because all the connections transit in internet. 
**ExpressRoute** is an Azure connectivity service that lets you create private connections between vDC and the on-premises networks. ExpressRoute connections do not go over the public Internet, and offer higher security, reliability and high speeds (10Gbps with Azure Utra-performance gateway) with deterministic low latency. ExpressRoute is a **must** to have for a proper effective usage of the vDC. By ExpressRoute customers can get beneficial of compliance rules, associated with private connections.
The deployment of ExpressRoute connection requires an engagement and agreement with an ExpressRoute service provider which requires a longer timeframe than Site-to-Site VPN. For customer that need to start quickly, it is quite common to adopt the Site-to-Site VPN to build up fast the connectivity with vDC, still the fulfilment of the ExpressRoute connection.

#####Connectivity within the cloud 
It is related to connectivity between the different components of the vDC. VNet and VNet peering are the basic connectivity services in the vDC. A VNet guarantees a natural boundary of isolation and VNet peering allows intercommunication between VNets of the vDC.
Traffic control inside a VNet and between VNets need to match with a set of security rules specified through Access Control Lists (Network Security Group), Network Virtual Appliance and custom routing tables (UDR).


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


####Component Type: Infrastructure
This is where major supporting infrastructure will reside, it's also where your centralized IT, Security, and/or Compliance teams will spend most of their time.

[![6]][6]

The Infrastructure is pervasive mandatory component spanning in different parts of the architecture and provides the interconnection between different components of the vDC: it is present in the hub and in the spokes. The responsibility to manage and maintenance the infrastructure components are typically assigned to the central IT and security team.
 
One of primary tasks of IT infrastructure team is to guarantee the consistency of IP address schema across the enterprise: the private IP address space assigned to the vDC needs to be consistent and NOT in overlapping with private IP addresses assigned on-premises networks.
Usage of NAT on the on-premises edge routers (or in Azure), to avoid IP address conflicts, it is not a recommended solution and it should be avoid because cause headache and usefulness complexities, breaking up one of the main criteria of vDC: simplicity of the management.

Let's describe briefly the different components included in the infrastructure. 

- **Identity and directory services**. Access to every resource type in Azure is controlled by identity stored in directory service. The directory service store not only the list of users, but also the right to access to a resources in a specific Azure subscription. The identity can live only in the cloud or can be synchronized with on-premises identity in Active Directory structure.
- **Virtual Network**. Virtual Network is one of main component of the vDC enables you to create a traffic isolation boundary on the Azure platform. A Virtual Network is composed by a single or multiple virtual network segments, each with a specific IP network prefix (subnet).  The Virtual Network define a internal perimeter area where IaaS virtual machines and PaaS services can establish a private communication. VMs (and PaaS services) in one virtual network cannot communicate directly to VMs (and PaaS services) in a different virtual network, even if both virtual networks are created by the same customer. Isolation is a critical property that ensures customer VMs and communication remains private within a virtual network. 
- **UDR**. Traffic in a Virtual Network is routed by default on system routing table. User Define Route is a custom routing table that network administrator can associate to one or more subnets to overwrite the behaviour of system routing table a define a communication path within a virtual network. The presence of UDRs guarantee that egress traffics from the spoke transit through specific custom VMs and/or Network Virtual Appliances and load balancers present in the hub and in the spokes.
- **NSG**. A Network Security Group is a list of security rules that act as traffic filtering on IP Source, IP Destination, Protocol, IP Source Port and IP Destination port to apply a separation of duties.  The NSG can be applied to a subnet or a Virtual NIC card of an Azure VM or both. The NSGs are essential to implement a correct flow control in the hub and in the spokes. The level of security afforded by the NSG is a function of which ports you open, and which purpose. A good traffic management and security design are still required a control per-VM basis. Customer should apply additional per-VM filters with host-based firewalls such as IPtables or the Windows Firewall. 
- **DNS**. The resolution naming of services in the VNets of the vDC is provided through DNS. The scope of naming resolution of the default DNS is limited to the VNet. In most of cases, a custom DNS service need to be deployed in the hub as part of common services. The main consumers of DNS reside in the spoke. if required, customer can create a hierarchical  DNS structure with delegation of DNS zones to the spokes. 
- **Subscription and Resource Group Management**. A subscription defines a natural boundary to create multiple type of resources in Azure. Resource in subscription can be assembly together in a logical container named Resource Group. The Resource Group represents a logical group to threat the resources of the vDC.
- **RBAC**. Through RBAC is possible map company role with rights to access to the Azure resources. Instead of giving everybody unrestricted permissions in your Azure subscription or resources, you can allow only certain actions. By RBAC, you can grant access by assigning the appropriate role to users, groups, and applications at a certain scope. The scope of a role assignment can be an azure subscription, a resource group, or a single resource. RBAC has a property of inheritance on Azure resources: a role assigned at a parent scope also grants access to the children contained within it. This is help a lot to maintain the structure of grants of the vDC under control.
Using RBAC, you can segregate duties and grant only the amount of access to users that they need to perform their jobs done. For example, use RBAC to let one employee manage virtual machines in a subscription, while another can manage SQL DBs within the same subscription.
- **VNet peering**. The fundamental component to create the infrastructure of a vDC is the VNet peering, a mechanism that connects two virtual networks (VNets) in the same region through the network of the Azure datacenter  (see [https://azure.microsoft.com/en-us/documentation/articles/virtual-network-peering-overview/](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-peering-overview/ "VNet peering")
 


####Component Type: Perimeter Networks
This is where network connectivity back to you on-prem or physical data center networks, and connectivity to and from the Internet will reside. It's also where your network, and perhaps security, teams will spend most of their time.
The incoming packets should flow through the security appliances, such as the firewall, IDS, and IPS, before reaching the back-end servers in the spokes. Internet-bound packets from the workloads can also flow through the security appliances in the perimeter network for policy enforcement, inspection, and auditing purposes, before leaving the network.

Feature list included in Perimeter Networks:

- Virtual Networks, UDR, NSG
- Network Virtual Appliance
- Load Balancer
- Public IPs

The central IT and security team have the ownership of definition of requirements, operation and of the perimeter networks. 

[![7]][7]

The diagram enforce two perimeters with internet and on-premises network, both resident in the hub.
In a single hub, the perimeter network to internet scales up to large number of LOBs, using multiple farm of WAFs and/or firewalls.

**Virtual Networks**
The hub is typically build on a VNet with multiple subnets, to host the different type of services to filter and inspect the traffic from/to internet via NVAs, WAFs, Azure Application Gateways. 

**UDR**
By UDR customers can deploy a firewall, IDS/IPS, and other virtual appliances, and route network traffic through these security appliances for security boundary policy enforcement, auditing, and inspection. UDRs can set in the hub and in the spokes to guarantee that traffics transit through specific custom VMs, Network Virtual Appliances and load balancers. To guarantee that traffic generated from the VMs resident in the spoke transit in the Virtual appliances, a UDR needs to be set in the subnets of the spoke by setting as next-hop the front-end IP address of the internal load balancer, that distribute the internal traffic to the Virtual Appliances (load balancer back-end pool).

[![8]][8]


**Network Virtual Appliances**
In the hub the perimeter network with internet is normally enforced through a farm of firewalls and/or Web Application Firewalls (WAFs). 
In the LOBs there is a predominant existence of web applications, which tends to be affected by a lot of different types of vulnerabilities and exploits. Web Applications Firewalls are a special breed of product used to detect attacks against web applications (HTTP/HTTPS) in more depth than generic firewall. Compare with tradition firewall technology, WAFs have a set of specific features to protect internal web servers from threats. 
A firewall farm is group of firewalls working in tandem under the same common administration, with a set of security rule to protect the workloads in the spokes and guarantee the right control of access to on-premises networks. A firewall farm have less specialized software compare with WAF, but has a broad application scope to filter and inspect any type of traffic in egress and ingress. Firewall farm are normally implemented in azure through Network Virtual Appliances (NVAs) available in Azure marketplace offering.
As good practice is recommended to use one set of NVAs for traffic originating on the Internet, and another for traffic originating on-premises. Using only one set of NVAs for both is a security risk, because it provides no security perimeter between the two sets of network traffic. Using separate NVAs reduces the complexity of checking security rules, and makes it clear which rules correspond to each incoming network request. One set of NVAs implements rules for Internet traffic only, while another set of NVAs implement rules for on-premises traffic only.

Most of large enterprise manage multiple domains; the Azure DNS can be used to host the DNS records for a particular domain. As example, the Virtual IP Address (VIP) of the Azure external load balancer (or the WAFs) can be registered in the A record of Azure DNS. 	

**Azure Load Balancer**
Azure load balancer offers an high availability Layer 4 (TCP,UDP) service to distribute incoming traffic among instances of services defined in a load-balanced set, based on different type of algorithms.
The traffic incoming in the load balancer from front end endpoints (public IP endpoints or private IP endpoints) can be redistributed with or without address translation to a set of back-end IP address pool (e.g Network Virtual appliances or VMs).
Azure Load Balancer can probe the health of the various server instances. When a probe fails to respond, the load balancer stops sending new connections to the unhealthy instances. In the vDC we have the presence of external load balancer in hub, for example to balance the traffic to NVAs, or in the spokes to balance the traffic between different VMs of multitier application.


**Public IPs**
Some Azure features enable to associated via service endpoints a public IP address that allows to your resource to be routed to internet.
The endpoint uses Network Address Translation (NAT) to route traffic to the internal address and port on the Azure virtual network. This path is the primary way for external traffic to pass into the virtual network. The Public IP addresses are configurable to determine which traffic is passed in, and how and where it's translated on to the virtual network.
Most of large enterprise manage multiple domains; the Azure DNS can be used to host the DNS records for a particular domain. As example, the Virtual IP Address (VIP) of the Azure external load balancer (or the WAFs) can be registered in the A record of Azure DNS. 	

####Component Type: Monitoring
This is where you have visibility and alerting from all of the above components. All related teams should have access to their components and services. If you have a centralized help desk or operations teams, they would be integrated to this data.

Azure offers plenty of different type of logs to track the Azure platform behaviour.
Governance and control of workloads in azure is based on capabilities to collect different type of logs, but also trigger actions based on specific reported events. 

There are two major type of logs in Azure:

- the **Audit Logs** (referred also as "Operational Log") provide insight into the operations that were performed on resources in the Azure subscription. The log reports the control-plane events for your subscriptions. Every Azure resource produces audit logs.
- the **Azure Diagnostic Logs** are logs emitted by a resource that provide rich, frequent data about the operation of that resource. The content of these logs varies by resource type.

[![9]][9]

In the vDC is extremely worth track the NSGs logs:

- **Event logs**: the log provides information on what NSG rules are applied to VMs and instance roles based on MAC address. 
-  **Counter logs**: the log tracks how many times each NSG rule was hit to deny or allow traffic.

As general approach all logs can be stored in Azure Storage Accounts for audit, static analysis, or backup. When the logs are stored in the Azure storage account, customer can use different type of frameworks to retrieve, prep, analyse and visualize data to report the status and healthy of cloud resources.
Large enterprise have already acquired a standard framework for the monitor of the on-premises system can extend the framework to track the logs in cloud deployments.
For customer that wish to keep all the logging in the cloud, Microsoft Operations Management Suite (OMS) is a great choice. Since OMS is implemented as a cloud-based service, you can have it up and running quickly with minimal investment in infrastructure services. As further element, OMS can integrate with System Center components such as System Center Operations Manager to extend your existing management investments into the cloud.
OMS Log analytics is a component of OMS framework help to collect, correlate, search, and act on log and performance data generated by operating systems, applications, infrastructure cloud components. It gives to customer real-time operational insights using integrated search and custom dashboards to analyse all the records across all of your workloads in vDCs. 


####Component Type: Workloads
This is where your actual application will reside, it's also where your application development teams will spend most of their time.

Feature list included in workloads:

- Internal LOB Applications
- Customer facing web sites
- Big Data/Analytics
- Events and Messaging

**Internal LOB Applications**
A line of business application is one of set of critical computer application that are vital to running an enterprise. LOB application have some characteristics:
- *interactive*. LOB applications are interactive by nature: data is entered and result/reports are returned.
- *data driven*. LOB application are data intensive with frequent access to the database
- *integrated*. LOB applications offer integrated with other systems as data feeders within or outside the organization.
- *stateless of stateful*. When users or services interact with an application they perform a series of interactions that set a session. A stateless application is an application that does not need of any knowledge of previous interactions and does not store session information. A stateless application can scale easily since any request can be serviced by any of the available pool resources.
In stateless application there is no data shared between resources. A mechanism of load balancing can distribute the load between available resources; when the system in under intensive load, the system can simply add more capacity as needed. When that capacity is not longer required, few resources in the pool can be terminated. In stateless application is quite easy implement mechanism of elasticity.
Nevertheless a quite lot of applications in the datacenter, especially legacy applications, are not stateless and require the ability to record user session activity, making some aspects of the application stateful. This type of application are not written to work in the cloud and requires an extra effort to understand how to guarantee the persistence of their state.
In the case customer own the code of the  applications, it can be done a porting to Azure with an architecture that stores the status of the application for example via a resilient messaging system. A typical example to adapt an existing application to the cloud is through a event/message system accessible by multiple application instances to implement a logic with producer(s)/consumer(s) of messages.

**Customer facing web sites**
Most of interaction with internet happen via web sites.
The vDC offers the capability to run web site in IaaS VMs or in Azure Web Apps (PaaS).
Azure Web Apps support the integration with VNets that allow to specific department to deploy the Web Apps in a spoke of vDC. A common scenario where you would use VNET Integration is enabling access from your web app to a database or a web services running on a virtual machine in your Azure virtual network. With VNET Integration you don't need to expose a public endpoint for applications on your VM but can use the private non-internet routable addresses instead.

**Big Data/Analytics**
when data need to scale up lo large volume, database do not scale up properly and Hadoop technology offer a system to run distribute queries in parallel on large number of nodes. Customer have the option to run build data workload in IaaS VMs or PaaS (HDInsight).
HDInsight supports deploying into a location-based VNet, that give to possibility to deploy a cluster in the spoke of the vDC. 

**Events and Messaging**
Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms and stores millions of events. 
As a distributed streaming platform, it offers low latency and configurable time retention, enabling you to ingress massive amounts of telemetry into Azure and to read the data from multiple applications.
With the Event Hubs a single stream can support both real-time and batch-based pipelines.

An highly-reliable cloud messaging service between applications and services, can be implemented through Azure Service bus the offers asynchronous operations brokered messaging between client and server, along with structured first-in-first-out (FIFO) messaging and publish/subscribe.


[![10]][10]


### Multiple vDC
 - Regional/Global presence
 - Disaster Recovery
 - mechanism to divert traffic between DC   

So far the discussion has been focus on single vDC; the article has described the different basic components and architecture that contribute to create a resilient vDC. Some Azure features as Azure load balancer, NVA in Azure marketplace, Availability Set,  Scale Set an many others mechanisms can contribute to run a system that overall have an acceptable SLA for production, minimizing potential failures and impact on the business. A single vDC has a regional extension and it is not resilient to a seldom major outage that might happen inside the specific region. Customers that want to achieve high SLAs need to protect the services through deployments of the same project in two (or more) vDCs, place in different regions.

####Regional/Global presence
Azure datacenters are present in numerous ww regions; in selection of multiple Azure datacenters customers need to consider few factors: geographical distances and latency. 
Customer need to evaluate the geographical distance between the vDCs and between the vDC and location of end users, to offer the best user experience.  Selection of vDCs need to be conform with compliance and regulatory established from the local industries/government that the company have embraced.

####Disaster Recovery
the implementation of disaster recovery is strongly related to the type of workload and the possibility to synchronize the workload state between different vDCs.
Ideally customers want to synchronize application data  between deployments running in two different vDC,to implement a fast fail-over mechanism between vDC. Most of applications are sensitive to latency, that can cause potential timeout and delay in data synchronization. Alignment of data in synch mode is a challenge, in particular when the data churn is high and forbidden the commitment of transactions logs to the remote vDC. 
Synchronization of applications in different vDCs require an intercommunication between vDCs. 
Two vDCs in different regions can be connected through the ExpressRoute private peering when the related connections are bind to the same ExpressRoute circuit. One other possibility to interconnect vDC is through VNet-to-VNet. 
In most of cases the ExpressRoute connection might the preferred  mechanism due higher bandwidth and low latency with transit through the Microsoft backbone, but customer need to be aware that inter-traffic between vDCs will transit in the ExpressRoute location.  
There is no magic recipe to validate an application distributed between two (or more) different vDCs located in different regions; customer should run a network qualification test to verify the latency and bandwidth and therefore aim the approach more appropriate with synchronous or asynchronous data replication.

####Mechanism to divert traffic between DC
One of effective technique to divert the traffic incoming in one DC to another, it is based on DNS.
Azure Traffic Manager uses the Domain Name System (DNS) mechanism to direct the end-user traffic to the most appropriate public endpoint in specific vDC. Through probes, Traffic Manager checks periodically the service healthy of public endpoints in different vDCs and, in case of failure of the endpoints, it routes automatically the connections on secondary vDC. Traffic Manager is resilient to failure of an entire Azure region and allows to control the distribution of user traffic for service endpoints in different vDCs based on different criteria (as failure of a service in a specific vDC or selection of vDC with the lowest network latency for the client). Traffic Manager works on Azure public endpoint and it can be used for example to control/divert the traffic to the Azure VMs and Web Apps in the appropriate vDC.


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
[8]: ./media/best-practice-vdc/vnet-peering-perimeter-neworks.png "VNet peering and perimeter networks"
[9]: ./media/best-practice-vdc/high-level-diagram-monitoring.png "high Level diagram for Monitoring"
[10]: ./media/best-practice-vdc/high-level-workloads.png "high Level diagram for Workload"

<!--Link References-->



