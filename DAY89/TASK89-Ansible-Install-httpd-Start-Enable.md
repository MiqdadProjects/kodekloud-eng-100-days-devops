# 🌟 Task 89 - Install & Start httpd on All App Servers

**📌 Task Description**  
The **Developers** need **httpd** installed, started, and enabled on all **3 Nautilus App Servers**. The **DevOps team** will use **Ansible** to automate this.

**Goal**:  
- Install `httpd`  
- Start service  
- Enable on boot  
- Run via: `ansible-playbook -i inventory playbook.yml`

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory | `/home/thor/ansible/inventory` (already exists) |
| Playbook | `/home/thor/ansible/playbook.yml` |
| Package | `httpd` |
| Service | `httpd` → started + enabled |
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
- Use `yum` module → install httpd
- Use `service` module → start + enable
- `become: yes` → root access
- `hosts: all` → all servers

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

### **Step 3: Verify Inventory Exists**
```bash
cat inventory
```

**Expected**:  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

---

### **Step 4: Create Playbook**
```bash
vi playbook.yml
```

**Content**:  
```yaml
---
- name: Install and configure httpd on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 5: Run Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
PLAY [Install and configure httpd on all app servers] **************************

TASK [Gathering Facts] *********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Install httpd package] ***************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Start and enable httpd service] ******************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP *********************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0
stapp02                    : ok=3    changed=2    unreachable=0    failed=0
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```

---

### **Step 6: Verify Installation & Service**
```bash
# Check package
ansible all -i inventory -m shell -a "rpm -q httpd"

# Check service active
ansible all -i inventory -m shell -a "systemctl is-active httpd"

# Check service enabled
ansible all -i inventory -m shell -a "systemctl is-enabled httpd"
```

**Expected Output**:  
```
stapp01 | CHANGED | rc=0 >>
httpd-2.4.37-43.el8.x86_64

stapp01 | CHANGED | rc=0 >>
active

stapp01 | CHANGED | rc=0 >>
enabled
```

---

## 📊 Code Analysis

### **Playbook** (`/home/thor/ansible/playbook.yml`)
```yaml
---
- name: Install and configure httpd on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes
```

| **Task** | **Module** | **Purpose** |
|----------|------------|-------------|
| Install | `yum` | `state: present` → idempotent |
| Service | `service` | `state: started`, `enabled: yes` |

---

## 🔍 Verification Steps
```bash
# Dry run
ansible-playbook -i inventory playbook.yml --check

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "rpm -q httpd"
ansible all -i inventory -m shell -a "systemctl is-active httpd"
ansible all -i inventory -m shell -a "systemctl is-enabled httpd"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "rpm -q httpd"
ansible all -i inventory -m shell -a "systemctl status httpd | grep Active"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `UNREACHABLE` | Check inventory credentials |
| `Permission denied` | Add `become: yes` |
| Service not starting | Check firewall or SELinux |
| Package not found | Use `httpd` (not apache) |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Install + start + enable httpd using Ansible with no extra args.

**💡 Solution**:  
```yaml
service:
  name: httpd
  state: started
  enabled: yes
```

**🎯 Key Success Factors**:  
- ✅ `become: yes`  
- ✅ `state: present`  
- ✅ `enabled: yes`  
- ✅ `hosts: all`  
- ✅ Idempotent

---

## ⚠️ Important Production Notes

### **Security**:  
- Open port: `firewall-cmd --add-service=http --permanent`  
- SELinux: Ensure context on `/var/www/html`

### **Best Practice**:  
- Use handlers for service restart  
- Add `notify` on config changes  
- Pin version: `httpd-2.4.37`

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Verified inventory file exists and contains all 3 servers
- [ ] Created `playbook.yml` with 2 tasks
- [ ] Task 1: Install httpd using `yum` module with `state: present`
- [ ] Task 2: Start and enable httpd using `service` module
- [ ] Set `state: started` in service task
- [ ] Set `enabled: yes` in service task
- [ ] Added `become: yes` for root privileges
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] All 3 servers show `ok=3 changed=2`
- [ ] Verified httpd package installed with `rpm -q httpd`
- [ ] Verified service is active with `systemctl is-active httpd`
- [ ] Verified service is enabled with `systemctl is-enabled httpd`
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Playbook contains 2 tasks (install and service)
- `yum` module used with `name: httpd` and `state: present`
- `service` module used with `state: started` and `enabled: yes`
- `become: yes` enables root privileges
- Command runs without additional arguments
- httpd installed on all 3 servers
- httpd service started on all 3 servers
- httpd service enabled for boot on all 3 servers
- All servers show `ok=3 changed=2` in PLAY RECAP

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ httpd package installed on all 3 App Servers
- ✅ httpd service started on all servers
- ✅ httpd service enabled for boot
- ✅ Idempotent playbook created
- ✅ All tasks completed successfully

**Final Status**: Task 89 completed successfully!  
**Outcome**: httpd web server deployed and configured on all 3 App Servers — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using `yum` module for package management
- ✅ Using `service` module to manage services
- ✅ `state: started` to start services
- ✅ `enabled: yes` to enable services at boot
- ✅ Idempotent service configuration
- ✅ Multi-host deployment with `hosts: all`

---

## 🚀 Next Steps

- Configure virtual hosts
- Add firewall rules for HTTP/HTTPS
- Deploy web content
- Configure SSL/TLS
- Add monitoring for httpd service

