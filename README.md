# 🏗️ AWS VPC Architecture Overview Note with Definitions

---

## 1️⃣ VPC (Virtual Private Cloud)

**Definition:** A VPC is a virtual network dedicated to your AWS account, isolated from other networks in the cloud. It allows you to launch AWS resources in a logically isolated network.

- Example CIDR: `10.0.0.0/16`
- Acts as an isolated network in AWS.

---

## 2️⃣ Subnets

**Definition:** A subnet is a range of IP addresses in your VPC. Subnets divide your VPC into smaller networks to place resources logically.

| Subnet Type    | Purpose                          | CIDR          | Key Config                  |
|----------------|----------------------------------|---------------|-----------------------------|
| Public Subnet  | Hosts Public EC2 & NAT Gateway   | `10.0.1.0/24` | Auto-assign Public IP ✅    |
| Private Subnet | Hosts Private EC2                | `10.0.2.0/24` | Auto-assign Public IP ❌    |

**Notes:**
- Public subnet → resources directly accessible from the internet
- Private subnet → resources not directly accessible from the internet

---

## 3️⃣ Internet Gateway (IGW)

**Definition:** IGW is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the internet.

- 1 IGW attached to VPC
- Provides internet access for public subnet and NAT gateway

---

## 4️⃣ NAT Gateway

**Definition:** NAT Gateway allows instances in a private subnet to access the internet for updates or downloads while preventing inbound internet traffic to these instances.

- Placed in **Public Subnet**
- Flow: `Private EC2 → NAT Gateway → IGW → Internet` (outbound only)

---

## 5️⃣ Route Tables

**Definition:** Route tables control the network traffic routing within the VPC. Each subnet must be associated with a route table.

| Route Table | Type         | Use                          | Association                                    |
|-------------|--------------|------------------------------|------------------------------------------------|
| Public RT   | Main RT      | Public Subnet → IGW          | Explicitly associated with Public Subnet       |
| Private RT  | Secondary RT | Private Subnet → NAT Gateway | Used implicitly (without explicit association) |

**Notes:**
- Main route table is Public RT
- Private subnet automatically uses Private RT if set as default for private traffic
- No NACL used; traffic controlled via Security Groups

---

## 6️⃣ Security Groups (SG)

**Definition:** Security Groups are virtual firewalls that control inbound and outbound traffic for AWS resources. SGs are stateful, so responses to allowed inbound traffic are automatically allowed outbound.

| SG             | Purpose               | Rules                                                                                                      |
|----------------|-----------------------|------------------------------------------------------------------------------------------------------------|
| Public EC2 SG  | Public EC2 instances  | **Inbound:** SSH (22) → My IP, HTTP (80), HTTPS (443) → Anywhere<br>**Outbound:** All traffic → Anywhere  |
| Private EC2 SG | Private EC2 instances | **Inbound:** SSH (22) → Only from Public EC2 SG<br>**Outbound:** All traffic → NAT Gateway → Internet     |

**Key Points:**
- Can use the same SG for both public & private EC2, but may expose private EC2 if inbound rules allow public access
- Best practice: use separate SGs for public and private EC2

---

## 7️⃣ Connectivity Flow (Visual)

```
Internet
   |
  IGW
   |
Public Subnet ---- NAT Gateway
   |                    |
Public EC2         Private Route Table
                        |
                 Private Subnet
                        |
                  Private EC2
```

---

## ✅ Summary

- Public EC2 → Direct internet access via IGW
- Private EC2 → Outbound internet via NAT only
- Security controlled by SGs
- Private subnet uses implicit route table association
