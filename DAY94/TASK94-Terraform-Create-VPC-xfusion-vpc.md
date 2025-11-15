# 🌟 Task 94 - Create VPC `xfusion-vpc` in AWS using Terraform

**📌 Task Description**  
The **Nautilus DevOps team** is migrating incrementally to **AWS**. As part of **Phase 1**, create a **VPC** using **Terraform**:

**Goal**:
- **VPC Name**: `xfusion-vpc`
- **Region**: `us-east-1`
- **CIDR**: Any valid IPv4 block
- **File**: `/home/bob/terraform/main.tf`
- **Single file only**

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Working Directory | `/home/bob/terraform` |
| File | `main.tf` (only) |
| Resource | `aws_vpc` |
| VPC Name | `xfusion-vpc` |
| Region | `us-east-1` |
| CIDR | Any valid (e.g., `10.0.0.0/16`) |
| Commands | `init`, `validate`, `plan`, `apply -auto-approve` |

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

### **Step 2: Navigate to Terraform Directory**
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
# Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Create VPC named xfusion-vpc
resource "aws_vpc" "xfusion_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "xfusion-vpc"
  }
}
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 4: Initialize Terraform**
```bash
terraform init
```

**Expected Output**:  
```
Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v5.x.x...
- Installed hashicorp/aws v5.x.x

Terraform has been successfully initialized!
```

---

### **Step 5: Validate Configuration**
```bash
terraform validate
```

**Expected**:  
```
Success! The configuration is valid.
```

---

### **Step 6: Review Plan**
```bash
terraform plan
```

**Expected Output**:  
```diff
+ create aws_vpc.xfusion_vpc
    cidr_block: "10.0.0.0/16"
    tags.Name:  "xfusion-vpc"
```

---

### **Step 7: Apply Configuration**
```bash
terraform apply -auto-approve
```

**Expected Output**:  
```diff
aws_vpc.xfusion_vpc: Creating...
aws_vpc.xfusion_vpc: Creation complete after 3s [id=vpc-0abcd1234efgh5678]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

---

## 🔍 Verification

### **Check AWS Console**
- **Region**: `us-east-1 (N. Virginia)`
- **VPC Name**: `xfusion-vpc`
- **CIDR**: `10.0.0.0/16`

### **Or via CLI**
```bash
aws ec2 describe-vpcs --region us-east-1 --filters "Name=tag:Name,Values=xfusion-vpc"
```

---

## 📊 Code Analysis

### **`main.tf`**
```hcl
provider "aws" {
  region = "us-east-1"
}
```
→ Sets region globally
```hcl
resource "aws_vpc" "xfusion_vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "xfusion-vpc"
  }
}
```
→ Creates VPC with required name tag

| **Field** | **Value** | **Purpose** |
|-----------|-----------|-------------|
| `cidr_block` | `10.0.0.0/16` | Private RFC 1918 |
| `tags.Name` | `xfusion-vpc` | Required |

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
| `Provider not initialized` | Run `terraform init` |
| `Invalid CIDR` | Use `/16` to `/28` |
| `Missing region` | Add `provider "aws"` block |
| `VPC not found` | Check region & tags |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Create VPC with exact name in single `main.tf`

**💡 Solution**:  
```hcl
tags = { Name = "xfusion-vpc" }
```

**🎯 Key Success Factors**:
- ✅ `provider "aws"` block
- ✅ `tags.Name`
- ✅ `terraform apply -auto-approve`
- ✅ Single file

---

## ⚠️ Important Production Notes

### **Best Practice**:
- Use remote backend (S3 + DynamoDB)
- Version pin provider: `required_providers`
- Modules for reusability

### **Security**:
- Use IAM role or env vars
- Never commit `.tfstate`

### **Example (Production-Ready)**:
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `bob`
- [ ] Navigated to `/home/bob/terraform/`
- [ ] Created `main.tf` file
- [ ] Added `provider "aws"` block with `region = "us-east-1"`
- [ ] Added `resource "aws_vpc"` block
- [ ] Set `cidr_block = "10.0.0.0/16"`
- [ ] Added `tags` with `Name = "xfusion-vpc"`
- [ ] Ran `terraform init` successfully
- [ ] Ran `terraform validate` with success
- [ ] Ran `terraform plan` and reviewed output
- [ ] Ran `terraform apply -auto-approve`
- [ ] Verified VPC created in AWS console
- [ ] Confirmed VPC name is `xfusion-vpc`
- [ ] Confirmed region is `us-east-1`
- [ ] Confirmed CIDR block is correct
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Single file `main.tf` exists
- Provider configured for `us-east-1`
- VPC resource created with name `xfusion_vpc`
- CIDR block is valid IPv4
- Tags include `Name = "xfusion-vpc"`
- `terraform init` completes without errors
- `terraform validate` shows success
- `terraform apply -auto-approve` creates VPC
- VPC visible in AWS console with correct name

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Terraform configuration created
- ✅ AWS provider configured for us-east-1
- ✅ VPC resource defined with correct name
- ✅ CIDR block set to 10.0.0.0/16
- ✅ Infrastructure deployed successfully
- ✅ VPC created in AWS

**Final Status**: Task 94 completed successfully!  
**Outcome**: VPC `xfusion-vpc` created in AWS us-east-1 using Terraform — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Terraform basics: `init`, `validate`, `plan`, `apply`
- ✅ AWS provider configuration
- ✅ Creating VPC resources
- ✅ Using tags for resource naming
- ✅ CIDR block configuration
- ✅ Single-file Terraform setup
- ✅ Infrastructure as Code (IaC) principles

---

## 🚀 Next Steps

- Add subnets to the VPC
- Create Internet Gateway
- Configure route tables
- Add security groups
- Implement remote backend (S3)
- Use Terraform modules
- Add variables and outputs

