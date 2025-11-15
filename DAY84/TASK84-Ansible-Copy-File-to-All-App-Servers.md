# 🌟 Task 84 - Copy File to All App Servers Using Ansible

**📌 Task Description**  
The **Nautilus DevOps team** needs to copy `/usr/src/finance/index.html` from **jump host** to all **3 App Servers** in **Stratos DC**, placing it at `/opt/finance/index.html`. This requires configuring a **multi-host inventory** and a playbook that runs on all servers simultaneously.

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory Path | `/home/thor/ansible/inventory` |
| Playbook Path | `/home/thor/ansible/playbook.yml` |
| Source File | `/usr/src/finance/index.html` |
| Destination | `/opt/finance/index.html` |
| Target Hosts | `stapp01`, `stapp02`, `stapp03` |
| Execution | `ansible-playbook -i inventory playbook.yml` |

---

## 🏗️ Infrastructure Overview

**Jump Host**: `thor@jumphost` (Ansible control node)  
**App Servers**:

| **Host** | **IP** | **User** | **Password** |
|----------|--------|----------|--------------|
| stapp01 | 172.16.238.10 | tony | Ir0nM@n |
| stapp02 | 172.16.238.11 | steve | Am3ric@ |
| stapp03 | 172.16.238.12 | banner | BigGr33n |

---

## 📝 Solution Overview

### **Architecture Components**

| **Component** | **Purpose** |
|---------------|-------------|
| Inventory | Defines 3 App Servers with SSH credentials |
| Playbook | Uses `copy` module to distribute file |
| Execution | `hosts: all` → runs on all servers simultaneously |

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

### **Step 3: Create/Update Inventory File**
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
- name: Copy finance index.html to all App Servers
  hosts: all
  become: yes
  become_user: root
  tasks:
    - name: Copy /usr/src/finance/index.html to /opt/finance/
      copy:
        src: /usr/src/finance/index.html
        dest: /opt/finance/index.html
        owner: root
        group: root
        mode: '0644'
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 5: Run the Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
PLAY [Copy finance index.html to all App Servers] ********************************

TASK [Gathering Facts] **********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Copy /usr/src/finance/index.html to /opt/finance/] ************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP **********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```

---

### **Step 6: Verify File on All Servers**
```bash
ansible all -i inventory -m shell -a "ls -l /opt/finance/index.html"
```

**Expected Output**:  
```
stapp01 | CHANGED | rc=0 >>
-rw-r--r-- 1 root root 35 Nov 15 18:03 /opt/finance/index.html

stapp02 | CHANGED | rc=0 >>
-rw-r--r-- 1 root root 35 Nov 15 18:03 /opt/finance/index.html

stapp03 | CHANGED | rc=0 >>
-rw-r--r-- 1 root root 35 Nov 15 18:03 /opt/finance/index.html
```

---

## 📊 Code Analysis

### **Inventory File** (`/home/thor/ansible/inventory`)
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

---

### **Playbook** (`/home/thor/ansible/playbook.yml`)
```yaml
---
- name: Copy finance index.html to all App Servers
  hosts: all
  become: yes
  become_user: root
  tasks:
    - name: Copy /usr/src/finance/index.html to /opt/finance/
      copy:
        src: /usr/src/finance/index.html
        dest: /opt/finance/index.html
        owner: root
        group: root
        mode: '0644'
```

**Key Elements**:
- **`hosts: all`** → All 3 servers
- **`become: yes`** → Root privileges
- **`copy` module** → File transfer
- **`mode: '0644'`** → Standard permissions

---

## 🔍 Verification Steps
```bash
# Check inventory
cat /home/thor/ansible/inventory

# Dry run
ansible-playbook -i inventory playbook.yml --check

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "ls -l /opt/finance/index.html"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Inventory
vi inventory
# → 3 lines for stapp01, stapp02, stapp03

# Playbook
vi playbook.yml
# → hosts: all, become: yes, copy module

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "ls -l /opt/finance/index.html"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `UNREACHABLE` | Check IP, user, password |
| `Permission denied` | Add `become: yes` |
| Only one host | Ensure `hosts: all` |
| File not copied | Verify source path |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Multi-host file distribution using Ansible with exact inventory and no extra args.

**💡 Solution**:  
1. 3-host inventory with complete SSH vars  
2. `hosts: all` → Parallel execution  
3. `become: yes` → Root write access  
4. `copy` module → Atomic file transfer

**🎯 Key Success Factors**:  
- ✅ Correct 3-line inventory  
- ✅ `hosts: all`  
- ✅ `become: yes`  
- ✅ Parallel deployment  
- ✅ Idempotent copy

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + Ansible Vault  
🔐 **Scalability**: `[app_servers]` group  
📊 **Idempotency**: `copy` module is safe  
🛡️ **Validation**: Add `stat` task to verify

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Created inventory with all 3 app servers
- [ ] Set correct IP addresses for each server
- [ ] Set correct usernames and passwords
- [ ] Created `playbook.yml` with `hosts: all`
- [ ] Added `become: yes` for root access
- [ ] Used `copy` module with correct src and dest
- [ ] Set file permissions to `0644`
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified file exists on all 3 servers
- [ ] Confirmed correct ownership (root:root)
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Inventory file contains all 3 app servers
- Each server has correct connection details
- Playbook uses `hosts: all`
- `copy` module copies from `/usr/src/finance/index.html` to `/opt/finance/index.html`
- Command runs without additional arguments
- File created on all 3 servers simultaneously
- File has correct permissions (0644) and ownership (root:root)
- All servers show `ok=2 changed=1` in PLAY RECAP

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Multi-host inventory configured
- ✅ Playbook targets all servers with `hosts: all`
- ✅ File copied to `/opt/finance/index.html` on all servers
- ✅ Root ownership and 0644 permissions applied
- ✅ Parallel execution successful

**Final Status**: Task 84 completed successfully!  
**Outcome**: `/opt/finance/index.html` deployed to all 3 App Servers — ready for validation.

---

## 🚀 Next Steps

- Organize inventory into groups (`[app_servers]`)
- Replace passwords with SSH keys
- Use Ansible Vault for secrets
- Add pre/post validation tasks
- Integrate into CI/CD pipeline

