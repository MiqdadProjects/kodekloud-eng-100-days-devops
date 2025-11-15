# 🌟 Task 99 - Secure DynamoDB Table + Read-Only IAM Role using Terraform

**📌 Task Description**  
The **Nautilus DevOps team** needs a **secure DynamoDB setup** with **fine-grained IAM access**:

**Goal**:
- Create **DynamoDB table** → `nautilus-table`
- Create **IAM Role** → `nautilus-role`
- Create **IAM Policy** → `nautilus-readonly-policy` → Read-only (GetItem, Scan, Query)
- **Attach policy to role**
- Use **variables, outputs, tfvars**
- **No changes** on `terraform plan`

---

## 📋 Requirements Summary

| **Resource** | **Name** | **Access** | **Scope** |
|--------------|----------|------------|-----------|
| DynamoDB | nautilus-table | Minimal | ID hash key |
| IAM Role | nautilus-role | ec2.amazonaws.com | Trust policy |
| IAM Policy | nautilus-readonly-policy | Read-only | Specific table ARN |
| Attachment | Role + Policy | — | — |

---

## 📝 File Structure
```
/home/bob/terraform/
├── main.tf
├── variables.tf
├── terraform.tfvars
└── outputs.tf
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

### **Step 3: Create `variables.tf`**
```bash
vi variables.tf
```

**Content**:  
```hcl
variable "KKE_TABLE_NAME" {
  description = "Name of the DynamoDB table"
  type        = string
}

variable "KKE_ROLE_NAME" {
  description = "Name of the IAM role"
  type        = string
}

variable "KKE_POLICY_NAME" {
  description = "Name of the IAM policy"
  type        = string
}
```

---

### **Step 4: Create `terraform.tfvars`**
```bash
vi terraform.tfvars
```

**Content**:  
```hcl
KKE_TABLE_NAME     = "nautilus-table"
KKE_ROLE_NAME      = "nautilus-role"
KKE_POLICY_NAME    = "nautilus-readonly-policy"
```

---

### **Step 5: Create `main.tf`**
```bash
vi main.tf
```

**Content**:  
```hcl
# Configure AWS Provider
provider "aws" {
  region = "us-east-1"
}

# -------------------------
# DynamoDB Table (Minimal)
# -------------------------
resource "aws_dynamodb_table" "nautilus_table" {
  name           = var.KKE_TABLE_NAME
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "ID"

  attribute {
    name = "ID"
    type = "S"
  }

  tags = {
    Name = var.KKE_TABLE_NAME
  }
}

# -------------------------
# IAM Role (Trusted by EC2)
# -------------------------
resource "aws_iam_role" "nautilus_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect    = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
        Action    = "sts:AssumeRole"
      }
    ]
  })

  tags = {
    Name = var.KKE_ROLE_NAME
  }
}

# -------------------------
# IAM Policy: Read-only on specific table
# -------------------------
resource "aws_iam_policy" "nautilus_policy" {
  name        = var.KKE_POLICY_NAME
  description = "Read-only access to the ${var.KKE_TABLE_NAME} DynamoDB table"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = [
          "dynamodb:GetItem",
          "dynamodb:Scan",
          "dynamodb:Query"
        ]
        Resource = aws_dynamodb_table.nautilus_table.arn
      }
    ]
  })
}

# -------------------------
# Attach Policy to Role
# -------------------------
resource "aws_iam_role_policy_attachment" "nautilus_attach" {
  role       = aws_iam_role.nautilus_role.name
  policy_arn = aws_iam_policy.nautilus_policy.arn
}
```

---

### **Step 6: Create `outputs.tf`**
```bash
vi outputs.tf
```

**Content**:  
```hcl
output "kke_dynamodb_table" {
  description = "Name of the DynamoDB table"
  value       = aws_dynamodb_table.nautilus_table.name
}

output "kke_iam_role_name" {
  description = "Name of the IAM role"
  value       = aws_iam_role.nautilus_role.name
}

output "kke_iam_policy_name" {
  description = "Name of the IAM policy"
  value       = aws_iam_policy.nautilus_policy.name
}
```

---

### **Step 7: Run Terraform**
```bash
terraform init
terraform validate
terraform plan
```

**Expected**:  
```
No changes. Your infrastructure matches the configuration.
```
```bash
terraform apply -auto-approve
```

**Expected**:  
```
+ create aws_dynamodb_table.nautilus_table
+ create aws_iam_role.nautilus_role
+ create aws_iam_policy.nautilus_policy
+ create aws_iam_role_policy_attachment.nautilus_attach

Apply complete! Resources: 4 added.
```

---

## 🔍 Verification

### **Check DynamoDB Table**
```bash
aws dynamodb describe-table --table-name nautilus-table --query "Table.TableName"
```

**Expected**: `"nautilus-table"`

### **Check IAM Policy**
```bash
aws iam get-policy --policy-arn $(aws iam list-policies --query "Policies[?PolicyName=='nautilus-readonly-policy'].Arn" --output text)
```

### **Check Role Attachment**
```bash
aws iam list-attached-role-policies --role-name nautilus-role
```

---

## 📊 Code Analysis

| **Feature** | **Implementation** |
|-------------|-------------------|
| Minimal Table | `PAY_PER_REQUEST`, ID hash key |
| Least Privilege | `Resource = table.arn` |
| Trust Policy | `ec2.amazonaws.com` |
| Idempotency | `terraform plan` → no changes |

---

## 📖 Quick Command Reference
```bash
cd /home/bob/terraform

# Initialize & Apply
terraform init
terraform plan    # Should show "No changes"
terraform apply -auto-approve

# Verify
terraform output
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `Resource = "*"` | Use `aws_dynamodb_table.nautilus_table.arn` |
| Role not trusted | `Service = "ec2.amazonaws.com"` |
| Policy not attached | Add `aws_iam_role_policy_attachment` |
| `terraform plan` shows changes | Re-run apply or fix drift |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Fine-grained, table-specific read-only access

**💡 Solution**:  
```hcl
Resource = aws_dynamodb_table.nautilus_table.arn
Action   = ["dynamodb:GetItem", "dynamodb:Scan", "dynamodb:Query"]
```

**🎯 Key Success Factors**:  
- ✅ `arn` reference → specific table  
- ✅ `aws_iam_role_policy_attachment`  
- ✅ Variables & outputs with exact names  
- ✅ `terraform plan` → no changes

---

## ⚠️ Important Production Notes

### **Best Practice**:
- Use instance profile for EC2  
- Enable DynamoDB encryption  
- Add tags for cost tracking

### **Enhanced Security**:
```hcl
# Add condition
Condition = {
  "StringEquals" = { "aws:ResourceTag/Environment" = "prod" }
}
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `bob`
- [ ] Navigated to `/home/bob/terraform/`
- [ ] Created `variables.tf` with 3 variables
- [ ] Variable names: `KKE_TABLE_NAME`, `KKE_ROLE_NAME`, `KKE_POLICY_NAME`
- [ ] Created `terraform.tfvars` with correct values
- [ ] Created `main.tf` with all resources
- [ ] DynamoDB table with `PAY_PER_REQUEST` billing
- [ ] Hash key set to `ID` of type `S`
- [ ] IAM role with EC2 trust policy
- [ ] IAM policy with read-only DynamoDB actions
- [ ] Policy resource uses table ARN reference
- [ ] Policy attachment connects role and policy
- [ ] Created `outputs.tf` with 3 outputs
- [ ] Output names: `kke_dynamodb_table`, `kke_iam_role_name`, `kke_iam_policy_name`
- [ ] Ran `terraform init` successfully
- [ ] Ran `terraform validate` with success
- [ ] Ran `terraform plan` (shows no changes after apply)
- [ ] Ran `terraform apply -auto-approve`
- [ ] Verified DynamoDB table created
- [ ] Verified IAM role created
- [ ] Verified IAM policy created
- [ ] Verified policy attached to role
- [ ] Verified outputs display correctly
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Four files exist with correct names
- DynamoDB table `nautilus-table` created
- Table uses `PAY_PER_REQUEST` billing mode
- Table has hash key `ID` of type String
- IAM role `nautilus-role` created
- Role has trust policy for `ec2.amazonaws.com`
- IAM policy `nautilus-readonly-policy` created
- Policy allows only GetItem, Scan, Query
- Policy resource references specific table ARN
- Policy attached to role via `aws_iam_role_policy_attachment`
- Variables use exact names as specified
- Outputs use exact names as specified
- `terraform plan` shows no changes after apply
- All resources created successfully

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ DynamoDB table with minimal configuration
- ✅ IAM role with EC2 trust policy
- ✅ Read-only IAM policy for specific table
- ✅ Policy attachment to role
- ✅ Variables and outputs configured
- ✅ Idempotent infrastructure

**Final Status**: Task 99 completed successfully!  
**Outcome**: Secure DynamoDB table with fine-grained IAM access created in AWS us-east-1 — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Creating DynamoDB tables with Terraform
- ✅ Configuring billing modes
- ✅ Defining hash keys and attributes
- ✅ Creating IAM roles with trust policies
- ✅ Writing fine-grained IAM policies
- ✅ Using resource ARN references
- ✅ Attaching policies to roles
- ✅ Multi-file Terraform organization
- ✅ Least privilege access patterns

---

## 🚀 Next Steps

- Create EC2 instance profile
- Attach role to EC2 instances
- Enable DynamoDB encryption
- Add DynamoDB streams
- Implement backup policies
- Add CloudWatch alarms
- Create additional indexes
- Implement data lifecycle policies


