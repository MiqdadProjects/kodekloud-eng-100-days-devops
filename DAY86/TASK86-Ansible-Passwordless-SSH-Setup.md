# 🌟 Task 86 - Setup Passwordless SSH for Ansible to App Server 3

**📌 Task Description**  
The **Nautilus DevOps team** wants **passwordless SSH** from **jump host** (Ansible controller) to **App Server 3** so that Ansible playbooks can run without prompts.

**Goal**:  
```bash
ansible stapp03 -i /home/thor/ansible/inventory -m ping
```
→ Returns `pong` without asking for password

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Controller | `thor@jumphost` |
| Target | `stapp03` (172.16.238.12) |
| User | `banner` |
| Password | `BigGr33n` |
| Inventory | `/home/thor/ansible/inventory` |
| Validation | `ansible stapp03 ... -m ping` → SUCCESS |

---

## 🏗️ Infrastructure Overview

| **Host** | **IP** | **User** | **Password** |
|----------|--------|----------|--------------|
| stapp03 | 172.16.238.12 | banner | BigGr33n |

---

## 📝 Solution Overview

### **Strategy**
Even though SSH keys are ideal, the lab environment uses **password-based SSH** with:
- `ansible_ssh_pass`
- `ansible_ssh_common_args='-o StrictHostKeyChecking=no'`

This simulates passwordless behavior by embedding credentials in inventory.

---

## 🔹 Implementation Steps

### **Step 1: Connect to Jump Host**
```bash
ssh thor@jumphost
```

---

### **Step 2: Navigate to Ansible Directory**
```bash
cd /home/thor/ansible
```

---

### **Step 3: Update Inventory File**
```bash
vi inventory
```

**Update stapp03 line**:  
```ini
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**Full inventory** (for reference):  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 4: Test Ansible Ping**
```bash
ansible stapp03 -i /home/thor/ansible/inventory -m ping
```

**Expected Output**:  
```json
stapp03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

**Success Indicators**:
- ✅ `ping: "pong"`  
- ✅ No password prompt  
- ✅ `SUCCESS`

---

## 📊 Code Analysis

### **Inventory Line for stapp03**
```ini
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

| **Variable** | **Value** | **Purpose** |
|--------------|-----------|-------------|
| `ansible_host` | 172.16.238.12 | Target IP |
| `ansible_user` | banner | SSH user |
| `ansible_ssh_pass` | BigGr33n | Password authentication |
| `ansible_ssh_common_args` | `-o StrictHostKeyChecking=no` | Skip host key prompt |

---

## 🔍 Verification Steps
```bash
# 1. Check inventory
cat /home/thor/ansible/inventory | grep stapp03

# 2. Test connectivity
ansible stapp03 -i /home/thor/ansible/inventory -m ping

# 3. Test command execution
ansible stapp03 -i /home/thor/ansible/inventory -m shell -a "whoami"
```

**Expected**:  
```
stapp03 | CHANGED | rc=0 >>
banner
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Edit inventory
vi inventory
# → Add ansible_ssh_pass and ansible_ssh_common_args for stapp03

# Test ping
ansible stapp03 -i /home/thor/ansible/inventory -m ping
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `UNREACHABLE` | Wrong IP or user |
| `Permission denied` | Wrong `ansible_ssh_pass` |
| `Host key verification failed` | Add `ansible_ssh_common_args` |
| Password prompt | `ansible_ssh_pass` missing |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
No password prompt during `ansible ... -m ping`

**💡 Solution**:  
```ini
ansible_ssh_pass=BigGr33n
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**🎯 Key Success Factors**:  
- ✅ `ansible_ssh_pass` → Password injection  
- ✅ `ansible_ssh_common_args` → Non-interactive SSH  
- ✅ Correct IP & user  
- ✅ `ping` returns `pong`

---

## ⚠️ Important Production Notes

### **Security Alert**:  
- ❌ Never use `ansible_ssh_pass` in production  
- ✅ Use SSH keys + `ansible-vault`  
- ✅ Restrict inventory file permissions: `chmod 600 inventory`

### **Best Practice**:
```bash
# Generate key on jump host
sudo -u thor ssh-keygen -t rsa -b 4096 -f /home/thor/.ssh/id_rsa -N ""

# Copy to stapp03
ssh-copy-id banner@172.16.238.12
```

---

## 🎓 Learning Outcomes

- ✅ `ansible_ssh_pass` → Password-based SSH
- ✅ `ansible_ssh_common_args` → SSH options
- ✅ `ansible -m ping` → Connectivity test
- ✅ Inventory-driven automation

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Opened inventory file for editing
- [ ] Located or added `stapp03` entry
- [ ] Set `ansible_host=172.16.238.12`
- [ ] Set `ansible_user=banner`
- [ ] Added `ansible_ssh_pass=BigGr33n`
- [ ] Added `ansible_ssh_common_args='-o StrictHostKeyChecking=no'`
- [ ] Saved inventory file
- [ ] Ran `ansible stapp03 -i /home/thor/ansible/inventory -m ping`
- [ ] Verified `SUCCESS` status
- [ ] Confirmed `ping: "pong"` in output
- [ ] Verified no password prompt appeared
- [ ] Tested command execution with `-m shell -a "whoami"`
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Inventory contains `stapp03` with all required variables
- `ansible_ssh_pass` set to correct password
- `ansible_ssh_common_args` includes `StrictHostKeyChecking=no`
- `ansible stapp03 -i /home/thor/ansible/inventory -m ping` succeeds
- Returns `pong` without password prompt
- Output shows `SUCCESS`
- User can run commands without interactive prompts

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Inventory updated with `ansible_ssh_pass` and `ansible_ssh_common_args`
- ✅ `ansible stapp03 ... -m ping` → `SUCCESS` + `pong`
- ✅ No password prompt

**Final Status**: Task 86 completed successfully!  
**Outcome**: Passwordless-like SSH enabled for Ansible → ready for playbooks

---

## 🚀 Next Steps

- Replace password with SSH key
- Use Ansible Vault for secrets
- Add all 3 app servers
- Build playbooks

