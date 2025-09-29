**🌟 Task 27 - Revert Latest Commit in Git Repository on Storage Server**

**📌 Task Description**

The **Nautilus application development team** requested to revert the latest commit (HEAD) to the previous commit in the `/usr/src/kodekloudrepos/games` Git repository on the **Storage server**.

**Requirements:**
- Revert **HEAD** to previous commit (which has the initial commit message)
- Use the commit message **"revert games"** (all lowercase) for the revert commit

👉 **Your task:** Safely revert the most recent commit while preserving Git history and creating a new revert commit.

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
cd /usr/src/kodekloudrepos/games
```

**Purpose**: Navigate to the target Git repository where the revert operation will be performed.

**Verify location**:
```bash
pwd
ls -la
```

---

## 🔹 Step 3: Examine current Git history

```bash
git log --oneline
```

**Purpose**: View the commit history to understand what will be reverted.

**Expected Output Example**:
```
abc1234 Latest commit message (HEAD)
def5678 initial commit
```

**Alternative detailed view**:
```bash
git log -n 3
```

**Important Notes**:
- **HEAD commit**: The latest commit that needs to be reverted
- **Previous commit**: Should contain "initial commit" message
- **Commit hashes**: Note these for verification purposes

---

## 🔹 Step 4: Check repository status before revert

```bash
git status
```

**Purpose**: Ensure the working directory is clean before performing the revert operation.

**Expected Output**:
```
On branch master
nothing to commit, working tree clean
```

**Clean Status Required**: Repository should have no uncommitted changes before reverting.

---

## 🔹 Step 5: Perform the revert operation

```bash
git revert HEAD
```

**Purpose**: Revert the changes introduced by the latest commit (HEAD).

**Process**:
1. Git will open a text editor for the commit message
2. Default message will be something like "Revert '[original commit message]'"
3. **Replace** the default message with **"revert games"**
4. Save and exit the editor

**Editor Actions**:
- **In vi/vim**: Press `i` to edit, type message, press `Esc`, type `:wq`, press `Enter`
- **In nano**: Edit message, press `Ctrl+X`, then `Y`, then `Enter`

---

## 🔹 Step 6: Alternative method with direct commit message

```bash
git revert HEAD -m "revert games"
```

**Purpose**: Perform revert operation with commit message specified directly in command.

**Use this if**: You prefer to avoid the interactive editor approach.

**Note**: The `-m` flag allows specifying the commit message directly.

---

## 🔹 Step 7: Verify the revert commit was created

```bash
git log --oneline -n 3
```

**Purpose**: Confirm that the revert commit was created with the correct message.

**Expected Output**:
```
xyz9876 revert games
abc1234 Latest commit message
def5678 initial commit
```

**Verification Points**:
- **New HEAD**: Should show "revert games" commit message
- **Commit order**: Revert commit on top, followed by original commits
- **History preservation**: All previous commits remain in history

---

## 🔹 Step 8: Check repository status after revert

```bash
git status
```

**Purpose**: Verify that the revert operation completed cleanly.

**Expected Output**:
```
On branch master
nothing to commit, working tree clean
```

---

## 🔹 Step 9: Verify file changes were reverted

```bash
ls -la
git diff HEAD~2 HEAD
```

**Purpose**: Compare the current state with the state before the reverted commit to confirm changes were undone.

**Alternative verification**:
```bash
git show HEAD
```

**Expected Result**: Should show the revert commit with changes that undo the previous commit.

---

## 🔹 Step 10: Check specific file content (if applicable)

```bash
cat games.txt  # If this file exists
```

**Purpose**: Verify that file contents match the expected state after revert.

**File State**: Should reflect the content as it was before the reverted commit.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/games

# Examine current state
git log --oneline
git status

# Perform revert
git revert HEAD
# (Edit commit message to "revert games" in editor)

# Alternative: Direct commit message
git revert HEAD -m "revert games"

# Verify revert
git log --oneline -n 3
git status
```

---

## 💡 Additional Tips

- **History preservation**: `git revert` creates a new commit instead of removing history
- **Safe operation**: Revert is safer than `git reset` as it doesn't rewrite history
- **Commit message**: Use descriptive messages to document why revert was needed
- **Multiple commits**: Use `git revert <hash1> <hash2>` to revert multiple commits
- **Merge commits**: Reverting merge commits requires `-m` flag with parent number

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Editor doesn't open or is unfamiliar**
**Symptoms**: Strange editor opens or command hangs
**Solution**: Set your preferred editor or use direct message flag
```bash
export EDITOR=nano  # or vi, vim, etc.
# Or use direct message approach:
git revert HEAD -m "revert games"
```

### **Issue 2: Revert fails due to conflicts**
**Symptoms**: Git reports merge conflicts during revert
**Solution**: Resolve conflicts manually and complete revert
```bash
# Edit conflicted files to resolve conflicts
git add <resolved-files>
git revert --continue
```

### **Issue 3: Wrong commit gets reverted**
**Symptoms**: Reverted the wrong commit
**Solution**: Revert the revert commit to restore original state
```bash
git log --oneline  # Find the revert commit hash
git revert <revert-commit-hash>
```

### **Issue 4: Working directory not clean**
**Symptoms**: Cannot revert due to uncommitted changes
**Solution**: Commit or stash changes before reverting
```bash
git stash  # Temporarily save changes
git revert HEAD
git stash pop  # Restore changes if needed
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **safely reverting the latest commit while preserving Git history** and ensuring the revert commit uses the exact specified commit message format.

**💡 Solution Approach:**

1. **History Analysis**: Examined current Git log to identify HEAD commit and verify previous commit contains "initial commit" message
2. **Clean State Verification**: Confirmed working directory was clean before performing revert operation
3. **Revert Operation**: Used `git revert HEAD` to create new commit that undoes changes from latest commit
4. **Commit Message Management**: Specified exact commit message "revert games" (lowercase) as required
5. **Verification Process**: Confirmed revert commit creation and validated that changes were properly undone
6. **History Integrity**: Ensured all previous commits remain in history while new revert commit is added

**🎯 Key Success Factors:**
- **Safe revert method** using `git revert` instead of destructive `git reset` commands
- **History preservation** maintaining complete commit history while undoing specific changes
- **Commit message accuracy** using exact lowercase format "revert games" as specified
- **State verification** confirming clean working directory before and after revert operation
- **Change validation** verifying that reverted changes match expected repository state
- **Process documentation** following proper Git workflow for collaborative development

**⚠️ Critical Operation Details:**
- **Revert vs Reset** - `git revert` creates new commit, `git reset` rewrites history
- **HEAD targeting** - specifically reverting the most recent commit (HEAD)
- **Message format** - exact lowercase "revert games" message as required
- **History integrity** - all previous commits preserved in chronological order
- **Working directory** - must be clean before revert operation

**🔒 Git History Benefits:**
- **Audit trail** - complete record of what was changed and when it was reverted
- **Collaboration safety** - other developers can see the revert in shared repository
- **Recovery capability** - revert commits can themselves be reverted if needed
- **Timeline preservation** - maintains chronological development history

---

## ⚠️ Important Production Notes

🔧 **Safe History Management**: Revert operation preserves complete Git history while undoing specific commit changes

🔐 **Collaboration Friendly**: Other team members can see revert commit in shared repository history

📊 **Change Tracking**: Revert commit provides clear documentation of what changes were undone and when

🛡️ **Recovery Options**: Revert commits can be reverted themselves if original changes need to be restored