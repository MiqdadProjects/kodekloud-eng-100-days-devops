# 🌟 Task 96 - Launch EC2 `devops-ec2` with RSA Key Pair using Terraform

**📌 Task Description**  
The **Nautilus DevOps team** continues **AWS migration**. This task:

**Goal**:  
- Launch **EC2 instance** named `devops-ec2`  
- Use **AMI**: `ami-0c101f26f147fa7fd` (Amazon Linux)  
- **Instance type**: `t2.micro`  
- Create **RSA key pair** named `devops-kp`  
- Attach **default security group**  
- Save **private key** locally  
- All in **single `main.tf`**  
- **Directory**: `/home/bob/terraform`

---

## 📋 Requirements Summary

| **Item** | **Value** |
|----------|-----------|
| Working Dir | `/home/bob/terraform` |
| File | `main.tf` (only) |
| Instance Name | `devops-ec2` |
| AMI | `ami-0c101f26f147fa7fd` |
| Type | `t2.micro` |
| Key Pair | `devops-kp` (RSA) |
| Security Group | `default` |
| Private Key Output | `/home/bob/devops-kp.pem` |
| Permissions | `0600` |

---

## 📝 Solution Overview

### **Resources Used**

| **Resource** | **Purpose** |
|--------------|-------------|
| `tls_private_key` | Generate RSA key |
| `aws_key_pair` | Upload public key to AWS |
| `local_file` | Save .pem locally |
| `data "aws_security_group"` | Get default SG |
| `aws_instance` | Launch EC2 |

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

# Generate RSA private key
resource "tls_private_key" "devops_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

# Create AWS key pair
resource "aws_key_pair" "devops_kp" {
  key_name   = "devops-kp"
  public_key = tls_private_key.devops_key.public_key_openssh
}

# Save private key to local file
resource "local_file" "private_key_pem" {
  content         = tls_private_key.devops_key.private_key_pem
  filename        = "/home/bob/devops-kp.pem"
  file_permission = "0600"
}

# Fetch default security group
data "aws_security_group" "default" {
  name   = "default"
  vpc_id = data.aws_vpc.default.id
}

# Fetch default VPC (required for SG data source)
data "aws_vpc" "default" {
  default = true
}

# Launch EC2 instance
resource "aws_instance" "devops_ec2" {
  ami                    = "ami-0c101f26f147fa7fd"
  instance_type          = "t2.micro"
  key_name               = aws_key_pair.devops_kp.key_name
  vpc_security_group_ids = [data.aws_security_group.default.id]

  tags = {
    Name = "devops-ec2"
  }
}
```



---

### **Step 4: Run Terraform Commands**
```bash
terraform init
```

**Expected**:
```
Terraform initialized successfully!
```
```bash
terraform apply -auto-approve
```

**Expected Output**:  
```
+ create tls_private_key.devops_key
+ create aws_key_pair.devops_kp
+ create local_file.private_key_pem
+ create aws_instance.devops_ec2

Apply complete! Resources: 4 added.
```

---

## 🔍 Verification

### **Check EC2 Instance**
```bash
aws ec2 describe-instances \
  --region us-east-1 \
  --filters "Name=tag:Name,Values=devops-ec2" "Name=instance-state-name,Values=running"
```

### **Check Key Pair**
```bash
aws ec2 describe-key-pairs --region us-east-1 --key-names devops-kp
```

### **Check Private Key File**
```bash
ls -l /home/bob/devops-kp.pem
```

**Expected**:
```
-rw------- 1 bob bob 3243 Nov 15 23:30 /home/bob/devops-kp.pem
```

---

## 📊 Code Analysis

| **Block** | **Purpose** |
|-----------|-------------|
| `tls_private_key` | Generates 4096-bit RSA key |
| `aws_key_pair` | Uploads public key |
| `local_file` | Saves .pem with 0600 |
| `data "aws_vpc"` | Needed for `data "aws_security_group"` |
| `vpc_security_group_ids` | Attaches default SG |

---

## 📖 Quick Command Reference
```bash
cd /home/bob/terraform

# Create file
vi main.tf

# Apply
terraform init
terraform apply -auto-approve
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `data.aws_security_group.default: no matching security group` | Add `data "aws_vpc" "default"` |
| `local_file permission denied` | Use absolute path `/home/bob/...` |
| Key not found | Ensure `key_name` matches |
| AMI not in region | Verify `us-east-1` |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Generate RSA key, upload, save locally, and attach to EC2 in one file

**💡 Solution**:  
```hcl
resource "tls_private_key" → aws_key_pair → local_file → aws_instance
```

**🎯 Key Success Factors**:  
- ✅ `tls_private_key` + `local_file`  
- ✅ Data sources for default VPC/SG  
- ✅ `file_permission = "0600"`  
- ✅ Single `main.tf`

---

## ⚠️ Important Production Notes

### **Security Alert**:  
- ❌ Never commit `.pem` files  
- ✅ Use S3 backend + encryption  
- ✅ Add `lifecycle { prevent_destroy = true }` for keys

### **Best Practice**:
```hcl
output "instance_public_ip" {
  value = aws_instance.devops_ec2.public_ip
}
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `bob`
- [ ] Navigated to `/home/bob/terraform/`
- [ ] Created `main.tf` file
- [ ] Added `provider "aws"` with `region = "us-east-1"`
- [ ] Added `tls_private_key` resource to generate RSA key
- [ ] Set `algorithm = "RSA"` and `rsa_bits = 4096`
- [ ] Added `aws_key_pair` resource named `devops-kp`
- [ ] Referenced `tls_private_key.devops_key.public_key_openssh`
- [ ] Added `local_file` resource to save private key
- [ ] Set `filename = "/home/bob/devops-kp.pem"`
- [ ] Set `file_permission = "0600"`
- [ ] Added `data "aws_vpc"` to get default VPC
- [ ] Added `data "aws_security_group"` to get default SG
- [ ] Added `aws_instance` resource named `devops_ec2`
- [ ] Set `ami = "ami-0c101f26f147fa7fd"`
- [ ] Set `instance_type = "t2.micro"`
- [ ] Set `key_name` to reference `aws_key_pair.devops_kp.key_name`
- [ ] Set `vpc_security_group_ids` to reference default SG
- [ ] Added `tags` with `Name = "devops-ec2"`
- [ ] Ran `terraform init` successfully
- [ ] Ran `terraform apply -auto-approve`
- [ ] Verified 4 resources created
- [ ] Verified EC2 instance running in AWS
- [ ] Verified key pair exists in AWS
- [ ] Verified private key file created locally
- [ ] Verified file permissions are `0600`
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Single file `main.tf` exists
- RSA key pair generated with 4096 bits
- AWS key pair `devops-kp` created
- Private key saved at `/home/bob/devops-kp.pem`
- File permissions set to `0600`
- EC2 instance `devops-ec2` launched
- Instance uses AMI `ami-0c101f26f147fa7fd`
- Instance type is `t2.micro`
- Instance attached to default security group
- Instance uses key pair `devops-kp`
- All resources created successfully

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ RSA key pair generated using Terraform
- ✅ Public key uploaded to AWS
- ✅ Private key saved locally with secure permissions
- ✅ EC2 instance launched with correct configuration
- ✅ Default security group attached
- ✅ All resources in single `main.tf`

**Final Status**: Task 96 completed successfully!  
**Outcome**: EC2 instance `devops-ec2` launched with RSA key pair `devops-kp` in AWS us-east-1 — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Generating RSA keys with `tls_private_key`
- ✅ Creating AWS key pairs
- ✅ Saving files locally with Terraform
- ✅ Setting file permissions in Terraform
- ✅ Using data sources for default resources
- ✅ Launching EC2 instances
- ✅ Attaching security groups to instances
- ✅ Resource dependencies in Terraform

---

## 🚀 Next Steps

- Add output for public IP address
- Create elastic IP for instance
- Add user data for bootstrapping
- Implement remote backend
- Add lifecycle rules for key pairs
- Create custom security group
- Add EBS volume attachment
- Implement auto-scaling group

