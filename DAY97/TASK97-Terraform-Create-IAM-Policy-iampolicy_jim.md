# 🌟 Task 97 - Create IAM Read-Only EC2 Policy `iampolicy_jim` using Terraform

**📌 Task Description**  
The **Nautilus DevOps team** is setting up **IAM** for secure AWS access. This task:

**Goal**:  
- Create **IAM Policy** named `iampolicy_jim`  
- **Read-only access** to EC2 console  
- Allow viewing: **Instances, AMIs, Snapshots**  
- **Region**: Global (IAM is region-agnostic)  
- **File**: `/home/bob/terraform/main.tf` (single file)

---

## 📋 Requirements Summary

| **Item** | **Value** |
|----------|-----------|
| Working Dir | `/home/bob/terraform` |
| File | `main.tf` |
| Policy Name | `iampolicy_jim` |
| Access | Read-only (Describe*) |
| Services | EC2 only |
| Resources | `*` (all) |
| Effect | Allow |

---

## 📝 Solution Overview

### **IAM Policy Scope**
- Use `ec2:Describe*` actions → read-only
- Resource = `"*"` → all EC2 resources
- `jsonencode()` → clean HCL syntax

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
# Configure AWS Provider (IAM is global, but region must be set)
provider "aws" {
  region = "us-east-1"
}

# Create IAM Policy: Read-only EC2 access
resource "aws_iam_policy" "iampolicy_jim" {
  name        = "iampolicy_jim"
  description = "Read-only EC2 access policy for users to view instances, AMIs, and snapshots."

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = [
          "ec2:DescribeInstances",
          "ec2:DescribeImages",
          "ec2:DescribeSnapshots",
          "ec2:DescribeVolumes",
          "ec2:DescribeSecurityGroups",
          "ec2:DescribeVpcs",
          "ec2:DescribeSubnets",
          "ec2:DescribeTags",
          "ec2:DescribeKeyPairs",
          "ec2:DescribeAddresses",
          "ec2:DescribeNetworkInterfaces",
          "ec2:Describe*"
        ]
        Resource = "*"
      }
    ]
  })
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
Terraform initialized successfully!
```
```bash
terraform apply -auto-approve
```

**Expected Output**:  
```
+ create aws_iam_policy.iampolicy_jim
    name:        "iampolicy_jim"
    policy:      "{...Describe*...}"

Apply complete! Resources: 1 added.
```

---

## 🔍 Verification

### **AWS Console**
1. **IAM** → **Policies** → Search `iampolicy_jim`
2. **Permissions** tab → **JSON**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeImages",
        "ec2:DescribeSnapshots",
        ...
        "ec2:Describe*"
      ],
      "Resource": "*"
    }
  ]
}
```

### **CLI Verification**
```bash
aws iam get-policy --policy-arn $(aws iam list-policies --query "Policies[?PolicyName=='iampolicy_jim'].Arn" --output text)
```

---

## 📊 Code Analysis

| **Component** | **Purpose** |
|---------------|-------------|
| `provider "aws"` | Required even for global services |
| `jsonencode()` | Embeds valid JSON policy |
| `ec2:Describe*` | Wildcard for all read actions |
| `Resource = "*"` | Applies to all EC2 resources |

**Note**: `ec2:Describe*` is safe and comprehensive for console read access.

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
| Invalid policy JSON | Use `jsonencode()` |
| Missing provider | Add `provider "aws"` block |
| Policy not found | Wait 10s for propagation |
| Wildcard not allowed | `ec2:Describe*` is allowed |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Allow full EC2 console read access without `ec2:*`

**💡 Solution**:  
```json
"ec2:Describe*"
```
→ Covers all read-only actions safely

**🎯 Key Success Factors**:  
- ✅ `jsonencode()` for clean syntax  
- ✅ `ec2:Describe*` wildcard  
- ✅ Exact name and description  
- ✅ Single `main.tf`

---

## ⚠️ Important Production Notes

### **Best Practice**:  
- Use least privilege  
- Attach policy via groups, not users  
- Add versioning and MFA

### **Enhanced Policy (Optional)**:
```hcl
# Add condition to restrict to specific VPC
Condition = {
  StringEquals = { "aws:RequestedRegion" = "us-east-1" }
}
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `bob`
- [ ] Navigated to `/home/bob/terraform/`
- [ ] Created `main.tf` file
- [ ] Added `provider "aws"` with `region = "us-east-1"`
- [ ] Added `resource "aws_iam_policy"` block
- [ ] Set `name = "iampolicy_jim"`
- [ ] Added appropriate description
- [ ] Used `jsonencode()` for policy document
- [ ] Set `Version = "2012-10-17"`
- [ ] Added `Statement` array with one statement
- [ ] Set `Effect = "Allow"`
- [ ] Added `Action` array with EC2 Describe actions
- [ ] Included `ec2:Describe*` wildcard
- [ ] Set `Resource = "*"`
- [ ] Ran `terraform init` successfully
- [ ] Ran `terraform apply -auto-approve`
- [ ] Verified policy created in AWS console
- [ ] Verified policy JSON is correct
- [ ] Confirmed read-only permissions
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Single file `main.tf` exists
- Provider configured (region required even for IAM)
- IAM policy named `iampolicy_jim` created
- Policy includes read-only EC2 actions
- Uses `ec2:Describe*` for comprehensive coverage
- Policy applies to all resources (`"*"`)
- `jsonencode()` used for clean syntax
- Policy visible in AWS IAM console
- All Terraform commands execute successfully

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ IAM policy for read-only EC2 access
- ✅ Policy named `iampolicy_jim`
- ✅ Comprehensive Describe actions included
- ✅ Clean JSON policy using `jsonencode()`
- ✅ Applied to all EC2 resources
- ✅ Infrastructure deployed successfully

**Final Status**: Task 97 completed successfully!  
**Outcome**: IAM policy `iampolicy_jim` created with read-only EC2 access — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Creating IAM policies with Terraform
- ✅ Using `jsonencode()` for policy documents
- ✅ Understanding IAM policy structure
- ✅ Using wildcard actions (`ec2:Describe*`)
- ✅ Read-only vs. full access permissions
- ✅ IAM best practices
- ✅ Global service configuration in Terraform

---

## 🚀 Next Steps

- Attach policy to IAM user or group
- Create IAM role with this policy
- Add conditions for enhanced security
- Implement policy versioning
- Create custom policies for other services
- Add MFA requirements
- Implement least privilege access

