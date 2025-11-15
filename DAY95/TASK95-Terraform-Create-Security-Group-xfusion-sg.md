# 🌟 Task 95 - Create Security Group `xfusion-sg` in Default VPC using Terraform

**📌 Task Description**  
The **Nautilus DevOps team** is migrating incrementally to **AWS**. As part of **Phase 2**, create a **Security Group** in the **default VPC** with:

**Requirements**:
- **Name**: `xfusion-sg`
- **Description**: `Security group for Nautilus App Servers`
- **Inbound Rules**:
  - HTTP (`80`) → `0.0.0.0/0`
  - SSH (`22`) → `0.0.0.0/0`
- **Region**: `us-east-1`
- **File**: `/home/bob/terraform/main.tf` (single file only)

---

## 📋 Requirements Summary

| **Field** | **Value** |
|-----------|-----------|
| Working Dir | `/home/bob/terraform` |
| File | `main.tf` |
| VPC | `default` |
| SG Name | `xfusion-sg` |
| Description | `Security group for Nautilus App Servers` |
| Ingress | HTTP (80), SSH (22) |
| Source | `0.0.0.0/0` |
| Egress | All (optional but best practice) |

---

## 📝 Solution Overview

### **File Structure**
```
/home/bob/terraform/
└── main.tf
```

---

## 🔹 Implementation Steps

### **Step 1: Connect to Jump Host**
```bash
ssh bob@jumphost
```

---

### **Step 2: Open VS Code & Terminal**

Right-click **EXPLORER** → **Open in Integrated Terminal**
```bash
cd /home/bob/terraform
```

---

### **Step 3: Create `main.tf`**
```bash
vi main.tf
```

**Content**:  
```hcl
# Configure AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Data source to get default VPC
data "aws_vpc" "default" {
  default = true
}

# Create Security Group
resource "aws_security_group" "xfusion_sg" {
  name        = "xfusion-sg"
  description = "Security group for Nautilus App Servers"
  vpc_id      = data.aws_vpc.default.id

  # Inbound: HTTP
  ingress {
    description = "Allow HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Inbound: SSH
  ingress {
    description = "Allow SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Outbound: Allow all (best practice)
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "xfusion-sg"
  }
}
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 4: Run Terraform Commands**
```bash
terraform init
```

**Expected**:
```
Initializing provider plugins...
Terraform has been successfully initialized!
```
```bash
terraform validate
```

**Expected**:
```
Success! The configuration is valid.
```
```bash
terraform plan
```

**Expected**:
```diff
+ create aws_security_group.xfusion_sg
    name:        "xfusion-sg"
    description: "Security group for Nautilus App Servers"
    ingress.#:   "2"
    egress.#:    "1"
    tags.Name:   "xfusion-sg"
```
```bash
terraform apply -auto-approve
```

**Expected**:
```
aws_security_group.xfusion_sg: Creating...
aws_security_group.xfusion_sg: Creation complete

Apply complete! Resources: 1 added.
```

---

## 🔍 Verification

### **AWS Console**
- **Region**: `us-east-1`
- **Security Groups** → Find `xfusion-sg`
- **Description**: Matches
- **Inbound Rules**:
  - `HTTP` → `80` → `0.0.0.0/0`
  - `SSH` → `22` → `0.0.0.0/0`

### **CLI Verification**
```bash
aws ec2 describe-security-groups \
  --region us-east-1 \
  --filters Name=tag:Name,Values=xfusion-sg
```

---

## 📊 Code Analysis

| **Block** | **Purpose** |
|-----------|-------------|
| `provider "aws"` | Sets region |
| `data "aws_vpc" "default"` | Fetches default VPC ID |
| `ingress {}` | HTTP & SSH rules |
| `egress {}` | Allows outbound (best practice) |
| `tags.Name` | Required for validation |

---

## 📖 Quick Command Reference
```bash
cd /home/bob/terraform

# Create file
vi main.tf

# Run Terraform
terraform init
terraform validate
terraform plan
terraform apply -auto-approve
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `default VPC not found` | Ensure account has default VPC |
| `Invalid description` | Use exact string |
| `Missing egress` | Add `egress` block |
| `Wrong region` | `region = "us-east-1"` |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Create SG in default VPC with exact name and rules

**💡 Solution**:  
```hcl
data "aws_vpc" "default" { default = true }
vpc_id = data.aws_vpc.default.id
```

**🎯 Key Success Factors**:
- ✅ `data` source for default VPC
- ✅ Exact name, description, ports
- ✅ `egress` block included
- ✅ Single `main.tf`

---

## ⚠️ Important Production Notes

### **Security Alert**:
- ⚠️ `0.0.0.0/0` is open to world
- ✅ Use bastion host or VPN in prod
- ✅ Restrict SSH to known IPs

### **Best Practice**:
```hcl
terraform {
  required_providers {
    aws = { source = "hashicorp/aws", version = "~> 5.0" }
  }
}
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `bob`
- [ ] Navigated to `/home/bob/terraform/`
- [ ] Created `main.tf` file
- [ ] Added `provider "aws"` with `region = "us-east-1"`
- [ ] Added `data "aws_vpc"` to fetch default VPC
- [ ] Created `resource "aws_security_group"` block
- [ ] Set `name = "xfusion-sg"`
- [ ] Set `description = "Security group for Nautilus App Servers"`
- [ ] Set `vpc_id = data.aws_vpc.default.id`
- [ ] Added HTTP ingress rule (port 80)
- [ ] Added SSH ingress rule (port 22)
- [ ] Both ingress rules use `cidr_blocks = ["0.0.0.0/0"]`
- [ ] Added egress rule for all outbound traffic
- [ ] Added `tags` with `Name = "xfusion-sg"`
- [ ] Ran `terraform init` successfully
- [ ] Ran `terraform validate` with success
- [ ] Ran `terraform plan` and reviewed output
- [ ] Ran `terraform apply -auto-approve`
- [ ] Verified security group in AWS console
- [ ] Confirmed inbound rules are correct
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Single file `main.tf` exists
- Provider configured for `us-east-1`
- Data source fetches default VPC
- Security group created with name `xfusion-sg`
- Description matches exactly
- HTTP (80) and SSH (22) ingress rules present
- Both rules allow `0.0.0.0/0`
- Egress rule allows all outbound traffic
- Tags include `Name = "xfusion-sg"`
- All Terraform commands execute successfully
- Security group visible in AWS console

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Terraform configuration for security group
- ✅ AWS provider configured for us-east-1
- ✅ Default VPC data source configured
- ✅ Security group with correct name and description
- ✅ HTTP and SSH ingress rules added
- ✅ Egress rule for outbound traffic
- ✅ Infrastructure deployed successfully

**Final Status**: Task 95 completed successfully!  
**Outcome**: Security group `xfusion-sg` created in default VPC in AWS us-east-1 — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using Terraform data sources
- ✅ Fetching default VPC ID
- ✅ Creating AWS security groups
- ✅ Configuring ingress rules
- ✅ Configuring egress rules
- ✅ Security group best practices
- ✅ Understanding CIDR blocks (`0.0.0.0/0`)

---

## 🚀 Next Steps

- Restrict SSH to specific IP ranges
- Create separate security groups per tier
- Implement bastion host pattern
- Add application-specific ports
- Use security group rules as separate resources
- Implement least privilege access
- Add descriptions to all rules

