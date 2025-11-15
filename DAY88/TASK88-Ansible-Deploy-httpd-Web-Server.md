# 🌟 Task 88 - Deploy httpd Web Server + Sample Page Using Ansible

**📌 Task Description**  
The **Nautilus DevOps team** wants to:
- Install and start **httpd** on all **3 App Servers**
- Deploy a sample **index.html** using **blockinfile**
- Set correct ownership (**apache:apache**) and permissions (**0744**)

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory | `/home/thor/ansible/inventory` |
| Playbook | `/home/thor/ansible/playbook.yml` |
| Package | `httpd` |
| Service | `httpd` → started + enabled |
| File | `/var/www/html/index.html` |
| Owner/Group | `apache` |
| Permissions | `0744` (`-rwxr--r--`) |
| Content | 3 lines via `blockinfile` |
| No custom marker | Use default `# BEGIN/END ANSIBLE MANAGED BLOCK` |

---

## 🏗️ Infrastructure Overview

| **Host** | **IP** | **User** | **Password** |
|----------|--------|----------|--------------|
| stapp01 | 172.16.238.10 | tony | Ir0nM@n |
| stapp02 | 172.16.238.11 | steve | Am3ric@ |
| stapp03 | 172.16.238.12 | banner | BigGr33n |

---

## 📝 Solution Overview

### **Key Modules**

| **Module** | **Purpose** |
|------------|-------------|
| `yum` | Install httpd |
| `service` | Start & enable |
| `blockinfile` | Insert content with default markers |

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

### **Step 3: Verify Inventory**
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
- name: Setup httpd web server with sample page
  hosts: all
  become: yes
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Ensure httpd service is running and enabled
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Add content to index.html using blockinfile
      blockinfile:
        path: /var/www/html/index.html
        create: yes
        owner: apache
        group: apache
        mode: "0744"
        block: |
          Welcome to XfusionCorp!

          This is Nautilus sample file, created using Ansible!
          Please do not modify this file manually!
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

**Critical Notes**:
- ✅ `create: yes` → Creates file if missing
- ✅ `mode: "0744"` → String format
- ✅ No `marker:` → Uses default `# BEGIN/END ANSIBLE MANAGED BLOCK`

---

### **Step 5: Run Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
TASK [Install httpd package] ****************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Ensure httpd service is running and enabled] ******************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Add content to index.html using blockinfile] ******************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP *********************************************************************
stapp01                    : ok=3    changed=3    unreachable=0    failed=0
stapp02                    : ok=3    changed=3    unreachable=0    failed=0
stapp03                    : ok=3    changed=3    unreachable=0    failed=0
```

---

### **Step 6: Verify on Any App Server**
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /var/www/html/index.html"
```

**Expected**:  
```
-rwxr--r-- 1 apache apache 179 Nov 15 18:38 /var/www/html/index.html
```
```bash
ansible stapp01 -i inventory -m shell -a "cat /var/www/html/index.html"
```

**Expected**:  
```
# BEGIN ANSIBLE MANAGED BLOCK
Welcome to XfusionCorp!

This is Nautilus sample file, created using Ansible!
Please do not modify this file manually!
# END ANSIBLE MANAGED BLOCK
```

---

## 📊 Code Analysis

### **Playbook** (`/home/thor/ansible/playbook.yml`)
```yaml
- name: Setup httpd web server with sample page
  hosts: all
  become: yes
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Ensure httpd service is running and enabled
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Add content to index.html using blockinfile
      blockinfile:
        path: /var/www/html/index.html
        create: yes
        owner: apache
        group: apache
        mode: "0744"
        block: |
          Welcome to XfusionCorp!

          This is Nautilus sample file, created using Ansible!
          Please do not modify this file manually!
```

| **Parameter** | **Value** | **Purpose** |
|---------------|-----------|-------------|
| `create: yes` | Create if missing | Safe |
| `owner: apache` | Web server user | Correct |
| `mode: "0744"` | `-rwxr--r--` | As required |
| `block:` | Multi-line content | Exact text |

---

## 🔍 Verification Steps
```bash
# Dry run
ansible-playbook -i inventory playbook.yml --check

# Run
ansible-playbook -i inventory playbook.yml

# Verify file
ansible all -i inventory -m shell -a "ls -l /var/www/html/index.html"

# Verify content
ansible stapp01 -i inventory -m shell -a "cat /var/www/html/index.html"
```

---

## 📖 Quick Command Reference
```bash
cd /home/thor/ansible

# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify
ansible all -i inventory -m shell -a "ls -l /var/www/html/index.html"
ansible stapp01 -i inventory -m shell -a "cat /var/www/html/index.html"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| httpd not found | Use `httpd` (RHEL/CentOS) |
| File not created | Add `create: yes` |
| Wrong owner | `owner: apache` |
| Wrong perms | `mode: "0744"` |
| Custom marker | Do not use `marker:` |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Use `blockinfile` with default markers + exact ownership & permissions

**💡 Solution**:  
```yaml
blockinfile:
  create: yes
  owner: apache
  group: apache
  mode: "0744"
  block: |
    Welcome to XfusionCorp!
    ...
```

**🎯 Key Success Factors**:  
- ✅ `create: yes`  
- ✅ `mode: "0744"`  
- ✅ No `marker:`  
- ✅ `owner: apache`  
- ✅ `service` module with `enabled: yes`

---

## ⚠️ Important Production Notes

### **Security**:  
- Use firewall (`firewalld`)  
- SELinux: `setsebool -P httpd_can_network_connect 1`  
- HTTPS: Add SSL later

### **Best Practice**:  
- Use `template` module for full control  
- Add `validate` for HTML  
- Use handlers for service restart

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `/home/thor/ansible/`
- [ ] Verified inventory contains all 3 app servers
- [ ] Created `playbook.yml` with 3 tasks
- [ ] Task 1: Install httpd using `yum` module
- [ ] Task 2: Start and enable httpd using `service` module
- [ ] Task 3: Create index.html using `blockinfile` module
- [ ] Set `create: yes` in blockinfile
- [ ] Set `owner: apache` and `group: apache`
- [ ] Set `mode: "0744"` (as string)
- [ ] Added 3-line content block
- [ ] Did NOT specify custom `marker:`
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] All 3 tasks completed on all servers
- [ ] Verified file exists with correct permissions
- [ ] Verified file has correct ownership (apache:apache)
- [ ] Verified content includes default BEGIN/END markers
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- httpd package installed on all 3 servers
- httpd service started and enabled
- File `/var/www/html/index.html` created
- File has permissions `0744` (`-rwxr--r--`)
- File owned by `apache:apache`
- File contains 3 lines with default Ansible markers
- All servers show `ok=3 changed=3` in PLAY RECAP
- Command runs without additional arguments

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ httpd installed on all 3 App Servers
- ✅ httpd service started and enabled
- ✅ Sample index.html deployed with blockinfile
- ✅ Correct ownership (apache:apache)
- ✅ Correct permissions (0744)
- ✅ Default Ansible markers used

**Final Status**: Task 88 completed successfully!  
**Outcome**: Web server deployed with sample page on all 3 App Servers — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Using `yum` module for package installation
- ✅ Using `service` module to manage services
- ✅ Using `blockinfile` for content insertion
- ✅ Setting file ownership and permissions
- ✅ Default Ansible markers in blockinfile
- ✅ Multi-task playbook execution

---

## 🚀 Next Steps

- Add firewall rules for HTTP
- Configure virtual hosts
- Add SSL/TLS certificates
- Use handlers for service restarts
- Create templates for dynamic content

