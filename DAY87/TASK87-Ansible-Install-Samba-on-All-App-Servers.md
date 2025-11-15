# 🌟 Task 87 - Install Samba on All App Servers Using Ansible

**📌 Task Description**  
The **Nautilus Application team** needs **samba** installed on all **3 App Servers** in **Stratos DC** for testing. The **DevOps team** will use **Ansible** to automate this.

**Goal**:  
- Install `samba` using `yum` module  
- Run from `thor` user on jump host  
- **Validation**: `ansible-playbook -i inventory playbook.yml`

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory Path | `/home/thor/playbook/inventory` |
| Playbook Path | `/home/thor/playbook/playbook.yml` |
| Package | `samba` |
| Module | `yum` |
| State | `present` |
| User | `thor` |
| Execution | No extra args |

---

## 🏗️ Infrastructure Overview

| **Host** | **IP** | **User** | **Password** |
|----------|--------|----------|--------------|
| stapp01 | 172.16.238.10 | tony | Ir0nM@n |
| stapp02 | 172.16.238.11 | steve | Am3ric@ |
| stapp03 | 172.16.238.12 | banner | BigGr33n |

---

## 📝 Solution Overview

### **Strategy**
- Use `yum` module with `state: present` → idempotent install
- `become: yes` → root privileges
- `hosts: all` → all 3 servers

---

## 🔹 Implementation Steps

### **Step 1: Connect to Jump Host**
```bash
ssh thor@jumphost
```

---

### **Step 2: Create Playbook Directory**
```bash
mkdir -p /home/thor/playbook && cd /home/thor/playbook
```

---

### **Step 3: Create Inventory File**
```bash
vi inventory
```

**Content**:  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 4: Create Playbook**
```bash
vi playbook.yml
```

**Content**:  
```yaml
---
- name: Install samba package on all App Servers
  hosts: all
  become: yes
  tasks:
    - name: Ensure samba is installed
      yum:
        name: samba
        state: present
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 5: Run the Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
PLAY [Install samba package on all App Servers] *********************************

TASK [Gathering Facts] *********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Ensure samba is installed] ***********************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP *********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```

---

### **Step 6: Verify Installation**
```bash
ansible all -i inventory -m shell -a "rpm -q samba"
```

**Expected Output**:  
```
stapp01 | CHANGED | rc=0 >>
samba-4.23.0-3.el9.x86_64

stapp02 | CHANGED | rc=0 >>
samba-4.23.0-3.el9.x86_64

stapp03 | CHANGED | rc=0 >>
samba-4.23.0-3.el9.x86_64
```

---

## 📊 Code Analysis

### **Inventory File** (`/home/thor/playbook/inventory`)
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

---

### **Playbook** (`/home/thor/playbook/playbook.yml`)
```yaml
---
- name: Install samba package on all App Servers
  hosts: all
  become: yes
  tasks:
    - name: Ensure samba is installed
      yum:
        name: samba
        state: present
```

| **Parameter** | **Value** | **Purpose** |
|---------------|-----------|-------------|
| `hosts: all` | All 3 servers | Mass deployment |
| `become: yes` | Root | Install packages |
| `name: samba` | Package | Target |
| `state: present` | Install if missing | Idempotent |

---

## 🔍 Verification Steps
```bash
# Check inventory
cat /home/thor/playbook/inventory

# Dry run
ansible-playbook -i inventory playbook.yml --check

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "rpm -q samba"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/playbook

# Inventory
vi inventory
# → 3 hosts with ansible_ssh_pass

# Playbook
vi playbook.yml
# → yum: name=samba, state=present

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "rpm -q samba"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `UNREACHABLE` | Check IP, user, password |
| `Permission denied` | Add `become: yes` |
| Package not found | Use correct name: `samba` |
| `yum` module fails | Ensure RHEL/CentOS |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Install samba on all 3 servers using `yum` module with no extra args.

**💡 Solution**:  
```yaml
yum:
  name: samba
  state: present
```

**🎯 Key Success Factors**:  
- ✅ `become: yes`  
- ✅ `state: present` → idempotent  
- ✅ `hosts: all`  
- ✅ Correct inventory credentials

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Ansible Vault  
🔐 **Best Practice**: Pin version: `samba-4.23.0`  
📊 **Idempotency**: `state: present` is safe  
🛡️ **Logging**: Add `register` and `debug`

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Created directory `/home/thor/playbook/`
- [ ] Created inventory with all 3 app servers
- [ ] Set correct IP addresses, usernames, and passwords
- [ ] Created `playbook.yml` with `hosts: all`
- [ ] Added `become: yes` for root privileges
- [ ] Used `yum` module with `name: samba`
- [ ] Set `state: present` for idempotent installation
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified all 3 servers show `changed: 1`
- [ ] Confirmed samba installed with `rpm -q samba`
- [ ] All servers return samba version
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Inventory contains all 3 servers with connection details
- Playbook uses `yum` module with `state: present`
- `become: yes` enables root privileges
- Command runs without additional arguments
- Samba installed on all 3 servers
- All servers show `ok=2 changed=1` in PLAY RECAP
- `rpm -q samba` returns package version on all hosts

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Inventory configured with 3 app servers
- ✅ Playbook created with `yum` module
- ✅ Samba installed on all servers
- ✅ Installation verified with `rpm -q`
- ✅ Idempotent and reusable

**Final Status**: Task 87 completed successfully!  
**Outcome**: Samba package installed on all 3 App Servers — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using `yum` module for package management
- ✅ `state: present` for idempotent installations
- ✅ `become: yes` for privilege escalation
- ✅ Multi-host deployment with `hosts: all`
- ✅ Package verification with `rpm -q`

---

## 🚀 Next Steps

- Configure Samba shares
- Start and enable Samba service
- Add firewall rules
- Test Samba connectivity
- Create automated service management playbooks

