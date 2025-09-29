**🌟 Task 28 - Cherry-Pick Commit from Feature Branch to Master and Push**

**📌 Task Description**

The **Nautilus application development team** has a project repository `/opt/media.git`, cloned to `/usr/src/kodekloudrepos/media` on the **Storage server** in **Stratos DC**. They want to merge one specific commit from the `feature` branch into the `master` branch. The commit message to pick is **"Update info.txt"**.

👉 **Your task:** Use Git cherry-pick to selectively apply a specific commit from one branch to another without merging entire branches.

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
cd /usr/src/kodekloudrepos/media/
```

**Purpose**: Navigate to the cloned Git repository where the cherry-pick operation will be performed.

**Verify location**:
```bash
pwd
ls -la
```

---

## 🔹 Step 3: Check existing branches

```bash
git branch
git branch -a
```

**Purpose**: View all available branches to understand the repository structure.

**Expected Output**:
```
* feature
  master
  remotes/origin/feature
  remotes/origin/master
```

**Branch Information**:
- **Current branch**: Indicated by asterisk (*)
- **Local branches**: feature, master
- **Remote branches**: origin/feature, origin/master

---

## 🔹 Step 4: Check commit history on feature branch

```bash
git log --oneline
```

**Purpose**: View commits on the current branch to find the target commit with "Update info.txt" message.

**Expected Output Example**:
```
2aaf343 (HEAD -> feature, origin/feature) Update welcome.txt
e7f7f0f Update info.txt
b2b709a (origin/master, master) Add welcome.txt
6813f47 initial commit
```

**Target Commit**: `e7f7f0f Update info.txt` - This is the commit to cherry-pick.

---

## 🔹 Step 5: Check master branch status

```bash
git checkout master
git log --oneline
```

**Purpose**: Switch to master branch and view its current commit history.

**Expected Output**:
```
b2b709a (HEAD -> master, origin/master) Add welcome.txt
6813f47 initial commit
```

**Current State**: Master branch doesn't have the "Update info.txt" commit yet.

---

## 🔹 Step 6: Identify target commit hash

```bash
git log --oneline --all --graph
```

**Purpose**: View commits from all branches to clearly identify the target commit hash.

**Alternative method**:
```bash
git log --oneline feature
```

**Target Identification**: Note the commit hash for "Update info.txt" (e.g., `e7f7f0f`).

---

## 🔹 Step 7: Perform cherry-pick operation

```bash
git cherry-pick e7f7f0f
```

**Purpose**: Apply the specific commit from the feature branch to the current master branch.

**Expected Output**:
```
[master c7651dc] Update info.txt
 1 file changed, X insertions(+), Y deletions(-)
```

**Cherry-pick Process**:
- Creates a new commit on master branch
- Applies the same changes as the original commit
- Uses the same commit message
- Generates new commit hash

---

## 🔹 Step 8: Verify cherry-pick success

```bash
git log --oneline
```

**Purpose**: Confirm the commit was successfully applied to master branch.

**Expected Output**:
```
c7651dc (HEAD -> master) Update info.txt
b2b709a (origin/master) Add welcome.txt
6813f47 initial commit
```

**Success Indicators**:
- **New commit**: "Update info.txt" now appears on master
- **New hash**: Different hash than original (c7651dc vs e7f7f0f)
- **Same message**: Commit message preserved

---

## 🔹 Step 9: Check file changes

```bash
git show HEAD
cat info.txt
```

**Purpose**: Verify that the file changes from the cherry-picked commit were applied correctly.

**Validation**: Compare file content with expected changes from the feature branch commit.

---

## 🔹 Step 10: Push updated master branch

```bash
git push origin master
```

**Purpose**: Push the updated master branch with the cherry-picked commit to the remote repository.

**Expected Output**:
```
Enumerating objects: X, done.
Counting objects: 100% (X/X), done.
...
To /opt/media.git
   b2b709a..c7651dc  master -> master
```

**Push Success**: Shows the range of commits being pushed to remote.

---

## 🔹 Step 11: Final verification

```bash
git log --oneline --all --graph
```

**Purpose**: View the complete repository state after cherry-pick and push operations.

**Expected Result**:
```
* c7651dc (HEAD -> master, origin/master) Update info.txt
| * 2aaf343 (origin/feature, feature) Update welcome.txt
| * e7f7f0f Update info.txt
|/
* b2b709a Add welcome.txt
* 6813f47 initial commit
```

**Final State**: Master now includes the cherry-picked commit with updated remote tracking.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/media/

# Examine repository state
git branch -a
git log --oneline

# Switch to master and cherry-pick
git checkout master
git cherry-pick e7f7f0f  # Replace with actual commit hash

# Push changes and verify
git push origin master
git log --oneline --all --graph
```

---

## 💡 What is Git Cherry-Pick?

**Cherry-pick Definition**: Git cherry-pick allows you to select a specific commit from one branch and apply it to another branch without merging the entire branch.

### **Key Benefits**:
- **Selective Integration**: Bring specific fixes without all branch changes
- **Clean History**: Avoid unnecessary merge commits
- **Flexible Workflow**: Apply commits in different order than original
- **Risk Mitigation**: Test individual changes before full branch merge

### **How Cherry-Pick Works**:
1. **Identifies Target**: Finds the specific commit by hash
2. **Extracts Changes**: Reads the diff from the original commit
3. **Creates New Commit**: Applies changes as new commit on current branch
4. **Preserves Message**: Maintains original commit message
5. **Generates New Hash**: Creates unique hash for the new commit location

### **Common Use Cases**:
- **Hotfixes**: Apply critical fixes to stable branches
- **Feature Selection**: Include specific features without entire branch
- **Backporting**: Apply fixes to older versions
- **Cleanup**: Reorganize commit history

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Cherry-pick conflicts**
**Symptoms**: Merge conflicts during cherry-pick operation
**Solution**: Resolve conflicts manually and complete cherry-pick
```bash
# Edit conflicted files
git add <resolved-files>
git cherry-pick --continue
```

### **Issue 2: Wrong commit hash**
**Symptoms**: "bad object" error or wrong commit picked
**Solution**: Find correct commit hash and retry
```bash
git log --oneline --all --grep="Update info.txt"
git cherry-pick <correct-hash>
```

### **Issue 3: Cherry-pick already applied**
**Symptoms**: "empty commit" or duplicate changes
**Solution**: Skip empty commit or abort cherry-pick
```bash
git cherry-pick --skip
# or
git cherry-pick --abort
```

### **Issue 4: Push rejected**
**Symptoms**: Remote push fails due to branch state
**Solution**: Pull latest changes and retry push
```bash
git pull origin master
git push origin master
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **selectively applying a specific commit from one branch to another** without merging entire branch histories, requiring precise commit identification and application.

**💡 Solution Approach:**

1. **Repository Navigation**: Located the cloned repository in `/usr/src/kodekloudrepos/media/` directory
2. **Branch Analysis**: Examined available branches (feature, master) and their respective commit histories
3. **Commit Identification**: Found the specific commit with "Update info.txt" message using git log commands
4. **Branch Management**: Switched to master branch as the target for cherry-pick operation
5. **Cherry-pick Execution**: Applied the specific commit using `git cherry-pick` with the identified commit hash
6. **Remote Synchronization**: Pushed the updated master branch to remote repository for team access

**🎯 Key Success Factors:**
- **Precise commit targeting** using commit hash to identify exact "Update info.txt" commit
- **Branch context switching** ensuring cherry-pick applies to correct target branch (master)
- **Commit hash understanding** recognizing that cherry-pick creates new commit with different hash
- **History preservation** maintaining clean commit history while selectively applying changes
- **Remote synchronization** ensuring changes available to entire development team
- **Verification procedures** confirming successful application through multiple validation methods

**⚠️ Critical Operation Details:**
- **Commit hash identification** - must target exact commit with "Update info.txt" message
- **Branch positioning** - must be on master branch when executing cherry-pick
- **New commit creation** - cherry-pick creates new commit with different hash but same changes
- **Message preservation** - original commit message maintained in cherry-picked commit
- **Remote push** - updated master branch pushed to origin for team access

**🔒 Cherry-Pick Benefits:**
- **Selective integration** - apply specific changes without entire branch merge
- **Clean history** - avoid complex merge commits for simple change application
- **Risk mitigation** - test individual changes before considering full branch integration
- **Workflow flexibility** - apply commits in different sequence than original development

---

## ⚠️ Important Production Notes

🔧 **Selective Integration**: Cherry-pick enables precise change application without full branch merging

🔐 **History Management**: Creates new commits while preserving original change content and commit messages

📊 **Team Collaboration**: Updated master branch available to all team members after remote push

🛡️ **Change Isolation**: Individual commit application allows for targeted testing and validation