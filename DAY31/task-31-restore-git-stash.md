# 🌟 Task 31 - Restore Stashed Changes and Push to Repository

## 📌 Task Description

The **Nautilus application development team** is working on a Git repository located at `/usr/src/kodekloudrepos/apps` on the **Storage server** in **Stratos DC**. A developer has stashed some in-progress changes in this repository, and the team needs to restore the changes associated with the `stash@{1}` identifier. After restoring the stash, the changes should be committed and pushed to the remote repository.

👉 **Your task**: Restore the `stash@{1}` changes in the `/usr/src/kodekloudrepos/apps` Git repository, commit the changes with an appropriate message, and push them to the `origin/master` branch.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01
sudo su -
```

**Purpose**: Connect to the storage server (ststor01) as the user Natasha and switch to root for administrative access.

---

## 🔹 Step 2: Navigate to Repository Directory

```bash
cd /usr/src/kodekloudrepos/apps/
```

**Purpose**: Navigate to the Git repository where the stash restoration will be performed.

**Verify location**:
```bash
pwd
ls -la
```

**Expected Output**:
```
/usr/src/kodekloudrepos/apps
total 4
-rw-r--r-- 1 root root 34 Sep 28 08:29 info.txt
```

---

## 🔹 Step 3: Check Stashed Changes

```bash
git stash list
```

**Purpose**: List all stashed changes to identify the `stash@{1}` entry.

**Expected Output**:
```
stash@{0}: WIP on master: a329bc7 initial commit
stash@{1}: WIP on master: a329bc7 initial commit
```

**Target Stash**: `stash@{1}` - This is the stash to restore.

---

## 🔹 Step 4: Verify Current Branch

```bash
git branch
```

**Purpose**: Confirm that you are on the `master` branch.

**Expected Output**:
```
* master
```

**Current State**: The repository is on the `master` branch, ready for stash application.

---

## 🔹 Step 5: Apply Stash

```bash
git stash apply stash@{1}
```

**Purpose**: Restore the changes from `stash@{1}` to the working tree and index.

**Expected Output**:
```
On branch master
Your branch is up to date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   welcome.txt
```

**Stash Application Effects**:
- Restores the changes from `stash@{1}` (e.g., adds `welcome.txt` to the index).
- Keeps the stash in the stash list for future reference.

---

## 🔹 Step 6: Verify Stash Application

```bash
git status
```

**Purpose**: Confirm the restored changes are staged and ready to commit.

**Expected Output**:
```
On branch master
Your branch is up to date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   welcome.txt
```

**Validation**: The `welcome.txt` file is staged, indicating successful stash application.

---

## 🔹 Step 7: Commit Restored Changes

```bash
git commit -m "Applied stash changes"
```

**Purpose**: Commit the restored changes with a descriptive message.

**Expected Output**:
```
[master 35fe725] Applied stash changes
 1 file changed, 1 insertion(+)
 create mode 100644 welcome.txt
```

**Commit Effects**:
- Creates a new commit on the `master` branch with the restored changes.
- Uses a clear commit message to indicate the stash application.

---

## 🔹 Step 8: Verify Repository Status

```bash
git status
```

**Purpose**: Ensure the working tree is clean and the branch is ready to push.

**Expected Output**:
```
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
nothing to commit, working tree clean
```

**Current State**: The local `master` branch is ahead of `origin/master` by the new commit.

---

## 🔹 Step 9: Push Changes to Remote Repository

```bash
git push origin master
```

**Purpose**: Push the new commit to the remote `master` branch.

**Expected Output**:
```
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 302 bytes | 302.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To /opt/apps.git
   a329bc7..35fe725  master -> master
```

**Push Effects**:
- Updates the remote `master` branch with the new commit.
- Synchronizes the local and remote repositories.

---

## 🔹 Step 10: Final Verification

```bash
git log --oneline --all --graph
ls
```

**Purpose**: Confirm the commit history and working tree reflect the applied changes.

**Expected Output**:
```
* 35fe725 (HEAD -> master, origin/master) Applied stash changes
* a329bc7 initial commit
```

**Working Tree Output**:
```
info.txt  welcome.txt
```

**Success Indicators**:
- Commit history includes the new commit (`Applied stash changes`).
- `welcome.txt` is present in the working tree.
- Local and remote `master` branches are synchronized.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/apps/

# Check stash and branch
git stash list
git branch

# Apply stash and verify
git stash apply stash@{1}
git status

# Commit and push
git commit -m "Applied stash changes"
git push origin master

# Final verification
git status
git log --oneline --all --graph
ls
```

---

## 💡 What is Git Stash?

**Git Stash Definition**: Git stash temporarily saves uncommitted changes (both staged and unstaged) in a stack, allowing you to switch branches or clean the working tree without losing work. The `git stash apply` command restores these changes.

### **Key Benefits**:
- **Work Preservation**: Save in-progress changes without committing.
- **Branch Switching**: Allows clean branch switches while preserving changes.
- **Flexible Workflow**: Restore changes when ready to continue work.
- **Change Isolation**: Keep working tree clean for other tasks.

### **How Git Stash Apply Works**:
1. **Saves Changes**: `git stash` stores changes in a stack with identifiers (e.g., `stash@{1}`).
2. **Restores Changes**: `git stash apply stash@{n}` reapplies changes to the working tree and index.
3. **Preserves Stash**: The stash remains in the stack unless explicitly dropped.
4. **Commits Changes**: Restored changes can be committed as needed.

### **Common Use Cases**:
- **Temporary Storage**: Save incomplete work to switch tasks.
- **Conflict Avoidance**: Stash changes to pull or merge without conflicts.
- **Experimentation**: Test changes and revert without committing.
- **Workflow Flexibility**: Restore changes to continue work later.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Stash Not Found**
**Symptoms**: `stash@{1}` does not exist or incorrect identifier.
**Solution**: Verify stash list and use the correct identifier.
```bash
git stash list
git stash apply <correct-stash-id>
```

### **Issue 2: Conflicts During Stash Apply**
**Symptoms**: Merge conflicts when applying the stash.
**Solution**: Resolve conflicts manually and continue.
```bash
# Edit conflicted files
git add <resolved-files>
git commit -m "Resolved stash conflicts"
```

### **Issue 3: Push Rejected**
**Symptoms**: Push fails due to branch divergence or permissions.
**Solution**: Pull latest changes and retry push.
```bash
git pull origin master
git push origin master
```

### **Issue 4: Stash Applied but Not Staged**
**Symptoms**: Changes appear in working tree but not staged.
**Solution**: Stage changes manually before committing.
```bash
git add .
git commit -m "Applied stash changes"
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **restoring specific stashed changes** (`stash@{1}`) and integrating them into the `master` branch while ensuring the changes are properly committed and pushed to the remote repository.

**💡 Solution Approach:**

1. **Repository Navigation**: Located the repository at `/usr/src/kodekloudrepos/apps/`.
2. **Stash Identification**: Used `git stash list` to confirm `stash@{1}` exists.
3. **Stash Application**: Applied `stash@{1}` to restore changes (e.g., `welcome.txt`).
4. **Change Verification**: Checked `git status` to confirm restored changes are staged.
5. **Commit Creation**: Committed changes with a clear message (`Applied stash changes`).
6. **Remote Synchronization**: Pushed the commit to `origin/master`.
7. **Final Validation**: Verified the commit history and working tree.

**🎯 Key Success Factors:**
- **Accurate Stash Targeting**: Correctly applied `stash@{1}`.
- **Clean Application**: Ensured no conflicts during stash restoration.
- **Clear Commit Message**: Used a descriptive message for traceability.
- **Remote Update**: Successfully pushed changes to the remote repository.
- **Thorough Verification**: Confirmed the final state with multiple checks.

**⚠️ Critical Operation Details:**
- **Stash Identifier**: Used `stash@{1}` to restore the correct changes.
- **Branch Context**: Applied stash on the `master` branch.
- **Commit Creation**: Created a new commit for the restored changes.
- **Remote Push**: Ensured remote repository reflects the new commit.
- **Working Tree**: Verified `welcome.txt` was added correctly.

**🔒 Git Stash Benefits:**
- **Change Preservation**: Restored in-progress work without loss.
- **Controlled Integration**: Committed changes cleanly to `master`.
- **Workflow Continuity**: Enabled seamless continuation of work.
- **Team Collaboration**: Pushed changes for team access.

---

## ⚠️ Important Production Notes

🔧 **Non-Destructive Stash**: `git stash apply` preserves the stash for future use; use `git stash drop stash@{1}` if no longer needed.

🔐 **Change Validation**: Verify restored changes before committing to avoid errors.

📊 **Team Coordination**: Notify team after pushing changes to `master`.

🛡️ **Backup Consideration**: Ensure critical changes are backed up before applying stashes.