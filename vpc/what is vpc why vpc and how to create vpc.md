1. What is Amazon VPC?
A Virtual Private Cloud — a private network in AWS where you launch resources (EC2, RDS). You control IPs, subnets, route tables, and security.


2. Difference between a default VPC and a custom VPC?

Default VPC: AWS creates it for you with public subnets, internet gateway, and default settings — ready to run.

Custom VPC: You design CIDR, subnets, route tables, gateways — full control.



3. Maximum number of VPCs per AWS region?
Default soft limit is 5 per region (can be increased via support request). (Limits can change; AWS allows increases.)


4. What is a subnet in VPC?
A subnet is a range of IP addresses (a portion of the VPC CIDR) in one Availability Zone where you put resources.


5. Difference between a public subnet and a private subnet?

Public subnet: Has a route to an Internet Gateway (IGW) — resources can have public IPs and reach internet.

Private subnet: No direct IGW route — no public IPs; used for backend servers/databases.



6. What is a route table in VPC?
A set of rules (routes) that tells network traffic where to go (local, IGW, NAT, peering, VPN, etc.). Each subnet uses one route table.


7. What is an Internet Gateway (IGW)?
A managed gateway that allows traffic between the VPC and the internet. Attach IGW to VPC and add route to route table for public subnets.


8. What is a NAT Gateway and how is it different from a NAT Instance?

NAT Gateway: AWS-managed, highly available, scales automatically, used for private subnet internet outbound.

NAT Instance: EC2 you manage as NAT — needs patching/scaling and is less reliable than NAT Gateway.



9. Difference between Security Groups and NACLs?

Security Groups: Instance-level virtual firewall, stateful (return traffic allowed automatically), rules allow only, applied to ENIs.

NACLs (Network ACLs): Subnet-level firewall, stateless (need explicit inbound/outbound rules), can allow or deny, processed in order.



10. What is a VPC Endpoint and what are its types?
A way to privately connect your VPC to AWS services without internet. Types:

Gateway Endpoint (for S3, DynamoDB) — route table target.

Interface Endpoint (AWS PrivateLink) — ENI with private IPs in subnets.



11. Difference between a Gateway Endpoint and an Interface Endpoint?

Gateway Endpoint: Adds route to S3/DynamoDB; simple and free.

Interface Endpoint: Uses ENI in subnet, supports many services and third-party services via PrivateLink; may cost per-hour and per-GB.



12. What is VPC Peering?
A private network connection between two VPCs allowing instances to communicate using private IPs. Works across accounts and regions (with limits).


13. What are the limitations of VPC Peering?

No transitive routing (A–B and B–C peering does NOT allow A–C).

Route and overlapping CIDR restrictions (CIDRs must not overlap).

Some cross-region limitations and scale limits per account.



14. What is AWS Transit Gateway and how is it different from VPC Peering?
Transit Gateway is a central hub to connect many VPCs and on-prem networks with simplified routing. Unlike peering (point-to-point), Transit Gateway supports transitive routing and scales better for many VPCs.


15. What is a DHCP option set in VPC?
DHCP options define network settings that VMs receive (domain-name-servers, domain-name, NTP servers). You attach the set to a VPC.


16. What is an Elastic IP (EIP) in VPC?
A static public IPv4 address you allocate to your account and associate with an instance or NAT Gateway. It stays fixed even if you stop/start the instance.


17. What are Flow Logs in VPC and why are they used?
Logs of IP traffic to/from network interfaces. Used for troubleshooting, security analysis, and auditing. They can be sent to CloudWatch or S3.


18. Difference between IPv4 CIDR and IPv6 CIDR in VPC?

IPv4 CIDR: e.g., 10.0.0.0/16 — smaller address space, common for private networking.

IPv6 CIDR: very large address space (e.g., /56 assigned by AWS) — no NAT needed, different routing/permissions. IPv6 addresses are global.



19. Maximum number of IP addresses in a VPC subnet?
Depends on subnet CIDR size. Example: a /24 subnet has 256 addresses, but AWS reserves 5 addresses in each subnet, so usable = 251. (Count depends on CIDR you choose.)


20. What are the default components created when you launch a new VPC?
For a default VPC, AWS creates: default VPC, default subnets (one per AZ), route table, internet gateway, default security group, and default NACL. For a custom VPC, nothing is auto-created unless you make it.




---

If you want, I can:

Make flashcards from these Q&A, or

Give one short diagram showing public vs private subnet flow, or

Ask you some practice interview questions one by one. Which would you like next?
