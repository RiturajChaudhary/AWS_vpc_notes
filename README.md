# 🏗️ AWS VPC Architecture

Simple reference note for core AWS VPC components and how they connect.

---

## 1. VPC (Virtual Private Cloud)

Your isolated private network in AWS.

- CIDR: `10.0.0.0/16`
- All resources live inside this boundary

---

## 2. Subnets

| Type           | CIDR          | Public IP      | Used For              |
|----------------|---------------|----------------|-----------------------|
| Public Subnet  | `10.0.1.0/24` | ✅ Auto-assign | EC2, NAT Gateway      |
| Private Subnet | `10.0.2.0/24` | ❌ Disabled    | Private EC2 instances |

---

## 3. Internet Gateway (IGW)

- 1 IGW per VPC
- Enables internet access for the public subnet and NAT Gateway

---

## 4. NAT Gateway

- Sits in the **public subnet**
- Allows private EC2 to reach the internet (outbound only)
- Flow: `Private EC2 → NAT Gateway → IGW → Internet`

---

## 5. Route Tables

| Route Table | Routes To    | Associated With                |
|-------------|--------------|--------------------------------|
| Public RT   | IGW          | Public Subnet (explicit)       |
| Private RT  | NAT Gateway  | Private Subnet (implicit)      |

- No NACLs used — traffic controlled by Security Groups only

---

## 6. Security Groups

| SG             | Inbound                                        | Outbound          |
|----------------|------------------------------------------------|-------------------|
| Public EC2 SG  | SSH (22) from My IP, HTTP/HTTPS from Anywhere  | All traffic       |
| Private EC2 SG | SSH (22) from Public EC2 SG only              | All traffic       |

> ✅ Best practice: use **separate SGs** for public and private EC2.

---

## 7. Connectivity Flow

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

## Summary

| Resource       | Access                              |
|----------------|-------------------------------------|
| Public EC2     | Direct internet via IGW             |
| Private EC2    | Outbound only via NAT Gateway       |
| Security       | Managed by Security Groups          |
| Private Subnet | Implicit route table association    |
