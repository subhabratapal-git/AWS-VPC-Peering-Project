# AWS VPC Peering Project

**Student:** Subhabrata Pal  
**Project:** AWS VPC Peering – Secure Private Connectivity Between Two VPCs  
**Platform:** Amazon Web Services (AWS)

---

## 1. Project Overview

This project demonstrates secure private communication between two isolated Amazon Virtual Private Clouds (VPCs) using AWS VPC Peering.

The infrastructure includes two non-overlapping VPCs, three Amazon EC2 instances, public and private subnets, custom route tables, security groups, an Internet Gateway, and a bastion host for secure SSH access.

The project validates bidirectional private connectivity, demonstrates how security group rules affect traffic, and documents the complete cleanup of the AWS resources.

## 2. Project Objectives

- Create two VPCs with non-overlapping IPv4 CIDR blocks.
- Configure public and private subnets with explicit route table associations.
- Deploy one public bastion host and two private application instances.
- Establish and activate a VPC Peering Connection.
- Configure forward and return routes for private communication.
- Restrict access through AWS Security Groups.
- Verify private SSH connectivity and bidirectional ICMP communication.
- Demonstrate controlled connectivity failure and successful recovery.
- Delete project resources after collecting execution evidence.

## 3. AWS Services and Technologies

| Service / Technology | Purpose |
|---|---|
| Amazon VPC | Isolated virtual networks |
| Amazon EC2 | Bastion host and private application instances |
| VPC Peering | Private communication between VPC A and VPC B |
| Subnets | Public and private network segmentation |
| Route Tables | Traffic routing between subnets and peered VPCs |
| Internet Gateway | Internet connectivity for the public subnet |
| Security Groups | Instance-level traffic control |
| SSH | Secure remote access through the bastion host |
| ICMP / Ping | Network connectivity verification |
| Amazon Linux 2023 | EC2 operating system |

## 4. Network Architecture

The architecture consists of two VPCs connected through an active VPC Peering Connection.

**VPC A – `10.10.0.0/16`**

- Public Subnet: `10.10.1.0/24`
- Private Subnet: `10.10.2.0/24`
- Bastion Host: `10.10.1.176`
- Private App-A: `10.10.2.94`

**VPC B – `10.20.0.0/16`**

- Private Subnet: `10.20.1.0/24`
- Private App-B: `10.20.1.193`

**Communication Flow**

1. The administrator connects to the public bastion host using SSH.
2. The bastion host provides secure access to the private EC2 instances.
3. App-A communicates with App-B through the VPC Peering Connection.
4. The private route tables provide forward and return paths.
5. Security Groups control permitted traffic.

Both private application instances operate without public IPv4 addresses.

## 5. AWS Resource Inventory

| Resource | Name / ID | Configuration |
|---|---|---|
| VPC A | `vpc-09d5f914c63cc5a49` | `10.10.0.0/16` |
| VPC B | `vpc-0786740c8561cb75a` | `10.20.0.0/16` |
| A Public Subnet | `ewb-a-public` | `10.10.1.0/24` |
| A Private Subnet | `ewb-a-private` | `10.10.2.0/24` |
| B Private Subnet | `ewb-b-private` | `10.20.1.0/24` |
| Bastion EC2 | `ewb-bastion` | `10.10.1.176` |
| Private EC2 A | `ewb-private-a` | `10.10.2.94` |
| Private EC2 B | `ewb-private-b` | `10.20.1.193` |
| VPC Peering | `pcx-0bfef6063363d0407` | VPC A ↔ VPC B |

All three EC2 instances used Amazon Linux 2023 and the `t3.micro` instance type.

## 6. Route Table Configuration

The following custom route tables were configured:

| Route Table | Destination | Target |
|---|---|---|
| A Public | `0.0.0.0/0` | Internet Gateway |
| A Public | `10.20.0.0/16` | VPC Peering Connection |
| A Private | `10.20.0.0/16` | VPC Peering Connection |
| B Private | `10.10.0.0/16` | VPC Peering Connection |

Each route table was associated with its corresponding subnet.

The local VPC routes were retained to support communication within each VPC.

## 7. Security Configuration

Security Groups were used to restrict network access.

- The bastion host allowed SSH access from the administrator's authorized public IP address.
- Private application instances were accessed through the bastion host.
- ICMP rules were configured to permit testing between the two private application instances.
- No public IPv4 address was assigned to App-A or App-B.
- The private workloads did not require a NAT Gateway.

This design demonstrates controlled access to private workloads while enabling inter-VPC communication.

## 8. Connectivity Testing and Results

| Test | Result |
|---|---|
| SSH access to private App-A through bastion | Successful |
| SSH access to private App-B through bastion | Successful |
| Forward Ping: App-A → App-B | 4 received, 0% packet loss |
| Reverse Ping: App-B → App-A | 4 received, 0% packet loss |
| Controlled ICMP Rule Removal | 100% packet loss |
| ICMP Rule Restoration | 4 received, 0% packet loss |
| Private HTTP Connectivity Test | HTTP 200 OK |

### Forward Connectivity

App-A (`10.10.2.94`) successfully communicated with App-B (`10.20.1.193`) over the VPC Peering Connection.

### Reverse Connectivity

App-B (`10.20.1.193`) successfully communicated with App-A (`10.10.2.94`), confirming that the return route and security configuration supported bidirectional communication.

### Controlled Failure and Recovery

The ICMP inbound rule on App-B was temporarily removed. The forward ping test then failed with 100% packet loss.

After restoring the rule, the ping test succeeded again with 0% packet loss.

This confirmed that Security Group rules are essential for permitting the intended traffic even when network routes are correctly configured.

## 9. Project Screenshots

All AWS configuration, testing, troubleshooting, and cleanup screenshots are available in the [screenshots](./screenshots) directory.

The evidence covers:

- VPC and subnet configuration
- EC2 instance details and private IP addresses
- Internet Gateway and route tables
- VPC Peering status
- Security Group rules
- SSH connectivity
- Forward and reverse ping tests
- Controlled failure and recovery
- Resource termination and cleanup

## 10. Final Project Report

The complete project report, including the architecture diagram, configuration details, test results, explanations, and supporting screenshots, is available in the [Project Report](./Project%20Report) directory.

**Report Filename:** `Subhabrata_Pal_EWB_VPC_Peering.pdf`

## 11. AWS Resource Cleanup

After collecting the required evidence, the project infrastructure was cleaned up.

- Three EC2 instances were terminated.
- Project EBS volumes were removed.
- No Elastic IPs or NAT Gateways remained.
- The VPC Peering Connection was deleted.
- Custom Security Groups were deleted.
- Project subnets and route tables were removed.
- Project Internet Gateways were deleted.
- Both project VPCs were deleted.
- The project EC2 Key Pair was deleted.
- No owned EBS snapshots remained in the project Region.

The existing default VPC and unrelated AWS resources were preserved.

## 12. Key Learnings

This project provided practical experience in AWS network isolation, subnet design, VPC Peering, route table management, bastion-based SSH access, and Security Group configuration.

The connectivity tests demonstrated the importance of both forward and return routes. The controlled failure and recovery test further illustrated how security rules affect private communication.

The cleanup process reinforced the importance of removing temporary cloud infrastructure after completing a project.

## 13. Conclusion

The AWS VPC Peering project successfully demonstrated private communication between application instances deployed in separate VPCs.

Bidirectional connectivity was verified, secure SSH access was demonstrated, and controlled failure and recovery testing confirmed the effectiveness of the network security configuration.

All major project resources were subsequently cleaned up, with supporting evidence retained for review.

---

**Project completed by Subhabrata Pal**  
**EWB Cloud Labs – AWS VPC Peering Project | October 2026**
