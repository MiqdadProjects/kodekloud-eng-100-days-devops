# 🌟 Task 82 - Create Ansible Inventory for App Server 3

**📌 Task Description**  
The **Nautilus DevOps team** is testing Ansible playbooks located at `/home/thor/playbook/` on **jump host**. They need an **INI-style inventory file** to connect to **App Server 3** in **Stratos DC**.

**👉 Task Requirements**:  
- Create file: `/home/thor/playbook/inventory`  
- **Format**: INI  
- **Host**: App Server 3 → `stapp03`  
- **Variables**:  
  - `ansible_host`  
  - `ansible_user`  
  - `ansible_ssh_pass`  
- **Validation Command**:  
```bash
  ansible-playbook -i inventory playbook.yml
```  
- No extra CLI args allowed

---

## 🔹 Step 1: Access Jump Host & Navigate to Directory

**Action**: SSH into jump host and go to playbook directory.  
**Purpose**: Prepare to create inventory file.

**Steps**:  
```bash
ssh thor@jump_host
cd /home/thor/playbook
```

**Success Indicators**:  
- ✅ In correct directory.  
- ✅ `playbook.yml` exists.

---

## 🔹 Step 2: Create INI Inventory File

**Action**: Create `/home/thor/playbook/inventory` with correct host and vars.  
**Purpose**: Enable Ansible to connect to `stapp03`.

**Steps**:  
```bash
vi /home/thor/playbook/inventory
```

**Paste exactly**:  
```ini
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

**Save & Exit**:  
- Press `Esc`  
- Type `:wq` → Enter

---

## 🔹 Step 3: Verify File Content

**Action**: Confirm inventory is correct.  
**Purpose**: Avoid syntax errors.

**Steps**:  
```bash
cat /home/thor/playbook/inventory
```

**Expected Output**:  
```ini
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

**Success Indicators**:  
- ✅ One line.  
- ✅ No extra spaces or newlines.  
- ✅ Correct values.

---

## 🔹 Step 4: Test Connectivity

**Action**: Ping the host using inventory.  
**Purpose**: Confirm Ansible can connect.

**Steps**:  
```bash
ansible stapp03 -i inventory -m ping
```

**Expected Output**:  
```json
stapp03 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

**Success Indicators**:  
- ✅ `pong` received.

---

## 🔹 Step 5: Validate with Playbook

**Action**: Run validation command.  
**Purpose**: Match exact lab validation.

**Steps**:  
```bash
ansible-playbook -i inventory playbook.yml
```

**Success Indicators**:  
- ✅ Playbook runs without errors.  
- ✅ Tasks complete.

---

## 🔹 Step 6: Optional Verification Commands

**Action**: Confirm Apache is installed and running.  
**Purpose**: Prove full connectivity.

**Steps**:  
```bash
# Check package
ansible stapp03 -i inventory -m shell -a "rpm -q httpd"

# Check service
ansible stapp03 -i inventory -m shell -a "sudo systemctl status httpd"
```

**Success Indicators**:  
- ✅ `httpd` package found.  
- ✅ Service active (running).

---

## 📋 Final Inventory File

**Path**: `/home/thor/playbook/inventory`  
**Content**:  
```ini
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

---

## 📖 Quick Reference Guide

**Inventory Line Breakdown**:  

| **Field** | **Value** | **Purpose** |
|-----------|-----------|-------------|
| `stapp03` | Hostname | Matches wiki & Ansible host |
| `ansible_host=172.16.238.12` | IP | Connection target |
| `ansible_user=banner` | User | SSH login |
| `ansible_ssh_pass=BigGr33n` | Password | Authentication |

**Validation Command**:  
```bash
ansible-playbook -i inventory playbook.yml
```

---

## 💡 Common Issues & Fixes

| **Issue** | **Fix** |
|-----------|---------|
| `UNREACHABLE` | Check IP, user, password |
| `Permission denied` | Verify `ansible_ssh_pass` |
| Playbook fails | Ensure exact one-line inventory |
| Extra newlines | Use `vi` and `:wq` properly |

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge**:  
Create INI inventory with exact variables so `ansible-playbook -i inventory playbook.yml` works without modification.

**💡 Solution**:  
1. Used single-line INI format  
2. Included all required vars  
3. Matched wiki hostname (`stapp03`)  
4. Tested with `ping` and `playbook.yml`

**🎯 Key Success Factors**:  
- File path: `/home/thor/playbook/inventory`  
- Host: `stapp03`  
- Vars: `ansible_host`, `ansible_user`, `ansible_ssh_pass`  
- No groups or sections  
- `ansible-playbook` runs cleanly

---

## ⚠️ Important Production Notes

🔧 **Security**: Use SSH keys + `ansible-vault` for passwords  
🔐 **Best Practice**: Group hosts in `[app_servers]`  
📊 **Idempotency**: Inventory should be versioned  
🛡️ **Validation**: Always test with `ansible -m ping`  
📹 **Documentation**: Comment inventory with purpose

---

## ✅ Task Completion Checklist

- [ ] SSH'd into jump host as `thor`  
- [ ] Navigated to `/home/thor/playbook/`  
- [ ] Created `inventory` file in correct location  
- [ ] Added `stapp03` as hostname  
- [ ] Set `ansible_host=172.16.238.12`  
- [ ] Set `ansible_user=banner`  
- [ ] Set `ansible_ssh_pass=BigGr33n`  
- [ ] Verified file content with `cat`  
- [ ] Tested connectivity with `ansible -m ping`  
- [ ] Ran `ansible-playbook -i inventory playbook.yml` successfully  
- [ ] Documented all steps  

**🎉 Success Criteria Met When**:  
- Inventory file exists at `/home/thor/playbook/inventory`  
- File is in INI format (single line)  
- Contains all three required variables  
- `ansible stapp03 -i inventory -m ping` returns `pong`  
- `ansible-playbook -i inventory playbook.yml` executes without errors  
- No additional CLI arguments needed

