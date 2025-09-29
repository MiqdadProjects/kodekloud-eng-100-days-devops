# 🌟 Task 32 - Rebase Feature Branch onto Master Branch

## 📌 Task Description

The **Nautilus application development team** is working on a project repository located at `/opt/ecommerce.git`, cloned to `/usr/src/kodekloudrepos/ecommerce` on the **Storage server** in **Stratos DC**. A developer working on the `feature` branch wants to incorporate recent changes from the `master` branch into their work without losing any changes in the `feature` branch. They also want to avoid creating a merge commit, preferring a clean history through rebasing. The task requires rebasing the `feature` branch onto the `master` branch and pushing the updated `feature` branch to the remote repository.

👉 **Your task**: Rebase the `feature` branch onto the `master` branch without losing any changes, avoid merge commits, and push the updated `feature` branch to the remote repository.

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
cd /usr/src/kodekloudrepos/ecommerce/
```

**Purpose**: Navigate to the cloned Git repository where the rebase operation will be performed.

**Verify location**:
```bash
pwd
ls -la
```

**Expected Output**:
```
/usr/src/kodekloudrepos/ecommerce
total 8
-rw-r--r-- 1 root root 34 Sep 28 08:38 feature.txt
-rw-r--r-- 1 root root 10 Sep 28 08:38 info.txt
```

---

## 🔹 Step 3: Check Current Branch and Available Branches

```bash
git branch
```

**Purpose**: Confirm the current branch and list all available branches.

**Expected Output**:
```
* feature
  master
```

**Current State**: The repository is on the `feature` branch, with `master` available for rebasing.

---

## 🔹 Step 4: Verify Commit History

```bash
git log --oneline --all --graph
```

**Purpose**: Examine the commit history to understand the state of both branches.

**Expected Output (Example)**:
```
* 4b269b1 (HEAD -> feature, origin/feature) Add new feature
| * 273c204 (origin/master, master) Update info.txt
|/
* 62ef9e8 initial commit
```

**Branch State**:
- `feature`: Contains commits like "Add new feature."
- `master`: Contains recent updates like "Update info.txt."
- The branches have diverged, requiring rebasing to incorporate `master` changes into `feature`.

---

## 🔹 Step 5: Rebase Feature Branch onto Master

```bash
git rebase master feature
```

**Purpose**: Rebase the `feature` branch onto the latest commit of the `master` branch.

**Expected Output**:
```
Successfully rebased and updated refs/heads/feature.
```

**Rebase Effects**:
- Replays the `feature` branch commits on top of the latest `master` commit.
- Preserves all changes from the `feature` branch.
- Creates a linear history without merge commits.
- Updates the `feature` branch pointer to the new commit history.

---

## 🔹 Step 6: Configure Pull to Use Rebase

```bash
git config pull.rebase true
```

**Purpose**: Set the pull strategy to rebase to avoid merge commits when pulling from the remote branch.

**Note**: This step addresses the divergent branches warning during `git pull`.

---

## 🔹 Step 7: Pull Remote Feature Branch with Rebase

```bash
git pull origin feature
```

**Purpose**: Synchronize the local `feature` branch with the remote `origin/feature` branch, applying rebase to resolve divergence.

**Expected Output**:
```
From /opt/ecommerce
 * branch            feature    -> FETCH_HEAD
warning: skipped previously applied commit 273c204
hint: use --reapply-cherry-picks to include skipped commits
hint: Disable this message with "git config advice.skippedCherryPicks false"
Successfully rebased and updated refs/heads/feature.
```

**Pull Effects**:
- Rebases local `feature` branch changes on top of `origin/feature`.
- Skips duplicate commits (e.g., `273c204`) already applied during the rebase.
- Ensures a clean, linear history.

---

## 🔹 Step 8: Verify Working Tree and Status

```bash
git status
ls
```

**Purpose**: Confirm the working tree and repository status after rebasing.

**Expected Output**:
```
On branch feature
Your branch is ahead of 'origin/feature' by 2 commits.
  (use "git push" to publish your local commits)
nothing to commit, working tree clean
```

**Working Tree Output**:
```
feature.txt  info.txt
```

**Validation**: The working tree contains expected files, and the branch is ready to push.

---

## 🔹 Step 9: Push Updated Feature Branch

```bash
git push origin feature
```

**Purpose**: Push the rebased `feature` branch to the remote repository.

**Expected Output**:
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 314 bytes | 314.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To /opt/ecommerce.git
   4b269b1..d0cbfe7  feature -> feature
```

**Push Effects**:
- Updates the remote `feature` branch with the rebased history.
- Synchronizes local and remote repositories.

---

## 🔹 Step 10: Final Verification

```bash
git log --oneline --all --graph
```

**Purpose**: Confirm the commit history reflects the rebased `feature` branch.

**Expected Output**:
```
* d0cbfe7 (HEAD -> feature, origin/feature) Add new feature
* 273c204 (origin/master, master) Update info.txt
* 62ef9e8 initial commit
```

**Success Indicators**:
- `feature` branch includes `master` commits (e.g., `Update info.txt`).
- `feature` branch commits (e.g., `Add new feature`) are preserved.
- History is linear, with no merge commits.
- Local and remote `feature` branches are synchronized.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/ecommerce/

# Check branches and history
git branch
git log --oneline --all --graph
ls

# Rebase feature onto master
git rebase master feature

# Configure pull to use rebase
git config pull.rebase true

# Pull and push
git pull origin feature
git push origin feature

# Final verification
git status
git log --oneline --all --graph
ls
```

---

## 💡 What is Git Rebase?

**Git Rebase Definition**: Git rebase moves or replays a branch’s commits on top of another branch, creating a linear history without merge commits.

### **Key Benefits**:
- **Clean History**: Produces a linear commit history without merge commits.
- **Change Preservation**: Retains all changes from the rebased branch.
- **Simplified Collaboration**: Makes history easier to read and review.
- **Flexible Workflow**: Incorporates updates from other branches cleanly.

### **How Git Rebase Works**:
1. **Identifies Base**: Uses the target branch (e.g., `master`) as the new base.
2. **Replays Commits**: Applies the source branch’s commits (e.g., `feature`) on top of the base.
3. **Updates Branch**: Moves the branch pointer to the new commit history.
4. **Resolves Conflicts**: Pauses for manual conflict resolution if needed.

### **Common Use Cases**:
- **Feature Updates**: Incorporate `master` changes into a feature branch.
- **History Cleanup**: Simplify commit history before merging.
- **Branch Synchronization**: Align branches without merge commits.
- **Team Collaboration**: Maintain a clean history for reviews.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Rebase Conflicts**
**Symptoms**: Conflicts occur during rebase, pausing the operation.
**Solution**: Resolve conflicts manually and continue.
```bash
# Edit conflicted files
git add <resolved-files>
git rebase --continue
```

### **Issue 2: Divergent Branches on Pull**
**Symptoms**: `git pull` fails with a "divergent branches" error.
**Solution**: Ensure `pull.rebase true` is set and retry.
```bash
git config pull.rebase true
git pull origin feature
```

### **Issue 3: Push Rejected**
**Symptoms**: Push fails due to non-fast-forward updates.
**Solution**: Force push if rebasing has rewritten history.
```bash
git push --force origin feature
```

### **Issue 4: Lost Commits**
**Symptoms**: Feature branch commits appear missing after rebase.
**Solution**: Check reflog to recover lost commits and retry rebase.
```bash
git reflog
git reset --hard <commit-hash>
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **rebasing the `feature` branch onto `master`** to incorporate recent changes without creating merge commits, while ensuring no data loss and synchronizing with the remote repository.

**💡 Solution Approach:**

1. **Repository Navigation**: Located the repository at `/usr/src/kodekloudrepos/ecommerce/`.
2. **Branch Verification**: Confirmed the `feature` branch is active and `master` contains recent updates.
3. **Rebase Execution**: Rebasing `feature` onto `master` to incorporate changes linearly.
4. **Pull Configuration**: Set `pull.rebase true` to handle divergent branches during `git pull`.
5. **Remote Synchronization**: Pulled and pushed the `feature` branch to align with `origin/feature`.
6. **Final Validation**: Verified the linear history and working tree state.

**🎯 Key Success Factors:**
- **Linear History**: Achieved a clean history without merge commits.
- **Change Preservation**: Retained all `feature` branch changes.
- **Remote Update**: Successfully pushed rebased history to `origin/feature`.
- **Conflict Avoidance**: Ensured smooth rebase with no conflicts.
- **Thorough Verification**: Confirmed the final state with commit history and file checks.

**⚠️ Critical Operation Details:**
- **Rebase Target**: Rebasing `feature` onto `master` incorporated `Update info.txt`.
- **No Merge Commits**: Avoided merge commits for a clean history.
- **Pull Strategy**: Configured `pull.rebase true` to handle divergence.
- **Remote Push**: Updated `origin/feature` with the rebased history.
- **Commit Preservation**: Ensured `Add new feature` was retained.

**🔒 Git Rebase Benefits:**
- **Clean History**: Linear history improves readability and reviewability.
- **Change Integration**: Seamlessly incorporated `master` updates.
- **No Data Loss**: Preserved all `feature` branch changes.
- **Team Collaboration**: Updated remote branch for team access.

---

## ⚠️ Important Production Notes

🔧 **Destructive Operation**: Rebasing rewrites history; use with caution and communicate with the team.

🔐 **Backup Consideration**: Back up the branch before rebasing to avoid accidental data loss.

📊 **Team Coordination**: Notify team before force-pushing to prevent conflicts.

🛡️ **Conflict Management**: Be prepared to resolve conflicts during rebase for smooth execution.