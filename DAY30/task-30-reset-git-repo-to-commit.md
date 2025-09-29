# 🌟 Task 30 - Reset Git Repository to Specific Commit

## 📌 Task Description

The **Nautilus application development team** is working on a test Git repository located at `/usr/src/kodekloudrepos/games` on the **Storage server** in **Stratos DC**. The repository contains test commits that need to be removed to clean up the commit history and working tree. The goal is to reset the repository so that the `master` branch points to the commit with the message "add data.txt file," leaving only the initial commit and this specific commit in the history. The changes must also be pushed to the remote repository.

👉 **Your task**: Reset the Git commit history to include only the initial commit and the "add data.txt file" commit, and force-push the changes to the remote repository.

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
cd /usr/src/kodekloudrepos/games/
```

**Purpose**: Navigate to the Git repository where the reset operation will be performed.

**Verify location**:
```bash
pwd
ls -la
```

**Expected Output**:
```
/usr/src/kodekloudrepos/games
total 8
-rw-r--r-- 1 root root 32 Sep 28 07:58 games.txt
-rw-r--r-- 1 root root 10 Sep 28 07:58 info.txt
```

---

## 🔹 Step 3: Check Commit History

```bash
git log --oneline
```

**Purpose**: View the commit history to identify the target commit with the message "add data.txt file."

**Expected Output**:
```
95784b9 (HEAD -> master, origin/master) Test Commit10
aa6f11e Test Commit9
5e5d502 Test Commit8
224f60f Test Commit7
8a2674b Test Commit6
38d04a8 Test Commit5
7da5181 Test Commit4
70c181e Test Commit3
9292215 Test Commit2
5f1f5e7 Test Commit1
1575dd2 add data.txt file
7f8b49d initial commit
```

**Target Commit**: `1575dd2 add data.txt file` - This is the commit to reset to.

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

**Current State**: The repository is on the `master` branch with multiple test commits.

---

## 🔹 Step 5: Perform Hard Reset to Target Commit

```bash
git reset --hard 1575dd2
```

**Purpose**: Reset the `master` branch and working tree to the commit with hash `1575dd2` ("add data.txt file").

**Expected Output**:
```
HEAD is now at 1575dd2 add data.txt file
```

**Reset Effects**:
- Moves `HEAD` and `master` branch to the specified commit.
- Discards all subsequent commits and changes in the working tree.
- Resets the working directory to match the state of the target commit.

---

## 🔹 Step 6: Verify Reset Commit History

```bash
git log --oneline
```

**Purpose**: Confirm that the commit history now contains only the initial commit and the "add data.txt file" commit.

**Expected Output**:
```
1575dd2 (HEAD -> master) add data.txt file
7f8b49d initial commit
```

**Validation**: The history is cleaned, with only two commits remaining.

---

## 🔹 Step 7: Check Working Tree

```bash
ls
git status
```

**Purpose**: Verify the working tree and repository status after the reset.

**Expected Output**:
```
data.txt
On branch master
Your branch is behind 'origin/master' by 10 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)
nothing to commit, working tree clean
```

**Note**: The branch appears behind `origin/master` because the remote still has the additional commits.

---

## 🔹 Step 8: Force Push to Remote Repository

```bash
git push --force origin master
```

**Purpose**: Force-push the reset `master` branch to the remote repository to synchronize it with the local state.

**Expected Output**:
```
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To /opt/games.git
 + 95784b9...1575dd2 master -> master (forced update)
```

**Push Effects**:
- Overwrites the remote `master` branch to match the local `master` branch.
- Removes all commits after `1575dd2` from the remote repository.

---

## 🔹 Step 9: Final Verification

```bash
git status
git log --oneline --all --graph
```

**Purpose**: Confirm the repository state and that the remote branch is updated.

**Expected Output**:
```
On branch master
Your branch is up to date with 'origin/master'.
nothing to commit, working tree clean
```

**Git Log Output**:
```
* 1575dd2 (HEAD -> master, origin/master) add data.txt file
* 7f8b49d initial commit
```

**Success Indicators**:
- Only two commits remain in the history.
- Working tree contains only `data.txt`.
- Local and remote `master` branches are synchronized.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/games/

# Examine repository state
git log --oneline
git branch
ls

# Reset to target commit
git reset --hard 1575dd2

# Verify reset
git log --oneline
git status

# Force push to remote
git push --force origin master

# Final verification
git status
git log --oneline --all --graph
```

---

## 💡 What is Git Reset?

**Git Reset Definition**: Git reset moves the `HEAD` and branch pointer to a specified commit, optionally modifying the working tree and index. The `--hard` option discards all changes after the target commit.

### **Key Benefits**:
- **History Cleanup**: Remove unwanted commits from the branch history.
- **State Restoration**: Revert the repository to a specific point in time.
- **Simplified Workflow**: Reset to a known good state for testing or cleanup.

### **How Git Reset --hard Works**:
1. **Moves HEAD**: Points `HEAD` and the current branch to the specified commit.
2. **Resets Index**: Updates the staging area to match the target commit.
3. **Resets Working Tree**: Reverts the working directory to the state of the target commit.
4. **Discards Commits**: Removes all subsequent commits from the branch history.

### **Common Use Cases**:
- **Repository Cleanup**: Remove test or erroneous commits.
- **Branch Realignment**: Reset to a specific commit for development restart.
- **Error Recovery**: Revert to a stable state after problematic changes.
- **History Simplification**: Clean up commit history for clarity.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Wrong Commit Hash**
**Symptoms**: Error like "bad object" or reset to incorrect commit.
**Solution**: Verify the correct commit hash and retry.
```bash
git log --oneline --all --grep="add data.txt file"
git reset --hard <correct-hash>
```

### **Issue 2: Push Rejected**
**Symptoms**: Force push fails due to permissions or branch protection.
**Solution**: Ensure you have push permissions and retry with `--force`.
```bash
git push --force origin master
```

### **Issue 3: Untracked Files Remain**
**Symptoms**: Extra files remain in the working tree after reset.
**Solution**: Clean untracked files and directories.
```bash
git clean -fd
```

### **Issue 4: Remote Branch Not Updated**
**Symptoms**: Remote repository still shows old commits.
**Solution**: Verify force push and check remote history.
```bash
git fetch origin
git log --oneline --all --graph
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **resetting the repository to a specific commit** while ensuring the commit history and working tree are cleaned, and synchronizing the changes with the remote repository without losing critical data.

**💡 Solution Approach:**

1. **Repository Navigation**: Located the repository at `/usr/src/kodekloudrepos/games/`.
2. **Commit Identification**: Identified the target commit (`1575dd2 add data.txt file`) using `git log`.
3. **Hard Reset**: Used `git reset --hard` to reset the `master` branch to the target commit, removing all subsequent commits and changes.
4. **Working Tree Cleanup**: Ensured the working tree only contains `data.txt` post-reset.
5. **Force Push**: Synchronized the reset state with the remote repository using `git push --force`.
6. **Verification**: Confirmed the commit history and working tree state locally and remotely.

**🎯 Key Success Factors:**
- **Precise Commit Targeting**: Correctly identified the `1575dd2` commit.
- **Hard Reset Accuracy**: Ensured all unwanted commits and files were removed.
- **Remote Synchronization**: Successfully force-pushed to update the remote repository.
- **Clean History**: Maintained only the initial commit and `add data.txt file` commit.
- **Verification Rigor**: Validated the final state with multiple checks.

**⚠️ Critical Operation Details:**
- **Commit Hash Accuracy**: Used `1575dd2` to target the correct commit.
- **Hard Reset**: Discarded all changes after the target commit.
- **Force Push**: Overwrote remote history to match the reset state.
- **Working Tree**: Ensured only `data.txt` remains after reset.
- **Remote Update**: Confirmed remote `master` reflects the reset history.

**🔒 Git Reset Benefits:**
- **History Control**: Cleaned up unwanted test commits.
- **State Restoration**: Restored the repository to a specific point.
- **Remote Consistency**: Ensured local and remote repositories are aligned.
- **Simplified Repository**: Reduced commit history for clarity.

---

## ⚠️ Important Production Notes

🔧 **Destructive Operation**: `git reset --hard` and `git push --force` are irreversible; use with caution.

🔐 **History Management**: Removes commits permanently from the branch history.

📊 **Team Coordination**: Notify team before force-pushing to avoid conflicts.

🛡️ **Backup Consideration**: Consider backing up the repository before resetting.