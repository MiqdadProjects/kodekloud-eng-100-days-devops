# 🌟 Task 85 - Create File with Dynamic Ownership & Permissions on All App Servers

**📌 Task Description**  
The **Nautilus DevOps team** is testing **dynamic file ownership** using Ansible variables. The goal is to create a blank file `/opt/app.txt` on all **3 App Servers** with:
- **Permissions**: `0655`  
- **Owner/Group**:  
  - `tony` on `stapp01`  
  - `steve` on `stapp02`  
  - `banner` on `stapp03`

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory Path | `~/playbook/inventory` |
| Playbook Path | `~/playbook/playbook.yml` |
| Target File | `/opt/app.txt` |
| Permissions | `0655` |
| Owner/Group | Dynamic per host |
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

### **Key Insight**
Use `ansible_user` fact (automatically set per host) to dynamically assign owner/group.
```yaml
owner: "{{ ansible_user }}"
group: "{{ ansible_user }}"
```

---

## 🔹 Implementation Steps

### **Step 1: Connect to Jump Host**
```bash
ssh thor@jumphost
```

---

### **Step 2: Navigate to Playbook Directory**
```bash
mkdir -p ~/playbook && cd ~/playbook
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
- name: Create /opt/app.txt with dynamic ownership and 0655 permissions
  hosts: all
  become: yes
  tasks:
    - name: Create file with correct owner, group, and permissions
      file:
        path: /opt/app.txt
        state: touch
        mode: "0655"
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 5: Run the Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
PLAY [Create /opt/app.txt with dynamic ownership and 0655 permissions] *********

TASK [Gathering Facts] *********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Create file with correct owner, group, and permissions] ******************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP *********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```

---

### **Step 6: Verify File on All Servers**
```bash
ansible all -i inventory -m shell -a "ls -l /opt/app.txt"
```

**Expected Output**:  
```
stapp01 | CHANGED | rc=0 >>
-rw-r-xr-x 1 tony tony 0 Nov 15 18:11 /opt/app.txt

stapp02 | CHANGED | rc=0 >>
-rw-r-xr-x 1 steve steve 0 Nov 15 18:11 /opt/app.txt

stapp03 | CHANGED | rc=0 >>
-rw-r-xr-x 1 banner banner 0 Nov 15 18:11 /opt/app.txt
```

---

## 📊 Code Analysis

### **Inventory File** (`~/playbook/inventory`)
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

**`ansible_user`** → Critical for dynamic ownership

---

### **Playbook** (`~/playbook/playbook.yml`)
```yaml
- name: Create /opt/app.txt with dynamic ownership and 0655 permissions
  hosts: all
  become: yes
  tasks:
    - name: Create file with correct owner, group, and permissions
      file:
        path: /opt/app.txt
        state: touch
        mode: "0655"
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
```

| **Parameter** | **Value** | **Purpose** |
|---------------|-----------|-------------|
| `state: touch` | Create empty file | Idempotent |
| `mode: "0655"` | `-rw-r-xr-x` | Correct permissions |
| `owner: "{{ ansible_user }}"` | tony, steve, banner | Dynamic per host |
| `group: "{{ ansible_user }}"` | Same as owner | Required |

---

## 🔍 Verification Steps
```bash
# Check inventory
cat ~/playbook/inventory

# Dry run
ansible-playbook -i inventory playbook.yml --check

# Run
ansible-playbook -i inventory playbook.yml

# Verify ownership & perms
ansible all -i inventory -m shell -a "ls -l /opt/app.txt"
```

---

## 📖 Quick Command Reference
```bash
cd ~/playbook

# Inventory
vi inventory
# → 3 lines with ansible_user

# Playbook
vi playbook.yml
# → use {{ ansible_user }} for owner/group

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "ls -l /opt/app.txt"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| Wrong owner | Ensure `ansible_user` is set in inventory |
| Permission denied | Add `become: yes` |
| `mode: 0655` fails | Use `"0655"` (string) |
| File not created | Check `/opt` exists or add mkdir task |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Dynamic ownership per host without hardcoding.

**💡 Solution**:  
```yaml
owner: "{{ ansible_user }}"
group: "{{ ansible_user }}"
```

**🎯 Key Success Factors**:  
- ✅ `ansible_user` in inventory  
- ✅ `{{ ansible_user }}` in playbook  
- ✅ `mode: "0655"`  
- ✅ `become: yes`  
- ✅ Idempotent & reusable

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Ansible Vault  
🔐 **Best Practice**: Use `host_vars` for complex logic  
📊 **Idempotency**: `file` with `touch` is safe  
🛡️ **Scalability**: Works for 100+ hosts

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Created directory `~/playbook/`
- [ ] Created inventory with all 3 app servers
- [ ] Set `ansible_user` for each host (tony, steve, banner)
- [ ] Set correct IP addresses and passwords
- [ ] Created `playbook.yml` with `hosts: all`
- [ ] Added `become: yes` for root access
- [ ] Used `file` module with `state: touch`
- [ ] Set `mode: "0655"` (as string)
- [ ] Set `owner: "{{ ansible_user }}"`
- [ ] Set `group: "{{ ansible_user }}"`
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified file exists on all 3 servers
- [ ] Confirmed permissions are `0655` (`-rw-r-xr-x`)
- [ ] Confirmed owner/group matches `ansible_user` on each host
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Inventory contains all 3 servers with `ansible_user` defined
- Playbook uses `{{ ansible_user }}` for dynamic ownership
- File `/opt/app.txt` created on all servers
- Permissions are exactly `0655`
- Owner is `tony` on stapp01, `steve` on stapp02, `banner` on stapp03
- Group matches owner on each server
- Command runs without additional arguments
- All servers show `ok=2 changed=1` in PLAY RECAP

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Dynamic ownership implemented using `{{ ansible_user }}`
- ✅ File created with correct permissions `0655`
- ✅ Owner/group set dynamically per host
- ✅ Playbook is reusable and scalable
- ✅ No hardcoded usernames

**Final Status**: Task 85 completed successfully!  
**Outcome**: `/opt/app.txt` created on all 3 App Servers with dynamic ownership — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using Ansible facts (`ansible_user`) for dynamic configuration
- ✅ Jinja2 templating in playbooks (`{{ variable }}`)
- ✅ Setting file permissions with `mode`
- ✅ Dynamic ownership without hardcoding
- ✅ Idempotent file creation with `state: touch`

---

## 🚀 Next Steps

- Extend to use `host_vars` for more complex configurations
- Replace passwords with SSH keys
- Use Ansible Vault for credential management
- Add validation tasks to verify permissions
- Create reusable roles

