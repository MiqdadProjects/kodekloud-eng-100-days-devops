# 🌟 Task 90 - Create Files with ACLs on Specific App Servers

**📌 Task Description**  
The **Nautilus DevOps team** needs **3 files** created with specific **ACLs** on each **App Server**:

| **Server** | **File** | **Path** | **Owner** | **ACL** |
|------------|----------|----------|-----------|---------|
| stapp01 | blog.txt | /opt/dba/blog.txt | root:root | Group tony: r |
| stapp02 | story.txt | /opt/dba/story.txt | root:root | User steve: rw |
| stapp03 | media.txt | /opt/dba/media.txt | root:root | Group banner: rw |

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory | `/home/thor/ansible/inventory` (exists) |
| Playbook | `/home/thor/ansible/playbook.yml` |
| Module | `file` + `acl` |
| Conditional | `when: inventory_hostname == "stapp0X"` |
| Execution | No extra args |

---

## 📝 Solution Overview

### **Key Strategy**
- Use `when` condition to target specific host
- Use `file` module → create file with `root:root`
- Use `acl` module → set ACL permissions
- Run on `hosts: all` → single playbook

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

### **Step 3: Create Playbook**
```bash
vi playbook.yml
```

**Content**:  
```yaml
---
- name: Create files with specific ACLs on app servers
  hosts: all
  become: yes
  tasks:

    # ----------------------- App Server 1 -----------------------
    - name: Create blog.txt on stapp01
      file:
        path: /opt/dba/blog.txt
        state: touch
        owner: root
        group: root
        mode: '0644'
      when: inventory_hostname == "stapp01"

    - name: Set ACL for group tony on stapp01
      acl:
        path: /opt/dba/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present
      when: inventory_hostname == "stapp01"

    # ----------------------- App Server 2 -----------------------
    - name: Create story.txt on stapp02
      file:
        path: /opt/dba/story.txt
        state: touch
        owner: root
        group: root
        mode: '0664'
      when: inventory_hostname == "stapp02"

    - name: Set ACL for user steve on stapp02
      acl:
        path: /opt/dba/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present
      when: inventory_hostname == "stapp02"

    # ----------------------- App Server 3 -----------------------
    - name: Create media.txt on stapp03
      file:
        path: /opt/dba/media.txt
        state: touch
        owner: root
        group: root
        mode: '0664'
      when: inventory_hostname == "stapp03"

    - name: Set ACL for group banner on stapp03
      acl:
        path: /opt/dba/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
      when: inventory_hostname == "stapp03"
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 4: Run Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
TASK [Create blog.txt on stapp01] **********************************************
changed: [stapp01]

TASK [Set ACL for group tony on stapp01] ***************************************
changed: [stapp01]

TASK [Create story.txt on stapp02] *********************************************
changed: [stapp02]

TASK [Set ACL for user steve on stapp02] ***************************************
changed: [stapp02]

TASK [Create media.txt on stapp03] *********************************************
changed: [stapp03]

TASK [Set ACL for group banner on stapp03] *************************************
changed: [stapp03]
```

---

### **Step 5: Verify ACLs**

#### **App Server 1**
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /opt/dba/blog.txt && getfacl /opt/dba/blog.txt"
```

**Expected**:  
```
-rw-r--r--+ 1 root root 0 ... /opt/dba/blog.txt
# file: opt/dba/blog.txt
# owner: root
# group: root
user::rw-
group::r--
group:tony:r--
mask::r--
other::r--
```

#### **App Server 2**
```bash
ansible stapp02 -i inventory -m shell -a "ls -l /opt/dba/story.txt && getfacl /opt/dba/story.txt"
```

**Expected**:  
```
-rw-rw-r--+ 1 root root 0 ... /opt/dba/story.txt
# file: opt/dba/story.txt
# owner: root
# group: root
user::rw-
user:steve:rw-
group::r--
mask::rw-
other::r--
```

#### **App Server 3**
```bash
ansible stapp03 -i inventory -m shell -a "ls -l /opt/dba/media.txt && getfacl /opt/dba/media.txt"
```

**Expected**:  
```
-rw-rw-r--+ 1 root root 0 ... /opt/dba/media.txt
# file: opt/dba/media.txt
# owner: root
# group: root
user::rw-
group::r--
group:banner:rw-
mask::rw-
other::r--
```

---

## 📊 Code Analysis

### **Playbook Breakdown**
```yaml
when: inventory_hostname == "stapp01"
```
→ Host-specific execution
```yaml
acl:
  entity: tony
  etype: group
  permissions: r
  state: present
```
→ ACL rule

| **Field** | **Value** | **Purpose** |
|-----------|-----------|-------------|
| `etype` | `group` / `user` | Type of entity |
| `permissions` | `r` / `rw` | ACL perms |
| `state: present` | Add ACL | Idempotent |

---

## 🔍 Verification Steps
```bash
# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify each server
ansible stapp01 -i inventory -m shell -a "getfacl /opt/dba/blog.txt"
ansible stapp02 -i inventory -m shell -a "getfacl /opt/dba/story.txt"
ansible stapp03 -i inventory -m shell -a "getfacl /opt/dba/media.txt"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible stapp01 -i inventory -m shell -a "getfacl /opt/dba/blog.txt"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `acl` module not found | Install `acl` package on target |
| `getfacl: not found` | Install `acl` on target |
| ACL not applied | Ensure `become: yes` |
| File missing | `file` task runs first |

**Note**: `acl` package must be installed on App Servers

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Host-specific ACLs in single playbook

**💡 Solution**:  
```yaml
when: inventory_hostname == "stapp0X"
```

**🎯 Key Success Factors**:  
- ✅ `inventory_hostname` → accurate targeting  
- ✅ `acl` module → fine-grained perms  
- ✅ `become: yes` → root access  
- ✅ Idempotent with `state: present`

---

## ⚠️ Important Production Notes

### **Security**:  
- Use default ACLs for directories  
- Audit with `getfacl`  
- Restrict `setfacl` access

### **Best Practice**:  
- Use roles for reusability  
- Add `register` + `debug`  
- Validate with `assert`

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Created `playbook.yml` with 6 tasks (2 per server)
- [ ] Task 1: Create `blog.txt` on stapp01 with `when` condition
- [ ] Task 2: Set ACL for group `tony` with read permission
- [ ] Task 3: Create `story.txt` on stapp02 with `when` condition
- [ ] Task 4: Set ACL for user `steve` with rw permission
- [ ] Task 5: Create `media.txt` on stapp03 with `when` condition
- [ ] Task 6: Set ACL for group `banner` with rw permission
- [ ] All files owned by `root:root`
- [ ] Used `inventory_hostname` for conditional execution
- [ ] Added `become: yes` for root privileges
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified file creation on each server
- [ ] Verified ACLs with `getfacl` on each server
- [ ] Confirmed `+` symbol in ls output (indicates ACL)
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Playbook contains 6 tasks with correct `when` conditions
- Files created on correct servers only
- All files owned by `root:root`
- ACLs set correctly:
  - stapp01: group `tony` has read (r)
  - stapp02: user `steve` has read-write (rw)
  - stapp03: group `banner` has read-write (rw)
- `getfacl` shows correct ACL entries
- Files have `+` in permissions (e.g., `-rw-r--r--+`)
- Command runs without additional arguments

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Host-specific files created using conditionals
- ✅ ACLs set correctly on each server
- ✅ All files owned by `root:root`
- ✅ Correct entity types (user/group)
- ✅ Correct permissions (r/rw)
- ✅ Idempotent playbook

**Final Status**: Task 90 completed successfully!  
**Outcome**: Files with ACLs created on all 3 App Servers — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using `when` conditionals for host-specific tasks
- ✅ Using `inventory_hostname` variable
- ✅ Using `acl` module for ACL management
- ✅ Setting user vs group ACLs
- ✅ Different permission levels (r vs rw)
- ✅ Combining `file` and `acl` modules

---

## 🚀 Next Steps

- Set default ACLs for directories
- Add ACL inheritance rules
- Implement ACL monitoring
- Create ACL audit reports
- Automate ACL cleanup

