# 🌟 Task 83 - Create Empty File on App Server 2

**📌 Task Description**  
An Ansible playbook is in progress on the **jump host**. A team member started the setup, and your task is to finalize it by:
- Updating the **inventory** to target **App Server 2** (`stapp02`)
- Creating a **playbook** that creates an empty file at `/tmp/file.txt` on **App Server 2**

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory Path | `/home/thor/ansible/inventory` |
| Playbook Path | `/home/thor/ansible/playbook.yml` |
| Target Host | `stapp02` |
| IP Address | `172.16.238.11` |
| Ansible User | `steve` |
| Password | `Am3ric@` |
| File to Create | `/tmp/file.txt` |
| Execution | No extra args |

---

## 🏗️ Infrastructure Overview

- **Jump Host**: `thor@jump_host` (Ansible control node)
- **Target**: App Server 2 (`stapp02`)
- **Working Dir**: `/home/thor/ansible`

---

## 📝 Solution Overview

### **Key Components**

| **Component** | **Purpose** |
|---------------|-------------|
| Inventory | Define `stapp02` with correct IP, user, password |
| Playbook | Use `file` module with `state: touch` |
| Execution | Run via `ansible-playbook -i inventory playbook.yml` |

---

## 🔹 Implementation Steps

### **Step 1: Connect to Jump Host**
```bash
ssh thor@jump_host
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

**Correct Content** (replace any existing line):  
```ini
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**Save & Exit**:  
- `Esc` → `:wq` → `Enter`

**💡 Why `ansible_ssh_common_args`?**  
Avoids SSH host key verification prompt.

---

### **Step 4: Create Playbook**
```bash
vi playbook.yml
```

**Content**:  
```yaml
---
- name: Create empty file on App Server 2
  hosts: stapp02
  become: yes
  become_user: root
  tasks:
    - name: Create /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

**💡 Why `become: yes`?**  
Ensures file is created with root privileges (required if `/tmp` has strict perms).

---

### **Step 5: Run Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
PLAY [Create empty file on App Server 2] ***************************************

TASK [Gathering Facts] *********************************************************
ok: [stapp02]

TASK [Create /tmp/file.txt] ****************************************************
changed: [stapp02]

PLAY RECAP *********************************************************************
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
```

---

### **Step 6: Verify File on App Server 2**
```bash
ssh steve@172.16.238.11
```

**Password**: `Am3ric@`
```bash
ls -l /tmp/file.txt
```

**Expected Output**:  
```
-rw-r--r-- 1 root root 0 Nov 15 17:46 /tmp/file.txt
```

---

## 📊 Code Analysis

### **Inventory File** (`/home/thor/ansible/inventory`)
```ini
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

| **Variable** | **Value** | **Purpose** |
|--------------|-----------|-------------|
| `ansible_host` | `172.16.238.11` | Correct IP |
| `ansible_user` | `steve` | Login user |
| `ansible_ssh_pass` | `Am3ric@` | Password |
| `ansible_ssh_common_args` | `-o StrictHostKeyChecking=no` | Skip host key check |

---

### **Playbook** (`/home/thor/ansible/playbook.yml`)
```yaml
- name: Create empty file on App Server 2
  hosts: stapp02
  become: yes
  become_user: root
  tasks:
    - name: Create /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```

- **`become: yes`** → Elevate to root  
- **`state: touch`** → Create file if missing, update mtime if exists  
- **Idempotent**

---

## 🔍 Verification Steps
```bash
# 1. Check inventory
cat /home/thor/ansible/inventory

# 2. Dry run
ansible-playbook -i inventory playbook.yml --check

# 3. Run
ansible-playbook -i inventory playbook.yml

# 4. Verify remotely
ssh steve@172.16.238.11 "ls -l /tmp/file.txt"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Update inventory
vi inventory
# → stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'

# Create playbook
vi playbook.yml
# → (see above)

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ssh steve@172.16.238.11 "sudo ls -l /tmp/file.txt"
```

---

## 💡 Troubleshooting Common Issues

| **Issue** | **Fix** |
|-----------|---------|
| `UNREACHABLE` | Wrong IP → use `172.16.238.11` |
| `Permission denied` | Add `become: yes` |
| `Host key verification failed` | Add `ansible_ssh_common_args` |
| File not created | Run with `-v` for debug |

---

## 🚨 Task-Specific Challenge & Solution

### **Challenges**
1. Incorrect IP in inventory (was `172.238.16.204` → wrong)
2. Missing `become` → file not created
3. SSH host key prompt → blocks automation

### **Solution**
```ini
# Fixed IP + added SSH args
stapp02 ansible_host=172.16.238.11 ... ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```
```yaml
become: yes  # Critical for root access
```

### **🎯 Key Success Factors**
- ✅ Correct IP: `172.16.238.11`
- ✅ `become: yes` → Root execution
- ✅ `ansible_ssh_common_args` → Non-interactive
- ✅ `state: touch` → Idempotent

---

## ⚠️ Production Notes

🔧 **Security**: Use SSH keys + Ansible Vault  
🔐 **Idempotency**: `touch` is safe  
📊 **Logging**: Add `log_path` in `ansible.cfg`  
🛡️ **Scalability**: Extend to `[app_servers]` group

---

## 🎓 Learning Outcomes

- ✅ INI inventory with connection variables
- ✅ `become` for privilege escalation
- ✅ `file` module with `state: touch`
- ✅ Non-interactive SSH via `ansible_ssh_common_args`

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Updated `inventory` with correct IP `172.16.238.11`
- [ ] Set `ansible_user=steve`
- [ ] Set `ansible_ssh_pass=Am3ric@`
- [ ] Added `ansible_ssh_common_args='-o StrictHostKeyChecking=no'`
- [ ] Created `playbook.yml` with `hosts: stapp02`
- [ ] Added `become: yes` for root access
- [ ] Used `file` module with `state: touch`
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified `/tmp/file.txt` exists on App Server 2
- [ ] Confirmed file owned by root
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Inventory file exists at `/home/thor/ansible/inventory`
- Playbook file exists at `/home/thor/ansible/playbook.yml`
- Inventory targets `stapp02` with correct connection details
- Playbook uses `file` module with `state: touch`
- Command `ansible-playbook -i inventory playbook.yml` runs without errors
- File `/tmp/file.txt` created on App Server 2
- No additional CLI arguments needed

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Inventory updated with correct IP & SSH args
- ✅ Playbook created with `become: yes`
- ✅ File `/tmp/file.txt` created as root
- ✅ Validation command works without extra args

**Final Status**: Task 83 completed successfully!  
**Outcome**: `/tmp/file.txt` created on App Server 2 — ready for validation.

---

## 🚀 Next Steps

- Replace password with SSH key
- Use Ansible Vault for secrets
- Add idempotency checks
- Integrate into CI/CD pipeline
