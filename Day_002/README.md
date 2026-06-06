# 1. Filter HTTP 5xx errors:
grep "HTTP/.*\" 5" Day_001/web_access.log

# 2. Count your total critical errors:
grep -E "ERROR|CRITICAL" Day_001/critical_errors.log | wc -l




Track 2: AWS & Container Engines – Subnet SegmentationMental Model: A VPC is your isolated cloud data center.  Public Subnets route directly to an Internet Gateway (hosting your Load Balancers).  Private Subnets have no direct inbound internet path (hosting your database and internal app nodes).  Task: Sketch or map out a single VPC divided across two Availability Zones, with one public subnet and one private subnet in each zone to ensure high availability.

Here is a diagram illustrating the network segmentation of a Virtual Private Cloud (VPC) across two Availability Zones for high availability.

This layout adheres to your mental model:



Public Subnets (Green): These route to the Internet Gateway, hosting resources like the Application Load Balancers and NAT Gateways.

Private Subnets (Blue): These contain your container nodes (labeled here as EC2 Worker Nodes/Container App Nodes) and have their outbound traffic routed through the NAT Gateways to remain isolated from direct internet access.


1. The Foundation: The VPC (Your Isolated Cloud Data Center)Imagine a Virtual Private Cloud (VPC) as a massive, secure plot of land you've rented in a digital city (an AWS Region like us-east-1). Nobody can enter this land, see inside it, or build on it unless you explicitly give them permission.When you create a VPC, you define its size using an IP address range called a CIDR block (e.g., 10.0.0.0/16). This gives you roughly 65,000 private IP addresses to assign to your resources.2. High Availability: Availability Zones (AZs)If you build your entire application in one physical data center, a power outage or natural disaster could take your whole business offline.To prevent this, AWS has Availability Zones (AZs). These are distinct, physical data centers isolated from each other within the same region, but connected by ultra-fast fiber-optic cables.By splitting our VPC architecture across two AZs (e.g., AZ 1 and AZ 2), we ensure High Availability (HA). If AZ 1 completely fails, AZ 2 keeps running smoothly.3. Subnet Segmentation: Public vs. PrivateYou cannot launch containers directly into a VPC; you must launch them into Subnets (sub-networks). Think of subnets as specific rooms inside your secure building.To map out our highly available architecture, we slice our VPC into four rooms—two in AZ 1, and two in AZ 2.+--------------------------------------------------------------------------+
|                          VPC (10.0.0.0/16)                               |
|                                                                          |
|       +--------------------------+    +--------------------------+       |
|       |     Availability Zone 1  |    |     Availability Zone 2  |       |
|       |                          |    |                          |       |
|       |  [ Public Subnet A ]     |    |  [ Public Subnet B ]     |       |
|       |  (Hosts Load Balancers)  |    |  (Hosts Load Balancers)  |       |
|       |                          |    |                          |       |
|       |  [ Private Subnet A ]    |    |  [ Private Subnet B ]    |       |
|       |  (Hosts Container Nodes) |    |  (Hosts Container Nodes) |       |
|       +--------------------------+    +--------------------------+       |
+--------------------------------------------------------------------------+
🟢 The Public Subnets (The Front Lobby)What makes them public? They are connected to an Internet Gateway (IGW) via their "Route Table" (the network's traffic cop). The IGW acts as the front door to the outside internet.What lives here? Application Load Balancers (ALBs). You do not put your application code or databases here. You only put the load balancer here to greet public users, accept incoming traffic, and screen it.
🔵 The Private Subnets (The Secure Backroom)What makes them private? They have no direct route to the Internet Gateway. The outside world cannot see, ping, or connect directly to anything inside these subnets.What lives here? Your Container Engines / App Nodes (like AWS ECS tasks or EKS Kubernetes pods) and your databases.4. How Container Engines Use This NetworkLet's look at how a user actually interacts with a containerized application (like Docker containers running on AWS ECS or EKS) in this setup.Step 1: Inbound Traffic (The Request)A user types your website URL into their browser.The request hits the Internet Gateway and is directed to the Application Load Balancer (ALB) sitting in the Public Subnets.Because the ALB spans both Public Subnet A and Public Subnet B, it evenly distributes incoming traffic across both Availability Zones.Step 2: The Handshake (Entering the Private Subnet)The ALB acts as a proxy. It takes the public request, verifies it, and passes it backward into the Private Subnets.Inside the Private Subnets, your Container Nodes (the servers actually running your application code) receive the request, process it, and talk to the private database if necessary.The containers send the response back to the ALB, which passes it back to the user.Step 3: Outbound Traffic (The NAT Gateway Exception)What happens if a container in the Private Subnet needs to download an external software update or a third-party API? It cannot talk directly to the internet.To solve this, we place a NAT Gateway (Network Address Translation) inside the Public Subnet.When a private container needs to talk out, its traffic goes to the NAT Gateway. The NAT Gateway reaches out to the internet on the container's behalf, grabs the update, and brings it back.Crucial Security Rule: Internet traffic can leave via the NAT Gateway, but outside hackers cannot use the NAT Gateway to initiate a connection in.

Summary of the Architecture BenefitsBy mapping your VPC this way, you achieve three gold-standard cloud principles:PrincipleHow This Architecture Achieves ItSecurityYour actual containers and data are invisible to the public internet, locked away in private subnets.High AvailabilityIf an entire AWS data center goes offline, your Load Balancer instantly routes 100% of the traffic to the subnets in the surviving Availability Zone.ScalabilityYour container engine (ECS/EKS) can automatically launch 10 or 100 new container instances across both private subnets instantly as traffic spikes.



Track 3: CI/CD & Automation – Branch PartitioningIsolate today’s work cleanly inside your local repository using standard feature branching.  Bashgit checkout main
git pull origin main
git checkout -b feature/day-002-log-parsing
mkdir -p Day_002 && touch Day_002/README.md



Track 4: High-Scale Systems Design – Cache-Aside ArchitectureThe Flow: Application $\rightarrow$ Check Redis (Hit/Miss) $\rightarrow$ Fallback to SQL Database $\rightarrow$ Populate Redis.  Task: Write a quick 5-line pseudocode block in your Day_002/README.md that maps out this if/else logic for fetching a user profile.
