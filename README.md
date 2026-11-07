# 100 Days of Cloud

# Day 1: Create an EC2 Key Pair

## Scenario

The Nautilus DevOps team is planning to migrate part of their infrastructure to the AWS cloud. Because of the scale of the project, they are taking an incremental approach: large tasks are broken into smaller, manageable units. This allows gradual phases, smoother implementation, better control, risk mitigation, and minimal disruption to operations.

## Task

Create an EC2 key pair with the following requirements:

| Requirement   | Value         |
| ------------- | ------------- |
| Key pair name | `nautilus-kp` |
| Key pair type | `rsa`         |

## What I Learned

- An **EC2 key pair** is a set of credentials (a public key stored by AWS and a private key you download) used to securely connect to EC2 instances over SSH.
- AWS stores only the **public key**. The **private key** is shown only once at creation, so it must be saved safely.
- Key pair **type** matters: `rsa` is widely supported, while `ed25519` is a newer, smaller, faster alternative (Linux instances only).
- Private key files must have restricted permissions (`chmod 400`) or SSH will refuse to use them.

## Steps

1. Sign in to the AWS Management Console and open the **EC2** service.
2. In the left menu, under **Network & Security**, click **Key Pairs**.
3. Click **Create key pair**.
4. Enter the name: `nautilus-kp`.
5. Select key pair type: **RSA**.
6. Choose the private key file format (`.pem` for OpenSSH).
7. Click **Create key pair**. The private key file downloads automatically.

# Day 2: Create a Security group

## Scenario

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the AWS cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations. By breaking down the migration into smaller tasks, the Nautilus DevOps team can systematically progress through each stage, allowing for better control, risk mitigation, and optimization of resources throughout the migration process.

## Task: Create a Security Group for Nautilus App Servers

### Requirements

| Requirement            | Value                                     |
| ---------------------- | ----------------------------------------- |
| Name of security group | `devops-sg`                               |
| Description            | `Security group for Nautilus App Servers` |
| Inbound rule 1         | HTTP, port `80`, CIDR `0.0.0.0/0`         |
| Inbound rule 2         | SSH, port `22`, CIDR `0.0.0.0/0`          |

### Steps

#### Option 1: AWS Console

1. Sign in to the AWS Management Console and open the **EC2** service.
2. In the left sidebar, under **Network & Security**, click **Security Groups**.
3. Click **Create security group**.
4. Fill in the basic details:
   - **Security group name:** `devops-sg`
   - **Description:** `Security group for Nautilus App Servers`
   - **VPC:** leave the default VPC selected (unless the task names another).
5. Under **Inbound rules**, click **Add rule** and set:
   - Type: `HTTP`, Port range: `80`, Source: `Custom` → `0.0.0.0/0`
6. Click **Add rule** again and set:
   - Type: `SSH`, Port range: `22`, Source: `Custom` → `0.0.0.0/0`
7. Leave **Outbound rules** as the default (all traffic allowed).
8. Click **Create security group**.
9. Verify: open `devops-sg` and check the **Inbound rules** tab shows both rules.

#### Option 2: AWS CLI

```bash
# Create the security group (returns the GroupId)
aws ec2 create-security-group \
  --group-name devops-sg \
  --description "Security group for Nautilus App Servers"

# Allow HTTP (80) from anywhere
aws ec2 authorize-security-group-ingress \
  --group-name devops-sg \
  --protocol tcp --port 80 --cidr 0.0.0.0/0

# Allow SSH (22) from anywhere
aws ec2 authorize-security-group-ingress \
  --group-name devops-sg \
  --protocol tcp --port 22 --cidr 0.0.0.0/0

# Verify
aws ec2 describe-security-groups --group-names devops-sg
```

### What I Learned

- A **security group** acts as a virtual firewall for EC2 instances, controlling inbound and outbound traffic at the instance level.
- Security groups are **stateful**: if inbound traffic is allowed, the response traffic is automatically allowed out, with no separate outbound rule needed.
- Security groups only have **allow rules**. There are no deny rules; anything not explicitly allowed is blocked.
- **Inbound** rules define what can reach the instance, while **outbound** rules define what it can reach. By default, all outbound traffic is allowed and all inbound is denied.
- Port `80` is used for **HTTP** (web traffic) and port `22` for **SSH** (remote administration).
- The CIDR `0.0.0.0/0` means **any IPv4 address** on the internet. It's convenient for a lab or task like this, but in production SSH should be restricted to a trusted IP or range (e.g. `203.0.113.10/32`) or a VPN/bastion host.
- A security group belongs to a specific **VPC**, so it must be created in the same VPC as the instances that will use it.
- The group name and description cannot be changed after creation, so it's worth getting them exactly right the first time.
- Rules can be changed at any time, and changes apply immediately to all instances using the group.
