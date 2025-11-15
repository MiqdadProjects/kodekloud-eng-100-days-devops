# 🌟 Task 92 - Ansible Role with Dynamic Jinja2 Template for httpd

**📌 Task Description**  
The **Nautilus DevOps team** is building an **Ansible role** for **httpd**. You must:
1. Update playbook to run **httpd role** on **App Server 2** (`stapp02`)
2. Create **Jinja2 template** `index.html.j2` with dynamic hostname
3. Add task in role to deploy template with:
   - **Path**: `/var/www/html/index.html`
   - **Owner**: sudo user (e.g., `steve` on `stapp02`)
   - **Permissions**: `0644`

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 📋 Requirements

| **Item** | **Value** |
|----------|-----------|
| Inventory | `~/ansible/inventory` (exists) |
| Playbook | `~/ansible/playbook.yml` |
| Role Path | `~/ansible/role/httpd/` |
| Template | `templates/index.html.j2` |
| Variable | `{{ inventory_hostname }}` |
| Owner | `{{ ansible_user }}` |
| Mode | `0644` |
| Target Host | `stapp02` |

---

## 🏗️ Infrastructure Overview

| **Host** | **IP** | **Sudo User** | **Password** |
|----------|--------|---------------|--------------|
| stapp02 | 172.16.238.11 | steve | Am3ric@ |

---

## 📝 Solution Overview

### **Architecture**
```
~/ansible/
├── inventory
├── playbook.yml
└── role/
    └── httpd/
        ├── tasks/
        │   └── main.yml
        └── templates/
            └── index.html.j2
```

---

## 🔹 Implementation Steps

### **Step 1: Connect to Jump Host**
```bash
ssh thor@jumphost
```

---

### **Step 2: Navigate to Ansible Directory**
```bash
cd ~/ansible
```

---

### **Step 3: Update Playbook**
```bash
vi playbook.yml
```

**Content**:  
```yaml
---
- hosts: stapp02
  become: yes
  become_user: root
  roles:
    - role/httpd
```

**Save & Exit**: `Esc` → `:wq` → `Enter`

---

### **Step 4: Create Jinja2 Template**
```bash
mkdir -p role/httpd/templates
vi role/httpd/templates/index.html.j2
```

**Content**:  
```jinja2
This file was created using Ansible on {{ inventory_hostname }}
```

**Save & Exit**

---

### **Step 5: Update Role Task**
```bash
vi role/httpd/tasks/main.yml
```

**Final Content**:  
```yaml
---
- name: Install the latest version of HTTPD
  yum:
    name: httpd
    state: latest

- name: Start service httpd
  service:
    name: httpd
    state: started
    enabled: yes

- name: Copy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: '0644'
```

**Save & Exit**

**Critical**:
- ✅ `{{ ansible_user }}` → dynamically uses `steve` on `stapp02`
- ✅ `{{ inventory_hostname }}` → `stapp02`
- ✅ `mode: '0644'` → `-rw-r--r--`

---

### **Step 6: Run Playbook**
```bash
ansible-playbook -i inventory playbook.yml
```

**Expected Output**:  
```
TASK [role/httpd : Copy index.html template] ***********************************
changed: [stapp02]

PLAY RECAP *********************************************************************
stapp02                    : ok=4    changed=3    unreachable=0    failed=0
```

---

### **Step 7: Verify on App Server 2**

#### **Check File Content**
```bash
ansible stapp02 -i inventory -m shell -a "cat /var/www/html/index.html"
```

**Expected**:  
```
This file was created using Ansible on stapp02
```

#### **Check Permissions & Ownership**
```bash
ansible stapp02 -i inventory -m shell -a "ls -l /var/www/html/index.html"
```

**Expected**:  
```
-rw-r--r-- 1 steve steve 48 Nov 15 21:24 /var/www/html/index.html
```

---

## 📊 Code Analysis

### **Template** (`role/httpd/templates/index.html.j2`)
```jinja2
This file was created using Ansible on {{ inventory_hostname }}
```
→ Dynamic per host

### **Task** (`role/httpd/tasks/main.yml`)
```yaml
- name: Copy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: '0644'
```

| **Variable** | **Value on stapp02** | **Source** |
|--------------|----------------------|------------|
| `{{ inventory_hostname }}` | `stapp02` | Ansible fact |
| `{{ ansible_user }}` | `steve` | From inventory |

---

## 🔍 Verification Steps
```bash
# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify content
ansible stapp02 -i inventory -m shell -a "cat /var/www/html/index.html"

# Verify perms
ansible stapp02 -i inventory -m shell -a "ls -l /var/www/html/index.html"
```

---

## 📖 Quick Command Reference
```bash
cd ~/ansible

# Run
ansible-playbook -i inventory playbook.yml

# Verify
ansible stapp02 -i inventory -m shell -a "cat /var/www/html/index.html"
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `inventory_hostname` wrong | Use `{{ inventory_hostname }}` |
| Owner wrong | Use `{{ ansible_user }}` |
| Template not found | Path: `templates/index.html.j2` |
| Role not applied | `roles: - role/httpd` |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Dynamic content + ownership using Jinja2 + facts

**💡 Solution**:  
```jinja2
{{ inventory_hostname }}
```
```yaml
owner: "{{ ansible_user }}"
```

**🎯 Key Success Factors**:  
- ✅ `inventory_hostname` in template  
- ✅ `ansible_user` in task  
- ✅ `mode: '0644'`  
- ✅ Role applied to only `stapp02`

---

## ⚠️ Important Production Notes

### **Best Practice**:  
- Use roles for reusability  
- Vault for secrets  
- Handlers for service restart

### **Idempotency**:  
- `yum: state=latest` → use `present` in prod  
- `template` module is idempotent

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`
- [ ] Navigated to `~/ansible/`
- [ ] Updated `playbook.yml` to target `stapp02`
- [ ] Added role `role/httpd` to playbook
- [ ] Created directory `role/httpd/templates/`
- [ ] Created `index.html.j2` template
- [ ] Added `{{ inventory_hostname }}` variable in template
- [ ] Updated `role/httpd/tasks/main.yml`
- [ ] Added template task with `template` module
- [ ] Set `src: index.html.j2` and `dest: /var/www/html/index.html`
- [ ] Set `owner: "{{ ansible_user }}"` and `group: "{{ ansible_user }}"`
- [ ] Set `mode: '0644'`
- [ ] Ran `ansible-playbook -i inventory playbook.yml`
- [ ] Verified file content shows "stapp02"
- [ ] Verified file ownership is `steve:steve`
- [ ] Verified file permissions are `0644`
- [ ] Documented all steps

**🎉 Success Criteria Met When**:
- Playbook targets only `stapp02`
- Role structure exists at `~/ansible/role/httpd/`
- Template file `index.html.j2` created in `templates/` directory
- Template contains `{{ inventory_hostname }}` variable
- Task uses `template` module
- File deployed to `/var/www/html/index.html`
- File owned by `steve:steve` (dynamic via `{{ ansible_user }}`)
- File permissions are `0644` (`-rw-r--r--`)
- Content shows "This file was created using Ansible on stapp02"
- Command runs without additional arguments

---

## 🏁 Task Completion Summary

**Completed**:
- ✅ Ansible role structure created
- ✅ Jinja2 template with dynamic variables
- ✅ Template deployed with correct ownership
- ✅ Dynamic hostname in file content
- ✅ Correct permissions (0644)
- ✅ Role applied to specific host

**Final Status**: Task 92 completed successfully!  
**Outcome**: Ansible role with Jinja2 template deployed to App Server 2 — ready for validation.

---

## 🎓 Learning Outcomes

- ✅ Creating Ansible role directory structure
- ✅ Using Jinja2 templates with variables
- ✅ `{{ inventory_hostname }}` for dynamic hostnames
- ✅ `{{ ansible_user }}` for dynamic ownership
- ✅ `template` module for file deployment
- ✅ Role-based playbook organization
- ✅ Targeting specific hosts with roles

---

## 🚀 Next Steps

- Extend role to all app servers
- Add handlers for httpd restart
- Use `defaults/main.yml` for variables
- Add role dependencies
- Implement role testing with Molecule

