# Module 4: Virtual Machines and Networks in the Cloud

How Google Compute Engine works, with a focus on virtual networking.

## Section 4.1 Virtual Private Cloud networking

Video: [Virtual Private Cloud networking](https://youtu.be/SFRCZvJN650) (2m 21s)

Key concepts:

- **Where users start:** many users begin with Google Cloud by defining their own virtual private cloud in their first project, or by starting with the default virtual private cloud.
- **Virtual private cloud (VPC):** a secure, individual, private cloud-computing model hosted within a public cloud, like Google Cloud.
  - Customers can run code, store data, host websites, and do anything else they could do in an ordinary private cloud, but it is hosted remotely by a public cloud provider.
  - VPCs combine the scalability and convenience of public cloud computing with the data isolation of private cloud computing.
- **What VPC networks do:** connect Google Cloud resources to each other and to the internet. This includes:
  - Segmenting networks.
  - Using firewall rules to restrict access to instances.
  - Creating static routes to forward traffic to specific destinations.
- **VPC networks are global:** this tends to surprise new Google Cloud users.
  - A VPC network can have **subnets** in any Google Cloud region worldwide. A subnet is a segmented piece of the larger network.
  - Subnets can span the zones that make up a region, so resources in different zones can be on the same subnet.
  - This makes it easy to define network layouts with global scope.
- **Expanding a subnet:** you can increase a subnet's size by expanding the range of IP addresses allocated to it. This doesn't affect VMs that are already configured.
- **Example from the course:** a VPC network named `vpc1` has two subnets, in the `asia-east1` and `us-east1` regions. Three Compute Engine VMs attached to it are neighbors on the same subnet even though they are in different zones. You can use this to build solutions that are resilient to disruptions but keep a simple network layout.

## Section 4.2 Compute Engine

Video: [Compute Engine](https://youtu.be/90pyGSKyPDQ) (3m 45s)

Key concepts:

- **Compute Engine:** Google Cloud's infrastructure as a service (IaaS) solution. Users create and run virtual machines on Google infrastructure.
  - No upfront investments.
  - Thousands of virtual CPUs can run on a system designed to be fast and to offer consistent performance.
- **A VM is configured like a physical server:** each VM has the power and functionality of a full-fledged operating system. You specify:
  - The amount of CPU power and memory.
  - The amount and type of storage.
  - The operating system.
- **Three ways to create a VM instance:**
  - The Google Cloud console, a web-based tool to manage Google Cloud projects and resources.
  - The Google Cloud CLI.
  - The Compute Engine API.
- **Images:** an instance can run Linux and Windows Server images provided by Google, or customized versions of these images. You can also build and run images of other operating systems, and flexibly reconfigure VMs.
- **Cloud Marketplace:** a quick way to get started, with solutions from Google and third-party vendors.
  - No need to manually configure the software, VM instances, storage or network settings, although many solutions can be modified before launch.
  - Most software packages cost nothing beyond the normal usage fees for Google Cloud resources.
  - Some images charge usage fees, particularly third-party ones with commercially licensed software. All of them show an estimate of monthly charges before launch.
- **Pricing and discounts:**

  | Option | How it works |
  |---|---|
  | Per-second billing | VMs are billed by the second, with a one-minute minimum |
  | Sustained-use discounts | Applied automatically the longer a VM runs. For each VM that runs for more than 25% of a month, a discount applies for every additional minute |
  | Committed-use discounts | For stable, predictable workloads. Buy a specific amount of vCPUs and memory for up to a 57% discount, in return for a one-year or three-year usage term |
  | Spot VMs | For workloads that don't need immediate human oversight, like a batch job analyzing a large dataset. Save up to 90% compared to standard pricing |

- **Spot VMs in more detail:**
  - Unlike the older Preemptible model, Spot VMs have no maximum runtime. They run as long as capacity is available.
  - The trade-off: Google Cloud can terminate the instance if those resources are needed elsewhere.
  - Design the workload so it can be stopped and restarted without losing progress.
- **Storage throughput:** you don't need a particular option or machine type to get high throughput between processing and persistent disks. It is the default, at no extra cost.
- **Machine types:** choose machine properties, like the number of vCPUs and the amount of memory, from a set of predefined machine types, or create your own custom machine types so you only pay for what you need.

## Section 4.3 Scaling virtual machines

Video: [Scaling virtual machines](https://youtu.be/YQK8u563me4) (1m 13s)

Key concepts:

- **Autoscaling:** a Compute Engine feature that adds VMs to or removes VMs from an application based on load metrics.
- **Load balancing:** the other part of making autoscaling work is balancing incoming traffic among the VMs. Google's VPC supports several kinds of load balancing, covered later in the module.
- **Scale out, not up:** Compute Engine can run very large VMs, which suit workloads such as in-memory databases and CPU-intensive analytics. Most Google Cloud customers start by scaling out (more VMs) rather than up (bigger VMs).
- **Limits on CPUs per VM:**
  - The maximum number of CPUs per VM is tied to its **machine family**.
  - It is also limited by the user's available **quota**, which is zone-dependent.
- **Machine type specs:** the current VM machine types are listed at [cloud.google.com/compute/docs/machine-types](https://cloud.google.com/compute/docs/machine-types).

## Section 4.4 Important VPC compatibilities

Video: [Important VPC compatibilities](https://youtu.be/UtNlJbm8s2Q) (1m 31s)

Key concepts:

- **Built-in routing tables:** like physical networks, VPCs have routing tables, but they are built in, so you don't provision or manage a router. They forward traffic from one instance to another:
  - Within the same network.
  - Across subnetworks.
  - Between Google Cloud zones, without needing an external IP address.
- **Global distributed firewall:** you don't provision or manage a firewall either. The VPC firewall can restrict access to instances for both incoming and outgoing traffic.
- **Firewall rules with network tags:** rules can be defined through network tags on Compute Engine instances.
  - Example from the course: tag all web servers `WEB`, then write a rule that allows traffic on ports 80 or 443 into all VMs with the `WEB` tag, whatever their IP addresses are.
- **Connecting VPCs across projects:** VPCs belong to Google Cloud projects. When a company has several projects whose VPCs need to talk to each other:
  - **VPC Peering:** establishes a relationship between two VPCs so they can exchange traffic.
  - **Shared VPC:** the alternative. It uses the full power of IAM to control who and what in one project can interact with a VPC in another.

## Section 4.5 Cloud Load Balancing

Video: [Cloud Load Balancing](https://youtu.be/HWJQ3LNagXc) (2m 56s)

Key concepts:

- **Why it's needed:** with autoscaling, an application might be served by four VMs one moment and 40 the next. Cloud Load Balancing is how customers reach it.
- **What a load balancer does:** distributes user traffic across multiple instances of an application. Spreading the load reduces the risk of performance issues.
- **Cloud Load Balancing:** a fully distributed, software-defined, managed service for all your traffic.
  - The load balancers don't run in VMs you manage, so you don't scale or manage them.
  - It can front HTTP or HTTPS, other TCP and SSL traffic, and UDP traffic.
  - It provides cross-region load balancing, including automatic multi-region failover, which gently moves traffic in fractions if backends become unhealthy.
  - It reacts quickly to changes in users, traffic, network, backend health and other related conditions.
- **No pre-warming:** for an anticipated spike in demand, such as an online game that's already a hit, you don't file a support ticket to warn Google. No "pre-warming" is required.
- **Types of load balancer:** classified by the OSI model layer they operate at and their functionality.

  | Type | Layer | Traffic | Key points |
  |---|---|---|---|
  | Application Load Balancer | Application | HTTP and HTTPS | Ideal for web applications and services needing content-based routing and SSL/TLS termination. Operates as a reverse proxy, distributing traffic across backends based on rules you define. Can be external (internet-facing) or internal |
  | Proxy Network Load Balancer | Transport | TCP, UDP and other IP protocols | Also a reverse proxy: terminates client connections and opens new ones to backend services. Advanced traffic management, and supports backends on-premises and in various cloud environments |
  | Passthrough Network Load Balancer | Transport | TCP, UDP and other IP protocols | Doesn't modify or terminate connections. Forwards traffic directly to the backend and preserves the original source IP address. Suits apps that need direct server return or a wider range of IP protocols |

## Section 4.6 Cloud DNS and Cloud CDN

Video: [Cloud DNS and Cloud CDN](https://youtu.be/TYB1cur47mk) (1m 45s)

Key concepts:

- **DNS:** translates internet hostnames to addresses. Google runs a highly developed DNS infrastructure and offers 8.8.8.8, a free public Domain Name Service, to the world.
- **Cloud DNS:** helps the world find the hostnames and addresses of applications built in Google Cloud.
  - A managed DNS service running on the same infrastructure as Google.
  - Low latency, high availability, and a cost-effective way to make applications and services available to users.
  - The DNS information you publish is served from redundant locations around the world.
  - Programmable: publish and manage millions of DNS zones and records with the Cloud console, the command-line interface or the API.
- **Edge caching:** using caching servers to store content closer to end users. Google has a global system of edge caches.
- **Cloud CDN (Content Delivery Network):** uses those edge caches to accelerate content delivery in your application.
  - Customers get lower network latency.
  - The origins of your content get reduced load.
  - You can even save money.
  - Once an Application Load Balancer is set up, you enable Cloud CDN with a single checkbox.
- **Other CDNs:** if you already use another CDN, it is likely part of Google Cloud's **CDN Interconnect** partner program, and you can keep using it.

## Section 4.7 Connecting networks to Google VPC

Video: [Connecting networks to Google VPC](https://youtu.be/g24UStCvpQg) (5m 47s)

Key concepts:

- **Why connect:** many customers want to connect their Google VPC networks to other networks, such as on-premises networks or networks in other clouds.
- **Cloud VPN:** a Virtual Private Network connection over the internet, using Cloud VPN to create a "tunnel" connection.
- **Cloud Router:** makes the VPN connection dynamic. It lets other networks and Google VPC exchange route information over the VPN using the **Border Gateway Protocol (BGP)**. If you add a new subnet to your Google VPC, your on-premises network automatically gets routes to it.
- **Why not always the internet:** security concerns, or bandwidth reliability.
- **Peering:** putting a router in the same public data center as a Google **point of presence** and using it to exchange traffic between networks. Google has points of presence around the world.
  - **Direct Peering:** you peer with Google directly at a point of presence.
  - **Carrier Peering:** for customers not already in a point of presence. A partner in the Carrier Peering program gives you direct access from your on-premises network, through the service provider's network, to Google Workspace and to Google Cloud products that can be exposed through one or more public IP addresses.
  - **Downside:** peering isn't covered by a Google Service Level Agreement (SLA).
- **Dedicated Interconnect:** for the highest uptimes. One or more direct, private connections to Google.
  - Connections whose topologies meet Google's specifications can be covered by an SLA of up to 99.99%.
  - They can be backed up by a VPN for even greater reliability.
- **Partner Interconnect:** connectivity between an on-premises network and a VPC network through a supported service provider.
  - Useful when a data center can't reach a Dedicated Interconnect colocation facility, or when data needs don't warrant an entire 10 Gigabytes per second connection (as stated in the video).
  - Can be configured for mission-critical services or for applications that can tolerate some downtime.
  - Can be covered by an SLA of up to 99.99% if topologies meet Google's specifications. Google isn't responsible for any part provided by the third-party service provider, or for issues outside Google's network.
- **Cross-Cloud Interconnect:** high-bandwidth dedicated connectivity between Google Cloud and another cloud service provider.
  - Google provisions a dedicated physical connection between its network and the other provider's network. You use it to peer your Google VPC network with your network hosted by a supported provider.
  - Supports an integrated multicloud strategy, with reduced complexity, site-to-site data transfer and encryption.
  - Two sizes: 10 Gbps or 100 Gbps.
- **Summary of options:**

  | Option | Path | Private? | SLA |
  |---|---|---|---|
  | Cloud VPN (with Cloud Router) | Tunnel over the internet | Yes | Not stated in the video |
  | Direct Peering | Your router at a Google point of presence | No, public IP addresses | No Google SLA |
  | Carrier Peering | Through a service provider's network | No, public IP addresses | No Google SLA |
  | Dedicated Interconnect | Direct, private connection to Google | Yes | Up to 99.99% if topology meets specs |
  | Partner Interconnect | Through a supported service provider | Yes | Up to 99.99% if topology meets specs, Google side only |
  | Cross-Cloud Interconnect | Dedicated physical link to another cloud | Dedicated, with encryption | Not stated in the video |

- **Three questions to choose an option:**
  1. Do on-premises servers or user computers with private addressing need to connect to Google Cloud resources with private addressing?
  2. Do the bandwidth and performance of your current connection to Google services meet your business requirements?
  3. Do you already have, or are you willing to install and manage, access and routing equipment in a Google point of presence?
- **Choosing, from the course:**

  | If you... | Use |
  |---|---|
  | Need private-to-private connectivity, and your internet connection meets requirements | Cloud VPN |
  | Don't need private access, and your internet connection meets requirements | Public IP addresses to Google services |
  | Don't need private access, your connection isn't performing well, and you have or will lease space and equipment at a point of presence | Direct Peering |
  | Don't need private access, your connection isn't performing well, and can't install equipment or prefer a partner | Carrier Peering |
  | Need private, high-performance connectivity, and can't install equipment or prefer a partner | Partner Interconnect |
  | Need a private circuit direct to Google, and have or will lease co-lo space and equipment at a point of presence | Dedicated Interconnect |

## Section 4.8 Lab: Get Started with VPC Networking and Compute Engine

This section is a hands-on lab, not a video. Step-by-step guide: [Lab 4.8: Get Started with Virtual Private Cloud Networking and Compute Engine](08-lab-vpc-networking-compute-engine/README.md).

What you do:

- Explore the **default network**: a subnet in each region, a route for each subnet, and four ingress firewall rules (`allow-icmp`, `allow-rdp`, `allow-ssh`, `allow-internal`).
- Delete the default network, and see that you can't create a VM without a VPC network.
- Create `mynetwork`, an **auto mode** VPC network with the standard firewall rules, and a VM in each of two regions.
- Delete the firewall rules one at a time to see what each one allows: external ping (`allow-icmp`), internal ping (`allow-custom`) and SSH (`allow-ssh`).
