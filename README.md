# AWS VPC Architecture

A simple overview of core AWS VPC components.

---

## Components

**VPC** — Your private network in AWS. CIDR: `10.0.0.0/16`

**Subnets**
- `Public Subnet (10.0.1.0/24)` — Hosts EC2 & NAT Gateway. Has public IP.
- `Private Subnet (10.0.2.0/24)` — Hosts private EC2. No public IP.

**Internet Gateway (IGW)** — Connects your VPC to the internet.

**NAT Gateway** — Lets private EC2 reach the internet (outbound only). Lives in public subnet.

**Route Tables**
- Public RT → routes traffic to IGW
- Private RT → routes traffic to NAT Gateway

**Security Groups** — Stateful firewalls for EC2 instances.
- Public EC2 SG: allows SSH, HTTP, HTTPS from internet
- Private EC2 SG: allows SSH only from Public EC2 SG

---

## Traffic Flow

```
Internet
   |
  IGW
   |
Public Subnet → NAT Gateway
   |                 |
Public EC2      Private Subnet
                     |
               Private EC2
```

---

## Key Points

- Public EC2 → internet via **IGW**
- Private EC2 → internet via **NAT** (outbound only)
- Security managed by **Security Groups**
- No NACLs used
