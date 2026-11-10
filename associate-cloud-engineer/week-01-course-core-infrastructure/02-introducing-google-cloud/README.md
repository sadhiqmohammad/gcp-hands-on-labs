# Module 2: Introducing Google Cloud

What cloud computing is, how it evolved, and what Google Cloud offers.

## Section 2.1 Cloud computing overview

Video: [Cloud computing overview](https://www.youtube.com/watch?v=ph5hjgOAf40) (3m 16s)

Key concepts:

- **Definition:** the US National Institute of Standards and Technology (NIST) created the term cloud computing, though nothing about it is US-specific. Cloud computing is a way of using IT that has five equally important traits:
  1. **On-demand self-service:** through a web interface, users get the processing power, storage and network they need with no human intervention.
  2. **Broad network access:** resources are reached over the internet, from anywhere with a connection.
  3. **Resource pooling:** the provider holds a big pool of resources and allocates them to users from it. Buying in bulk lets the provider pass savings to customers, and customers don't need to know or care where the resources physically are.
  4. **Rapid elasticity:** resources are flexible. Customers get more quickly when they need more, and scale back when they need less.
  5. **Measured service:** customers pay only for what they use or reserve as they go. Stop using resources, stop paying.
- **Three waves of the trend towards cloud:**

  | Wave | Model | What it gave users |
  |---|---|---|
  | 1 | Colocation | Financial efficiency of renting physical space instead of investing in data center real estate |
  | 2 | Virtualized data centers | Servers, CPUs, disks, load balancers and so on as virtual devices. The enterprise still maintains the infrastructure, and it stays user-controlled and user-configured |
  | 3 | Container-based, fully automated elastic cloud | Automated services and scalable data. Services automatically provision and configure the infrastructure that runs applications |

- **Why Google moved to the third wave:** Google's business couldn't move fast enough within the virtualization model, so it switched to a container-based architecture. Google Cloud makes this third-wave cloud available to customers.
- **Google's view of the future:** every company, whatever its size or industry, will differentiate itself through technology, increasingly in the form of software. Great software is based on high-quality data, so every company is, or will become, a data company.

## Section 2.2 IaaS and PaaS

Video: [IaaS and PaaS](https://youtu.be/C7cb6kFhNmw) (2m 28s)

Key concepts:

- **Origin:** the move to virtualized data centers introduced two new types of offering, IaaS and PaaS.
- **Service models compared:**

  | Model | What it provides | How you pay | Google example |
  |---|---|---|---|
  | Infrastructure as a service (IaaS) | Raw compute, storage and network capabilities, organized virtually into resources similar to physical data centers | For the resources you allocate ahead of time | Compute Engine |
  | Platform as a service (PaaS) | Binds code to libraries that give access to the infrastructure the application needs, so more resources go to application logic | For the resources you actually use | App Engine |

- **Shift to managed services:** as cloud computing evolved, momentum moved toward managed infrastructure and managed services. Companies spend less time and money creating and maintaining technical infrastructure, focus more on business goals, and deliver products and services to customers more quickly and reliably.
- **Serverless:** another step in the evolution of cloud computing. It removes the need for any infrastructure management, so developers concentrate on code rather than server configuration. Google serverless technologies:
  - **Cloud Run:** deploys containerized, microservices-based applications in a fully managed environment.
  - **Cloud Run functions:** manages event-driven code as a pay-as-you-go service.
- **Software as a service (SaaS):** outside the scope of the course. SaaS provides the entire application stack: a complete cloud-based application that customers access and use. It is not installed on your local computer; it runs in the cloud and end users consume it directly over the internet. Google Workspace apps such as Gmail, Docs and Drive are examples.

## Section 2.3 The Google Cloud network

Video: [The Google Cloud network](https://youtu.be/SDGJqKIE5yY) (2m 56s)

Key concepts:

- **Google's global network:** Google Cloud runs on Google's own global network, the largest of its kind, built with billions of dollars of investment over many years. It is designed for the highest possible throughput and lowest possible latency.
- **Content caching nodes:** more than 100 worldwide. High-demand content is cached there for quicker access, so applications answer user requests from the location with the quickest response time.
- **Infrastructure:** redundant cloud regions and high-bandwidth connectivity, including subsea cables, deliver services to users wherever they are.
- **Seven major geographic locations:** North America, South America, Europe, Africa, the Middle East, Asia and Australia.
- **Why location matters:** where you place applications affects availability, durability and latency. Latency is the time a packet of information takes to travel from its source to its destination.
- **Locations, regions and zones:**

  | Level | What it is | Course example |
  |---|---|---|
  | Region | An independent geographic area, made up of zones | London (`europe-west2`), which currently has three zones |
  | Zone | An area where Google Cloud resources are deployed | A Compute Engine VM runs in the zone you specify, for resource redundancy |
  | Multi-region | Some services place resources across several regions | Spanner multi-region configurations replicate data across multiple zones in multiple regions, giving low-latency reads from locations such as the Netherlands and Belgium |

- **Running in several regions:** brings applications closer to users around the world and protects against issues affecting a whole region, such as a natural disaster.
- **Growing footprint:** the number of regions and zones keeps increasing. Current numbers are at [cloud.google.com/about/locations](https://cloud.google.com/about/locations).

## Section 2.4 Environmental impact

Video: [Environmental impact](https://youtu.be/MwZiq-DRGFo) (1m 30s)

Key concepts:

- **Energy use:** the virtual world, including Google Cloud's network, is built on physical infrastructure. Existing data centers use roughly 2% of the world's electricity, so Google works to run its data centers as efficiently as possible.
- **Customer goals:** Google Cloud customers have their own environmental goals, and running workloads on Google Cloud can be part of meeting them.
- **ISO 14001:** Google's data centers were the first to achieve this certification. It is a standard that maps out a framework for improving environmental performance through better resource efficiency and less waste.
- **Hamina, Finland:** one of the most advanced and efficient data centers in Google's fleet. Its cooling system uses sea water from the Bay of Finland to reduce energy use, the first of its kind anywhere in the world.
- **Google's milestones:**

  | Period | Milestone |
  |---|---|
  | First decade | First major company to be carbon neutral |
  | Second decade | First company to achieve 100% renewable energy |
  | By 2030 (goal) | First major company to operate on carbon-free energy 24/7, 365 days a year |

## Section 2.5 Security

Video: [Security](https://youtu.be/BggWZl8qTzk) (5m 41s)

Key concepts:

- **Why security matters to Google:** nine Google services each have more than one billion users, so design for security runs throughout the infrastructure that Google Cloud and Google services run on.
- **Progressive layers:** the security infrastructure is explained in layers, from physical security of the data centers, through securing the hardware and software, to the technical constraints and processes behind operational security.

  | Layer | Key security features |
  |---|---|
  | Hardware infrastructure | **Hardware design and provenance:** Google custom-designs its server boards and networking equipment, and custom chips including a hardware security chip being deployed on servers and peripherals. **Secure boot stack:** cryptographic signatures over the BIOS, bootloader, kernel and base OS image make sure machines boot the correct software. **Premises security:** Google designs and builds its own data centers with multiple layers of physical protection, and only a very small number of employees can access them. In third-party data centers, Google adds its own physical security measures on top of the operator's. |
  | Service deployment | **Encryption of inter-service communication:** Google services talk to each other with remote procedure calls (RPCs). The infrastructure gives RPC data cryptographic privacy and integrity, and automatically encrypts all infrastructure RPC traffic between data centers. Hardware cryptographic accelerators are being deployed to extend default encryption to RPC traffic inside data centers. |
  | User identity | **Central identity service:** usually seen by users as the Google login page. Beyond username and password, it challenges users for more information based on risk factors, such as whether they signed in from the same device or a similar location before. Users can add secondary factors, including devices based on the Universal 2nd Factor (U2F) open standard. |
  | Storage services | **Encryption at rest:** most applications reach physical storage indirectly through storage services, which apply encryption with centrally managed keys. Google also enables hardware encryption support in hard drives and SSDs. |
  | Internet communication | **Google Front End (GFE):** internet-facing Google services register with the GFE, which terminates all TLS connections with a public-private key pair and an X.509 certificate from a Certificate Authority (CA), and follows best practices such as perfect forward secrecy. **Denial of Service (DoS) protection:** Google's scale lets it absorb many DoS attacks, and multi-tier, multi-layer DoS protections further reduce risk to services behind a GFE. |
  | Operational security | **Intrusion detection:** rules and machine intelligence warn security teams of possible incidents, and Red Team exercises measure and improve detection and response. **Reducing insider risk:** Google aggressively limits and actively monitors employees with administrative access. **Employee U2F use:** employee accounts require U2F-compatible Security Keys to guard against phishing. **Software development practices:** central source control, two-party review of new code, and libraries that stop developers introducing certain classes of security bugs. A Vulnerability Rewards Program pays anyone who finds and reports bugs in Google's infrastructure or applications. |

- **Further reading:** [cloud.google.com/security/security-design](https://cloud.google.com/security/security-design).

## Review questions

- What are the five traits of cloud computing, as NIST defines it?
- How does the third wave of cloud differ from virtualized data centers?
- How do IaaS and PaaS differ in what they provide and how you pay? Name a Google Cloud example of each.
- Which two Google serverless technologies does the course name, and what does each do?
- Where does SaaS fit, and which Google products are examples?
- What is the relationship between a region and a zone? Give the course's example region.
- Why run resources in more than one region, and what does a Spanner multi-region configuration add?
- What does ISO 14001 certification cover, and what makes the Hamina data center notable?
- What are Google's three sustainability milestones, including the 2030 goal?
- Name the six layers of Google's security infrastructure, from hardware up to operations.
- What does the Google Front End do, and how does Google protect against DoS attacks?
- How are data in transit between services and data at rest protected?
