# 🌟 Task 93 - Conditional File Copy Using when on All App Servers

**📌 Task Description**  
The **Nautilus DevOps team** wants to train members using **Ansible conditionals** (`when`). The task is to copy different files from **jump host** to specific **App Servers** using **host-specific conditions**.

**Goal**:  
- Use `when: inventory_hostname == "stapp0X"`  
- Run on `hosts: all`  
- Copy file → `/opt/data/`  
- Set owner, group, mode: `0744`

---

## 📋 Requirements

| **Server** | **Source File** | **Dest Path** | **Owner/Group** | **Mode** |
|------------|-----------------|---------------|-----------------|----------|
| stapp01 | /usr/src/data/blog.txt | /opt/data/blog.txt | tony | 0744 |
| stapp02 | /usr/src/data/story.txt | /opt/data/story.txt | steve | 0744 |
| stapp03 | /usr/src/data/media.txt | /opt/data/media.txt | banner | 0744 |

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 📝 Solution Overview

### **Key Strategy**
- `hosts: all` → single play
- `when: inventory_hostname == "stapp0X"` → conditional execution
- `copy` module → file transfer + metadata
- `become: yes` → root privileges

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
- name: Copy files conditionally to specific app servers
  hosts: all
  become: yes
  tasks:

    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/data/blog.txt
        dest: /opt/data/blog.txt
        owner: tony
        group: tony
        mode: "0744"
      when: inventory_hostname == "stapp01"

    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/data/story.txt
        dest: /opt/data/story.txt
        owner: steve
        group: steve
        mode: "0744"
      when: inventory_hostname == "stapp02"

    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/data/media.txt
        dest: /opt/data/media.txt
        owner: banner
        group: banner
        mode: "0744"
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
TASK [Copy blog.txt to App Server 1] *******************************************
skipping: [stapp02]
skipping: [stapp03]
changed: [stapp01]

TASK [Copy story.txt to App Server 2] ******************************************
skipping: [stapp01]
skipping: [stapp03]
changed: [stapp02]

TASK [Copy media.txt to App Server 3] ******************************************
skipping: [stapp01]
skipping: [stapp02]
changed: [stapp03]
```

---

### **Step 5: Verify on Each Server**

#### **App Server 1**
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /opt/data/blog.txt"
```

**Expected**:  
```
-rwxr--r-- 1 tony tony ... /opt/data/blog.txt
```

#### **App Server 2**
```bash
ansible stapp02 -i inventory -m shell -a "ls -l /opt/data/story.txt"
```

**Expected**:  
```
-rwxr--r-- 1 steve steve ... /opt/data/story.txt
```

#### **App Server 3**
```bash
ansible stapp03 -i inventory -m shell -a "ls -l /opt/data/media.txt"
```

**Expected**:  
```
-rwxr--r-- 1 banner banner ... /opt/data/media.txt
```

---

## 📊 Code Analysis

### **Playbook** (`/home/thor/ansible/playbook.yml`)
```yaml
when: inventory_hostname == "stapp01"
```
→ Only runs on stapp01
```yaml
copy:
  src: /usr/src/data/blog.txt
  dest: /opt/data/blog.txt
  owner: tony
  group: tony
  mode: "0744"
```
→ Full file control

| **Parameter** | **Purpose** |
|---------------|-------------|
| `inventory_hostname` | Hostname from inventory |
| `owner / group` | Per-user ownership |
| `mode: "0744"` | `-rwxr--r--` |

---

## 🔍 Verification Steps
```bash
# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify each file
ansible stapp01 -i inventory -m shell -a "ls -l /opt/data/blog.txt"
ansible stapp02 -i inventory -m shell -a "ls -l /opt/data/story.txt"
ansible stapp03 -i inventory -m shell -a "ls -l /opt/data/media.txt"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible stapp01 -i inventory -m shell -a "ls -l /opt/data/blog.txt"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| Task runs on wrong host | Check `when` condition |
| Permission denied | Add `become: yes` |
| File not found | Verify `/usr/src/data/` on jump host |
| Wrong owner | Use correct user per server |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Conditional execution in single play using `when`

**💡 Solution**:  
```yaml
when: inventory_hostname == "stapp01"
```

**🎯 Key Success Factors**:  
- ✅ `hosts: all`  
- ✅ `when` with `inventory_hostname`  
- ✅ `copy` with owner, group, mode  
- ✅ Idempotent and safe

---

## ⚠️ Important Production Notes

### **Best Practice**:  
- Use roles or `include_tasks`  
- Validate source files  
- Add `creates:` for idempotency

### **Alternative (Advanced)**:
```yaml
- copy:
    src: "/usr/src/data/{{ item.file }}"
    dest: "/opt/data/{{ item.file }}"
    owner: "{{ item.user }}"
    group: "{{ item.user }}"
    mode: "0744"
  loop:
    - { file: blog.txt, user: tony, host: stapp01 }
  when: inventory_hostname == item.host
```

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Verified inventory contains all 3 app servers
- [ ] Created `playbook.yml` with `hosts: all`
- [ ] Added 3 copy tasks with `when` conditions
- [ ] Task 1: Copy `blog.txt` when `inventory_hostname == "stapp01"`
- [ ] Task 2: Copy `story.txt` when `inventory_hostname == "stapp02"`
- [ ] Task 3: Copy `media.txt` when `inventory_hostname == "stapp03"`
- [ ] Set correct source paths from `/usr/src/data/`
- [ ] Set correct destination paths to `/opt/data/`
- [ ] Set owner/group to `tony`, `steve`, `banner` respectively
- [ ] Set `mode: "0744"` for all files
- [ ] Added `become: yes` for root privileges
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified tasks skip on non-matching hosts
- [ ] Verified files created only on target servers
- [ ] Verified correct ownership on each file
- [ ] Verified correct permissions (0744) on each file
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Playbook uses `hosts: all`
- Each task has correct `when` condition
- Files copied only to matching servers
- Other servers show "skipping" for non-matching tasks
- Files have permissions `0744` (`-rwxr--r--`)
- Correct ownership per server (tony, steve, banner)
- Command runs without additional arguments
- All files exist at `/opt/data/` on respective servers

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Conditional file distribution implemented
- ✅ Single playbook for all servers
- ✅ Correct files copied to correct servers
- ✅ Proper ownership and permissions
- ✅ Tasks skip on non-matching hosts
- ✅ Idempotent execution

**Final Status**: Task 93 completed successfully!  
**Outcome**: Conditional file copy completed on all 3 App Servers — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using `when` conditionals in Ansible
- ✅ `inventory_hostname` variable for host matching
- ✅ Conditional task execution in single play
- ✅ `copy` module with ownership and permissions
- ✅ Skipping tasks based on conditions
- ✅ Host-specific operations without separate plays

---

## 🚀 Next Steps

- Use loops with conditionals for scalability
- Implement variable-driven file mapping
- Add validation with `stat` module
- Create roles for reusable logic
- Add error handling with `block/rescue`
