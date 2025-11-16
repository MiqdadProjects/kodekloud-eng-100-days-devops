# 🌟 Task 100 - Launch EC2 + CloudWatch CPU Alarm → SNS Notification

**📌 Task Description**  
The **Nautilus DevOps team** needs to:
1. Launch **EC2 instance** → `nautilus-ec2` (Ubuntu)
2. Create **CloudWatch alarm** → `nautilus-alarm`
3. Trigger on **CPU ≥ 90%** for **1 period of 5 minutes**
4. Send alert to **existing SNS topic**: `nautilus-sns-topic`

---

## 📋 Requirements Summary

| **Resource** | **Name** | **Details** |
|--------------|----------|-------------|
| EC2 | nautilus-ec2 | AMI: ami-0c02fb55956c7d316, t2.micro |
| CloudWatch Alarm | nautilus-alarm | CPUUtilization, Average, ≥90%, 1×5min |
| SNS Topic | nautilus-sns-topic | Already exists → reference only |
| Outputs | KKE_instance_name, KKE_alarm_name | — |

---

## 📝 File Structure
```
/home/bob/terraform/
├── main.tf
└── outputs.tf
```

**Note**: SNS topic already exists → do not create, just reference.

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
# Configure AWS Provider
provider "aws" {
  region = "us-east-1"
}

# -------------------------
# EC2 Instance: Ubuntu
# -------------------------
resource "aws_instance" "nautilus_ec2" {
  ami           = "ami-0c02fb55956c7d316"  # Ubuntu 22.04 LTS
  instance_type = "t2.micro"

  tags = {
    Name = "nautilus-ec2"
  }
}

# -------------------------
# Reference Existing SNS Topic (DO NOT CREATE)
# -------------------------
data "aws_sns_topic" "existing_sns" {
  name = "nautilus-sns-topic"
}

# -------------------------
# CloudWatch Alarm: CPU >= 90% for 5 mins
# -------------------------
resource "aws_cloudwatch_metric_alarm" "nautilus_alarm" {
  alarm_name          = "nautilus-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300  # 5 minutes
  statistic           = "Average"
  threshold           = 90
  alarm_description   = "Alarm when CPU utilization >= 90% for 5 minutes"
  
  alarm_actions = [data.aws_sns_topic.existing_sns.arn]

  dimensions = {
    InstanceId = aws_instance.nautilus_ec2.id
  }

  tags = {
    Name = "nautilus-alarm"
  }
}
```

---

### **Step 4: Create `outputs.tf`**
```bash
vi outputs.tf
```

**Content**:  
```hcl
output "KKE_instance_name" {
  description = "Name of the EC2 instance"
  value       = aws_instance.nautilus_ec2.tags["Name"]
}

output "KKE_alarm_name" {
  description = "Name of the CloudWatch alarm"
  value       = aws_cloudwatch_metric_alarm.nautilus_alarm.alarm_name
}
```

---

### **Step 5: Run Terraform**
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
+ create aws_instance.nautilus_ec2
+ create aws_cloudwatch_metric_alarm.nautilus_alarm

Apply complete! Resources: 2 added.
```

---

## 🔍 Verification

### **Check EC2 Instance**
```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=nautilus-ec2" "Name=instance-state-name,Values=running" \
  --query "Reservations[].Instances[].InstanceId"
```

### **Check CloudWatch Alarm**
```bash
aws cloudwatch describe-alarms --alarm-names nautilus-alarm
```

### **Check SNS Subscription (Manual)**
1. Go to **SNS Console** → `nautilus-sns-topic`
2. Confirm alarm is listed

---

## 📊 Code Analysis

| **Feature** | **Implementation** |
|-------------|-------------------|
| Existing SNS | `data "aws_sns_topic"` → no creation |
| Alarm Trigger | ≥90%, 1 period, 300s, Average |
| Dimension | `InstanceId = aws_instance.id` |
| Outputs | Exact names: `KKE_instance_name`, `KKE_alarm_name` |

---

## 📖 Quick Command Reference
```bash
cd /home/bob/terraform

# Initialize
terraform init

# Plan (should show no changes after apply)
terraform plan

# Apply
terraform apply -auto-approve

# Verify
terraform output
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| SNS topic not found | Use `data "aws_sns_topic"` |
| Alarm not linked | Use `data.aws_sns_topic.existing_sns.arn` |
| `terraform plan` shows changes | Re-apply or fix drift |
| AMI not in region | Confirm `us-east-1` |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Reference existing SNS topic without creating it

**💡 Solution**:  
```hcl
data "aws_sns_topic" "existing_sns" { name = "nautilus-sns-topic" }
```

**🎯 Key Success Factors**:  
- ✅ Data source for existing SNS  
- ✅ `period = 300`, `evaluation_periods = 1`  
- ✅ `dimensions.InstanceId`  
- ✅ `terraform plan` → no changes

---

## ⚠️ Important Production Notes

### **Best Practice**:
- Add OK actions  
- Use email/SMS subscription  
- Enable CloudWatch agent for deeper metrics

### **Enhanced Alarm**:
```hcl
treat_missing_data = "missing"
datapoints_to_alarm = 1
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `bob`
- [ ] Navigated to `/home/bob/terraform/`
- [ ] Created `main.tf` with all resources
- [ ] Added EC2 instance with correct AMI and type
- [ ] Added `data` source for existing SNS topic
- [ ] Created CloudWatch alarm resource
- [ ] Set `alarm_name = "nautilus-alarm"`
- [ ] Set `comparison_operator = "GreaterThanOrEqualToThreshold"`
- [ ] Set `evaluation_periods = 1`
- [ ] Set `metric_name = "CPUUtilization"`
- [ ] Set `namespace = "AWS/EC2"`
- [ ] Set `period = 300` (5 minutes)
- [ ] Set `statistic = "Average"`
- [ ] Set `threshold = 90`
- [ ] Set `alarm_actions` to SNS topic ARN
- [ ] Set `dimensions.InstanceId` to EC2 instance ID
- [ ] Created `outputs.tf` with 2 outputs
- [ ] Output names: `KKE_instance_name`, `KKE_alarm_name`
- [ ] Ran `terraform init` successfully
- [ ] Ran `terraform validate` with success
- [ ] Ran `terraform plan` (shows no changes after apply)
- [ ] Ran `terraform apply -auto-approve`
- [ ] Verified EC2 instance running
- [ ] Verified CloudWatch alarm created
- [ ] Verified alarm linked to SNS topic
- [ ] Verified outputs display correctly
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Two files exist: `main.tf`, `outputs.tf`
- EC2 instance `nautilus-ec2` created with Ubuntu AMI
- Instance type is `t2.micro`
- CloudWatch alarm `nautilus-alarm` created
- Alarm monitors `CPUUtilization` metric
- Threshold set to 90%
- Evaluation period is 1 × 5 minutes (300 seconds)
- Statistic is `Average`
- Alarm linked to existing SNS topic via data source
- Alarm dimensions reference EC2 instance ID
- Outputs use exact names as specified
- `terraform plan` shows no changes after apply
- All resources created successfully

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ EC2 instance launched with Ubuntu AMI
- ✅ CloudWatch alarm configured for CPU monitoring
- ✅ Alarm threshold set to 90%
- ✅ Alarm linked to existing SNS topic
- ✅ Outputs configured correctly
- ✅ Monitoring and alerting infrastructure complete

**Final Status**: Task 100 completed successfully!  
**Outcome**: EC2 instance with CloudWatch CPU alarm and SNS notification created in AWS us-east-1 — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Launching EC2 instances with Terraform
- ✅ Creating CloudWatch metric alarms
- ✅ Configuring alarm thresholds and periods
- ✅ Using data sources for existing resources
- ✅ Referencing existing SNS topics
- ✅ Linking alarms to notification channels
- ✅ Setting CloudWatch dimensions
- ✅ Monitoring and alerting patterns

---

## 🚀 Next Steps

- Subscribe email to SNS topic
- Test alarm by simulating high CPU
- Add OK actions for recovery notifications
- Create dashboard for monitoring
- Add additional metrics (memory, disk)
- Implement auto-scaling based on alarms
- Add composite alarms for complex scenarios
- Enable detailed monitoring

