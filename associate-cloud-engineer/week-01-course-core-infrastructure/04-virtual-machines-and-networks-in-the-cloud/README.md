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
