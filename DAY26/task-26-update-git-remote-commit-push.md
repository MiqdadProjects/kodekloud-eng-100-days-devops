**🌟 Task 26 - Update Git Remote and Commit Changes on Storage Server Repo**

**📌 Task Description**

The **xFusionCorp development team** updated the Git server configuration for the `/opt/demo.git` repository on the **Storage server** in **Stratos Datacenter**. The **DevOps team** needs to:

a. Add a new remote named **`dev_demo`** in the `/usr/src/kodekloudrepos/demo` repository pointing to **`/opt/xfusioncorp_demo.git`**
b. Copy **`/tmp/index.html`** into the repo, add and commit it on the **`master`** branch
c. Push the **`master`** branch to the new remote **`dev_demo`**

👉 **Your task:** Configure Git remote, add file changes, and push to the new remote repository.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
sudo su -
```

**Purpose**: Connect to the storage server (ststor01) as natasha user and switch to root for administrative access.

---

## 🔹 Step 2: Navigate to repository directory

```bash
cd /usr/src/kodekloudrepos/demo
```

**Purpose**: Navigate to the existing Git repository where remote configuration and commits will be made.

**Verify location**:
```bash
pwd
ls -la
```

---

## 🔹 Step 3: Check current Git remote configuration

```bash
git remote
git remote -v
```

**Purpose**: View current remote repositories configured for this Git repository.

**Expected Output** (example):
```
origin  /opt/demo.git (fetch)
origin  /opt/demo.git (push)
```

**Remote Types**:
- **List**: `git remote` shows remote names
- **Verbose**: `git remote -v` shows URLs for fetch and push

---

## 🔹 Step 4: Verify target remote repository exists

```bash
ls -ld /opt/xfusioncorp_demo.git
```

**Purpose**: Confirm that the target remote repository exists before adding it as a remote.

**Expected Output**:
```
drwxr-xr-x. 7 root root 119 Mar 15 10:30 /opt/xfusioncorp_demo.git
```

---

## 🔹 Step 5: Add new Git remote

```bash
git remote add dev_demo /opt/xfusioncorp_demo.git
```

**Purpose**: Add a new remote named `dev_demo` pointing to the specified repository location.

**Command Breakdown**:
- `git remote add` - Add new remote
- `dev_demo` - Remote name
- `/opt/xfusioncorp_demo.git` - Repository path

---

## 🔹 Step 6: Verify new remote was added

```bash
git remote -v
```

**Purpose**: Confirm the new remote `dev_demo` was added successfully.

**Expected Output**:
```
dev_demo    /opt/xfusioncorp_demo.git (fetch)
dev_demo    /opt/xfusioncorp_demo.git (push)
origin      /opt/demo.git (fetch)
origin      /opt/demo.git (push)
```

**Status**: ✅ **New remote `dev_demo` configured successfully**

---

## 🔹 Step 7: Check if source file exists

```bash
ls -la /tmp/index.html
```

**Purpose**: Verify that the source file exists before copying it to the repository.

**Expected Output**:
```
-rw-r--r--. 1 root root 1234 Mar 15 10:30 /tmp/index.html
```

---

## 🔹 Step 8: Copy index.html to repository

```bash
cp /tmp/index.html .
```

**Purpose**: Copy the index.html file from /tmp to the current repository directory.

**Verify copy operation**:
```bash
ls -l index.html
```

**Expected Result**: File should now exist in the repository directory.

---

## 🔹 Step 9: Check Git repository status

```bash
git status
```

**Purpose**: View the current status of the repository and see untracked files.

**Expected Output**:
```
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
    index.html

nothing added to commit but untracked files present (use "git add" to track)
```

---

## 🔹 Step 10: Add file to Git staging area

```bash
git add index.html
```

**Purpose**: Stage the index.html file for commit.

**Verify staging**:
```bash
git status
```

**Expected Output**:
```
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    new file:   index.html
```

---

## 🔹 Step 11: Commit the changes

```bash
git commit -m "Added index.html for dev_demo remote"
```

**Purpose**: Commit the staged changes with a descriptive commit message.

**Expected Output**:
```
[master abc1234] Added index.html for dev_demo remote
 1 file changed, X insertions(+)
 create mode 100644 index.html
```

**Commit Details**:
- **Branch**: master
- **Message**: Descriptive commit message
- **Files**: 1 new file added

---

## 🔹 Step 12: Verify commit was created

```bash
git log --oneline -n 3
```

**Purpose**: View recent commits to confirm the new commit was created successfully.

**Expected Output**:
```
abc1234 Added index.html for dev_demo remote
def5678 Previous commit message
...
```

---

## 🔹 Step 13: Push to new remote repository

```bash
git push -u dev_demo master
```

**Purpose**: Push the master branch to the new dev_demo remote and set up tracking.

**Command Options**:
- `-u` or `--set-upstream`: Set up tracking between local and remote branch
- `dev_demo`: Remote name
- `master`: Branch name

**Expected Output**:
```
Enumerating objects: X, done.
Counting objects: 100% (X/X), done.
...
To /opt/xfusioncorp_demo.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'dev_demo'.
```

---

## 🔹 Step 14: Verify push was successful

```bash
git status
```

**Purpose**: Check repository status after push to confirm everything is up to date.

**Expected Output**:
```
On branch master
Your branch is up to date with 'dev_demo/master'.

nothing to commit, working tree clean
```

---

## 🔹 Step 15: List all remotes for final verification

```bash
git remote -v
```

**Purpose**: Final verification that all remotes are configured correctly.

**Expected Output**:
```
dev_demo    /opt/xfusioncorp_demo.git (fetch)
dev_demo    /opt/xfusioncorp_demo.git (push)
origin      /opt/demo.git (fetch)
origin      /opt/demo.git (push)
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/demo

# Check current remotes
git remote -v

# Add new remote
git remote add dev_demo /opt/xfusioncorp_demo.git
git remote -v

# Copy file and commit
cp /tmp/index.html .
git status
git add index.html
git commit -m "Added index.html for dev_demo remote"

# Push to new remote
git push -u dev_demo master

# Verify final status
git status
git remote -v
```

---

## 💡 Additional Tips

- **Remote naming**: Use descriptive names for remotes to identify their purpose
- **Upstream tracking**: The `-u` flag sets up tracking for easier future pushes
- **Commit messages**: Use clear, descriptive commit messages for better project history
- **Status checks**: Regularly use `git status` to understand repository state
- **Remote management**: Use `git remote remove <name>` to remove unwanted remotes

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Remote already exists**
**Symptoms**: Error when adding remote "remote dev_demo already exists"
**Solution**: Check existing remotes and remove if needed
```bash
git remote -v
git remote remove dev_demo
git remote add dev_demo /opt/xfusioncorp_demo.git
```

### **Issue 2: File not found when copying**
**Symptoms**: `cp: cannot stat '/tmp/index.html': No such file or directory`
**Solution**: Verify source file exists and check permissions
```bash
ls -la /tmp/index.html
# If missing, check if file exists elsewhere or create it
```

### **Issue 3: Push fails with permission errors**
**Symptoms**: Permission denied when pushing to remote
**Solution**: Check repository permissions and user access
```bash
ls -ld /opt/xfusioncorp_demo.git
# Ensure proper permissions for Git operations
```

### **Issue 4: Nothing to commit**
**Symptoms**: Git says "nothing to commit, working tree clean"
**Solution**: Verify file was copied and staged properly
```bash
ls -la index.html
git status
git add index.html
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **managing multiple Git remotes** while performing file operations and ensuring proper commit workflow from local repository to a newly configured remote repository.

**💡 Solution Approach:**

1. **Remote Configuration**: Added new remote `dev_demo` pointing to `/opt/xfusioncorp_demo.git` while preserving existing remote configuration
2. **File Management**: Copied `index.html` from temporary location to repository working directory
3. **Git Workflow**: Followed proper Git workflow of staging, committing, and pushing changes
4. **Branch Management**: Used `master` branch for commit and push operations as specified
5. **Upstream Tracking**: Set up tracking relationship between local master and remote dev_demo/master branch
6. **Verification Process**: Confirmed successful completion through status checks and remote listing

**🎯 Key Success Factors:**
- **Remote management** properly adding new remote without affecting existing configuration
- **File system operations** successfully copying file from /tmp to repository directory  
- **Git staging** correctly adding new file to Git index for commit
- **Commit creation** using descriptive commit message for project history
- **Push configuration** using upstream tracking for future workflow efficiency
- **Verification procedures** confirming all operations completed successfully

**⚠️ Critical Configuration Details:**
- **Remote URL specification** using exact path `/opt/xfusioncorp_demo.git` as required
- **Branch targeting** specifically using `master` branch for commit and push operations
- **File location** ensuring `index.html` copied to correct repository root directory
- **Upstream tracking** setting up proper branch relationship with `-u` flag
- **Working directory** operating from correct repository location `/usr/src/kodekloudrepos/demo`

**🔒 Git Workflow Benefits:**
- **Multiple remote support** enables pushing to different repositories for different purposes
- **Tracking relationships** simplify future push/pull operations
- **Commit history** maintains clear project development timeline
- **Remote flexibility** allows integration with different Git server configurations

---

## ⚠️ Important Production Notes

🔧 **Multi-Remote Setup**: Repository now configured with multiple remotes for different deployment or collaboration workflows

🔐 **Branch Tracking**: Upstream relationship established between local master and dev_demo remote for streamlined operations

📊 **Version Control**: Proper commit history maintained with descriptive messages for project documentation

🛡️ **Repository Management**: File additions and remote configurations completed without disrupting existing Git structure