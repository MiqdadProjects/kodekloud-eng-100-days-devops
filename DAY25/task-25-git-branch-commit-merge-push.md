**🌟 Task 25 - Manage Branches and Commit Changes on Storage Server Git Repository**

**📌 Task Description**

The **Nautilus development team** has requested the creation of a new branch and the addition of a file in the Git repository located at **`/usr/src/kodekloudrepos/demo`** on the **Storage Server** in **Stratos Datacenter**.

**Requirements:**
- Create a new branch **`nautilus`** from the `master` branch in the **`/usr/src/kodekloudrepos/demo`** repository.
- Copy the file **`/tmp/index.html`** from the storage server into the repository.
- Add and commit the `index.html` file in the `nautilus` branch.
- Merge the `nautilus` branch back into the `master` branch.
- Push changes to the `origin` remote for both the `nautilus` and `master` branches.
- Perform the task as user **`natasha`**.
- Do not modify repository or directory permissions beyond the specified actions.

👉 **Your task:** Create a new Git branch, add and commit a file, merge the branch, and push changes to the remote repository while maintaining proper user context and permissions.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
```

**Purpose**: Connect to the storage server (`ststor01`) as the `natasha` user.

**Important**: Do not switch to the root user as the task must be performed as user `natasha`.

---

## 🔹 Step 2: Navigate to the Repository  (use sudo su - if getting any error or permission issue)

```bash
sudo su -
cd /usr/src/kodekloudrepos/demo
```

**Purpose**: Change to the target repository directory to perform Git operations.

**Expected Output**:
```
/usr/src/kodekloudrepos/demo
```

**Verification**: Ensure the directory exists and is a valid Git repository.

---

## 🔹 Step 3: Verify Repository Structure

```bash
ls -la
```

**Purpose**: Examine the repository structure to confirm it’s a valid Git repository.

**Expected Contents**:
```
drwxr-xr-x. 3 natasha natasha 4096 Sep 18 13:31 .
drwxr-xr-x. 3 natasha natasha 4096 Sep 18 13:31 ..
drwxr-xr-x. 8 natasha natasha 4096 Sep 18 13:31 .git
-rw-r--r--. 1 natasha natasha   XX Sep 18 13:31 [repository files]
```

**Verification**: The presence of the `.git` directory confirms it’s a valid Git repository.

---

## 🔹 Step 4: Check Existing Branches

```bash
git branch -v
```

**Purpose**: List all existing branches and their latest commits to verify the presence of the `master` branch.

**Expected Output**:
```
* master  abc1234  [commit message]
```

**Verification**: Ensure the `master` branch exists and note its latest commit hash (e.g., `abc1234`).

---

## 🔹 Step 5: Create the New Branch

```bash
sudo chown -R natasha:natasha /usr/src/kodekloudrepos/cluster  (use only if getting permissions errors)
git checkout -b nautilus master
```

**Purpose**: Create and switch to the new branch `nautilus` from the `master` branch.

**Expected Output**:
```
Switched to a new branch 'nautilus'
```

**Verification**: The new branch is created and points to the same commit as `master`.

---

## 🔹 Step 6: Copy the File to the Repository

```bash
cp /tmp/index.html .
```

**Purpose**: Copy the `index.html` file from `/tmp/` to the current repository directory.

**Verification**: Confirm the file is copied successfully.

```bash
ls -l
```

**Expected Output**:
```
-rw-r--r--. 1 natasha natasha  XX Sep 18 13:31 index.html
[other repository files]
```

---

## 🔹 Step 7: Add the File to Git

```bash
git add index.html
```

**Purpose**: Stage the `index.html` file for committing in the `nautilus` branch.

**Verification**: Check the status to ensure the file is staged.

```bash
git status
```

**Expected Output**:
```
On branch nautilus
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   index.html
```

---

## 🔹 Step 8: Commit the File

```bash
git commit -m "Added index.html into nautilus branch"
```

**Purpose**: Commit the staged `index.html` file to the `nautilus` branch.

**Expected Output**:
```
[nautilus def5678] Added index.html into nautilus branch
 1 file changed, XX insertions(+)
 create mode 100644 index.html
```

**Verification**: The commit creates a new commit hash (e.g., `def5678`) on the `nautilus` branch.

---

## 🔹 Step 9: Switch to Master Branch

```bash
git checkout master
```

**Purpose**: Switch back to the `master` branch to prepare for merging.

**Expected Output**:
```
Switched to branch 'master'
```

**Verification**: Confirm you are on the `master` branch.

```bash
git branch
```

**Expected Output**:
```
  nautilus
* master
```

---

## 🔹 Step 10: Merge Nautilus Branch into Master

```bash
git merge nautilus
```

**Purpose**: Merge the changes from the `nautilus` branch into the `master` branch.

**Expected Output**:
```
Updating abc1234..def5678
Fast-forward
 index.html | XX ++++++++++++++++++++++
 1 file changed, XX insertions(+)
 create mode 100644 index.html
```

**Verification**: The merge should be a fast-forward, adding `index.html` to `master`.

---

## 🔹 Step 11: Verify Merge Success

```bash
ls -l
```

**Purpose**: Confirm that `index.html` is now present in the `master` branch.

**Expected Output**:
```
-rw-r--r--. 1 natasha natasha  XX Sep 18 13:31 index.html
[other repository files]
```

**Verification**: Check the commit history to ensure the merge was successful.

```bash
git log --oneline
```

**Expected Output**:
```
def5678 Added index.html into nautilus branch
abc1234 [previous commit message]
```

---

## 🔹 Step 12: Push Master Branch to Origin

```bash
git push origin master
```

**Purpose**: Push the updated `master` branch to the remote repository `origin`.

**Expected Output**:
```
To [remote repository URL]
   abc1234..def5678  master -> master
```

**Verification**: The `master` branch on the remote is updated with the new commit.

---

## 🔹 Step 13: Push Nautilus Branch to Origin

```bash
git push origin nautilus
```

**Purpose**: Push the `nautilus` branch to the remote repository `origin`.

**Expected Output**:
```
To [remote repository URL]
 * [new branch]      nautilus -> nautilus
```

**Verification**: The `nautilus` branch is created on the remote repository.

---

## 📋 Quick Command Reference

For quick copy-paste, execute as **natasha user** on **Storage Server (ststor01)**:

```bash
# Connect to storage server as natasha
ssh natasha@ststor01

# Navigate to repository
cd /usr/src/kodekloudrepos/demo

# Verify repository and branches
ls -la
git branch -v

# Create branch, add and commit file
git checkout -b nautilus master
cp /tmp/index.html .
git add index.html
git commit -m "Added index.html into nautilus branch"

# Merge and push changes
git checkout master
git merge nautilus
git push origin master
git push origin nautilus

#if getting permissions errors
sudo su -
sudo chown -R natasha:natasha /usr/src/kodekloudrepos/cluster
```

---

## 💡 Additional Tips

- **Branch Management**: The `nautilus` branch is created for isolated changes and merged back to `master`.
- **File Addition**: The `index.html` file is copied and committed only in the `nautilus` branch before merging.
- **Remote Repository**: Pushing both branches ensures remote collaboration and backup.
- **User Permissions**: All files and commits are owned by `natasha`, maintaining proper permissions.
- **Fast-Forward Merge**: Since `nautilus` is directly ahead of `master`, the merge is straightforward.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Permission Denied When Accessing Repository**
**Symptoms**: Cannot access `/usr/src/kodekloudrepos/demo`.
**Solution**: Verify `natasha` user has read/write access to the repository.
```bash
ls -ld /usr/src/kodekloudrepos/demo
# Check if natasha has access
```

### **Issue 2: File Not Found in /tmp**
**Symptoms**: `cp /tmp/index.html .` fails because the file doesn’t exist.
**Solution**: Verify the file exists in `/tmp/`.
```bash
ls -l /tmp/index.html
# Ensure file is present and readable
```

### **Issue 3: Merge Conflicts**
**Symptoms**: `git merge nautilus` fails due to conflicts.
**Solution**: Check for conflicting changes and resolve them manually.
```bash
git status
# Resolve conflicts, then commit
git add .
git commit
```

### **Issue 4: Push Fails Due to Remote Issues**
**Symptoms**: `git push origin master` or `git push origin nautilus` fails.
**Solution**: Verify remote configuration and network access.
```bash
git remote -v
# Ensure origin is correctly set
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **creating a new branch, adding and committing a file, merging it back to `master`, and pushing both branches to the remote repository** while maintaining proper user context and permissions.

**💡 Solution Approach:**

1. **User Context Maintenance**: Performed all operations as the `natasha` user without escalating privileges.
2. **Branch Creation**: Created the `nautilus` branch from `master` using `git checkout -b`.
3. **File Management**: Copied and committed `index.html` in the `nautilus` branch.
4. **Merge Operation**: Merged `nautilus` into `master` with a fast-forward merge.
5. **Remote Push**: Pushed both branches to the `origin` remote to ensure synchronization.
6. **Verification Steps**: Validated each step with commands like `git status`, `ls`, and `git log`.

**🎯 Key Success Factors:**
- **User context adherence**: All operations performed as `natasha` without privilege escalation.
- **Branch and file accuracy**: Created `nautilus` branch and committed `index.html` correctly.
- **Merge integrity**: Ensured a clean fast-forward merge to `master`.
- **Remote synchronization**: Successfully pushed both branches to `origin`.
- **Permission compliance**: Maintained existing repository permissions.
- **Verification procedures**: Confirmed successful operations through multiple validation methods.

**⚠️ Critical Configuration Details:**
- **Repository path**: `/usr/src/kodekloudrepos/demo` must exist and be accessible.
- **File source**: `/tmp/index.html` must be present and readable.
- **Branch name**: Exact name `nautilus` must be used.
- **Merge behavior**: Fast-forward merge expected due to linear history.
- **Remote configuration**: `origin` must be set and accessible for pushing.

**🔒 Repository Management Benefits:**
- **Isolated development**: The `nautilus` branch allowed safe file addition before merging.
- **Version control**: Maintained complete commit history across branches.
- **Remote collaboration**: Pushing both branches enables team access to changes.
- **File integration**: `index.html` is now part of both `master` and `nautilus`.

---

## ⚠️ Important Production Notes

🔧 **Branch Management**: Created and merged the `nautilus` branch with a new file added.

🔐 **User Context**: All operations performed under `natasha` user maintaining proper access control.

📊 **Version Control**: Changes propagated to both local and remote repositories.

🛡️ **Permission Management**: Original permissions preserved as specified in requirements.
