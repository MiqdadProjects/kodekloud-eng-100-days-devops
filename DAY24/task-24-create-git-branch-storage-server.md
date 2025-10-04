**🌟 Task 24 - Create a New Branch in Git Repository on Storage Server**

**📌 Task Description**

The **Nautilus development team** has requested the creation of a new branch named **`xfusioncorp_media`** from the `master` branch in the Git repository located at **`/usr/src/kodekloudrepos/media`** on the **Storage Server** in **Stratos Datacenter**.

**Requirements:**
- Create the new branch **`xfusioncorp_media`** from the `master` branch.
- Perform the task as user **`natasha`**.
- Do not make any changes to the code or repository contents.
- Ensure the new branch points to the same commit as the `master` branch.

👉 **Your task:** Create a new Git branch in the specified repository while maintaining proper user context and ensuring no modifications to the codebase.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
```

**Purpose**: Connect to the storage server (`ststor01`) as the `natasha` user.

**Important**: Do not switch to the root user as the task must be performed as user `natasha`.

---

## 🔹 Step 2: Navigate to the Repository

```bash
cd /usr/src/kodekloudrepos/media
```

**Purpose**: Change to the target repository directory to perform Git operations.

**Expected Output**:
```
/usr/src/kodekloudrepos/media
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
drwxr-xr-x. 3 natasha natasha 4096 Sep 18 13:21 .
drwxr-xr-x. 3 natasha natasha 4096 Sep 18 13:21 ..
drwxr-xr-x. 8 natasha natasha 4096 Sep 18 13:21 .git
-rw-r--r--. 1 natasha natasha   XX Sep 18 13:21 [repository files]
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

## 🔹 Step 5: Ensure You Are on the Master Branch

```bash
git checkout master
```

**Purpose**: Switch to the `master` branch to ensure the new branch is created from the correct starting point.

**Expected Output**:
```
Switched to branch 'master'
```

**Verification**: Confirms you are on the `master` branch.

---

## 🔹 Step 6: Create the New Branch

```bash
git checkout -b xfusioncorp_media
```

**Purpose**: Create and switch to the new branch `xfusioncorp_media` from the `master` branch.

**Expected Output**:
```
Switched to a new branch 'xfusioncorp_media'
```

**Clone Process**: The new branch points to the same commit as `master` with no changes to the codebase.

---

## 🔹 Step 7: Verify Branch Creation

```bash
git branch -v
```

**Purpose**: Confirm the new branch `xfusioncorp_media` was created and points to the same commit as `master`.

**Expected Output**:
```
  master            abc1234  [commit message]
* xfusioncorp_media abc1234  [commit message]
```

**Verification**: Both branches point to the same commit hash, with `xfusioncorp_media` as the active branch.

---

## 🔹 Step 8: Check Branch List

```bash
git branch --list
```

**Purpose**: List all branches to further confirm the new branch exists.

**Expected Contents**:
```
  master
* xfusioncorp_media
```

**Key Elements**:
- The `xfusioncorp_media` branch is listed.
- The asterisk (`*`) indicates the active branch.

---

## 🔹 Step 9: Navigate to Repository Directory

```bash
cd /usr/src/kodekloudrepos/media
```

**Purpose**: Ensure you are in the repository directory for verification commands.

---

## 🔹 Step 10: Verify Git Repository Status

```bash
git status
```

**Purpose**: Confirm the directory is a valid Git working directory and no changes were made.

**Expected Output**:
```
On branch xfusioncorp_media
nothing to commit, working tree clean
```

---

## 🔹 Step 11: Check Commit History

```bash
git log --oneline
```

**Purpose**: Verify that the repository history matches the `master` branch.

**Expected Output**:
```
abc1234 [commit message]
[previous commits]
```

**Verification**: The commit history on `xfusioncorp_media` is identical to `master`.

---

## 🔹 Step 12: Verify Current Working Directory

```bash
pwd
```

**Purpose**: Confirm current location for final verification.

**Expected Output**:
```
/usr/src/kodekloudrepos/media
```

---

## 📋 Quick Command Reference

For quick copy-paste, execute as **natasha user** on **Storage Server (ststor01)**:

```bash
# Connect to storage server as natasha
ssh natasha@ststor01  

#if getting any permissions errors 
sudo su -

# Navigate to repository
cd /usr/src/kodekloudrepos/media

# Verify repository and branches
ls -la
git branch -v

# Create new branch
git checkout master
git checkout -b xfusioncorp_media

# Verify branch creation
git branch -v
git branch --list
git status

#TROUBLESHOOTING

# if getting ownership issues 
sudo chown -R natasha:natasha /usr/src/kodekloudrepos/media

# to adjust repo permissions

sudo chmod -R g+w /usr/src/kodekloudrepos/media
sudo chgrp -R natasha /usr/src/kodekloudrepos/media


```

---

## 💡 Additional Tips

- **Branch Creation**: The `xfusioncorp_media` branch is a new reference pointing to the same commit as `master`.
- **No Code Changes**: No files are modified during branch creation, as per requirements.
- **User Permissions**: Operations are performed as `natasha`, ensuring proper ownership.
- **Git Workflow**: The new branch can be used for future development without affecting `master`.
- **Remote Tracking**: The branch can be pushed to a remote repository if needed using `git push origin xfusioncorp_media`.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Permission Denied When Accessing Repository**
**Symptoms**: Cannot access `/usr/src/kodekloudrepos/media`.
**Solution**: Verify `natasha` user has read/write access to the repository.
```bash
ls -ld /usr/src/kodekloudrepos/media
# Check if natasha has access
```

### **Issue 2: Master Branch Not Found**
**Symptoms**: `git checkout master` fails with "branch not found".
**Solution**: Check available branches and repository state.
```bash
git branch -a
# Verify if master exists or another default branch is used
```

### **Issue 3: Branch Already Exists**
**Symptoms**: `git checkout -b xfusioncorp_media` fails because the branch exists.
**Solution**: Verify existing branches and delete if permitted.
```bash
git branch -v
git branch -d xfusioncorp_media
# Then retry creating the branch
```

### **Issue 4: Not a Git Repository**
**Symptoms**: Git commands fail with "not a git repository".
**Solution**: Verify the repository structure.
```bash
ls -la /usr/src/kodekloudrepos/media/.git
# Ensure .git directory exists
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **creating a new Git branch without modifying the codebase** while maintaining proper user context and ensuring the branch points to the same commit as `master`.

**💡 Solution Approach:**

1. **User Context Maintenance**: Performed all operations as the `natasha` user without escalating privileges.
2. **Repository Verification**: Confirmed the repository at `/usr/src/kodekloudrepos/media` is valid and accessible.
3. **Branch Creation**: Used `git checkout -b` to create and switch to the `xfusioncorp_media` branch.
4. **Verification Steps**: Validated branch creation, commit alignment, and repository status.
5. **No Code Changes**: Ensured no modifications to the codebase, as required.

**🎯 Key Success Factors:**
- **User context adherence**: Performed all operations as `natasha` without privilege escalation.
- **Branch accuracy**: Created `xfusioncorp_media` from `master` with identical commit history.
- **No modifications**: Maintained repository integrity with no file changes.
- **Verification procedures**: Used multiple commands to confirm successful branch creation.
- **Permission compliance**: Ensured operations respected existing permissions.

**⚠️ Critical Configuration Details:**
- **Repository path**: `/usr/src/kodekloudrepos/media` must exist and be accessible.
- **Branch name**: Exact name `xfusioncorp_media` must be used.
- **Commit alignment**: New branch points to the same commit as `master`.
- **User ownership**: Files remain owned by `natasha` user.
- **Git behavior**: Branch creation does not alter the working directory.

**🔒 Repository Management Benefits:**
- **Isolated development**: The `xfusioncorp_media` branch allows independent development.
- **History preservation**: Maintains complete commit history from `master`.
- **Remote connectivity**: Can push/pull changes to/from a remote repository if configured.
- **Independent operation**: Full Git functionality available on the new branch.

---

## ⚠️ Important Production Notes

🔧 **Branch Creation**: Creates a new branch `xfusioncorp_media` with identical commit history to `master`.

🔐 **User Context**: All operations performed under `natasha` user maintaining proper access control.

📊 **Version Control**: New branch maintains full Git functionality for future development.

🛡️ **No Code Changes**: Repository contents remain unchanged as specified in requirements.
