# 100 Days of Cloud - Day 1: Create an EC2 Key Pair
 
## Scenario
The Nautilus DevOps team is planning to migrate part of their infrastructure to the AWS cloud. Because of the scale of the project, they are taking an incremental approach: large tasks are broken into smaller, manageable units. This allows gradual phases, smoother implementation, better control, risk mitigation, and minimal disruption to operations.
 
## Task
Create an EC2 key pair with the following requirements:
 
| Requirement | Value |
|-------------|-------|
| Key pair name | `nautilus-kp` |
| Key pair type | `rsa` |
 
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
