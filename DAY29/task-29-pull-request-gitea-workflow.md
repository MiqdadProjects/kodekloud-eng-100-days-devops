# 🌟 Task 29 - Create and Merge Pull Request for Fox and Grapes Story

## 📌 Task Description

The **Nautilus application development team** needs to manage changes to a repository on the **Storage server** in **Stratos DC**. Direct pushes to the `master` branch are prohibited to ensure only reviewed and approved content is merged. Max has written a story, "The 🦊 Fox and Grapes 🍇," and pushed it to the `story/fox-and-grapes` branch in the remote Git repository hosted on Gitea. The task is to create a Pull Request (PR) to merge Max's story into the `master` branch, assign Tom as a reviewer, and have Tom review and merge the PR.

👉 **Your task**: Create a Pull Request for Max's story, assign Tom as a reviewer, and complete the review and merge process via the Gitea UI.

💡 **Note**: Take screenshots or record the process (e.g., using loom.com) for verification in case the task is marked incomplete.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh max@ststor01
```

**Credentials**:
- Username: max
- Password: Max_pass123

**Purpose**: Connect to the storage server (ststor01) as the user Max.

---

## 🔹 Step 2: Navigate to Cloned Repository

```bash
cd ~/story-blog/
```

**Purpose**: Navigate to the cloned repository in Max's home directory.

**Verify location**:
```bash
pwd
ls -la
```

**Expected Output**:
```
/home/max/story-blog
fox-and-grapes.txt  frogs-and-ox.txt  lion-and-mouse.txt
```

---

## 🔹 Step 3: Check Repository Contents and Commit History

```bash
git branch
git log --oneline --all --graph
```

**Purpose**: Verify branches and commit history to confirm Max's story and Sarah's contributions.

**Expected Output**:
```
* 1ea22a5 (HEAD -> story/fox-and-grapes, origin/story/fox-and-grapes) Added fox-and-grapes story
| * b6f683e (origin/master, master) Merge branch 'story/frogs-and-ox'
| * e66aacf Fix typo in story title
| * af2daf1 Completed frogs-and-ox story
|/
* 6c69be1 Initial commit
```

**Commit Details**:
```bash
git log
```

**Expected Output**:
```
commit 1ea22a5bdea9b3b73bb43f97a55bf7d1ae21bcea
Author: max <max@stratos.xfusioncorp.com>
Date:   Sun Sep 28 07:26:56 2025 +0000
    Added fox-and-grapes story

commit b6f683e47d775e27499f053c0dd074d295c7819a
Author: sarah <sarah@stratos.xfusioncorp.com>
Date:   Sun Sep 28 07:26:55 2025 +0000
    Merge branch 'story/frogs-and-ox'

commit e66aacfb71dc67324f9103cbbfd4d98ed69067cf
Author: sarah <sarah@stratos.xfusioncorp.com>
Date:   Sun Sep 28 07:26:55 2025 +0000
    Fix typo in story title

commit af2daf1a415e731247597c63db4cdde5ef1fce6b
Author: sarah <sarah@stratos.xfusioncorp.com>
Date:   Sun Sep 28 07:26:55 2025 +0000
    Completed frogs-and-ox story
```

**Validation**:
- Confirm Max’s commit: `Added fox-and-grapes story` (hash: `1ea22a5`)
- Confirm Sarah’s commits and author info.

---

## 🔹 Step 4: Verify Master Branch Contents

```bash
git checkout master
ls
```

**Purpose**: Switch to the `master` branch and confirm it does not yet contain `fox-and-grapes.txt`.

**Expected Output**:
```
frogs-and-ox.txt  lion-and-mouse.txt
```

**Current State**: `master` branch lacks Max’s story (`fox-and-grapes.txt`).

---

## 🔹 Step 5: Access Gitea UI and Create Pull Request



1. **Open Gitea UI**:
   - Click the **Gitea UI** button on the top bar to access the Gitea interface.

2. **Log in as Max**:
   - Username: max
   - Password: Max_pass123


3. **Navigate to Repository**:
   - Go to the repository `sarah/story-blog`.


4. **Create Pull Request**: ![screenshot: 1](<TASK 29.png>)
   - Click **Pull Requests** tab.
   - Click **New Pull Request**.
   - Select:
     - **Merge into**: `sarah:master` (destination branch)
     - **Pull from**: `sarah:story/fox-and-grapes` (source branch)
   - Set PR title: `Added fox-and-grapes story`
   ![screenshot: 2](<TASK 29 (2).png>)
   ![screenshot: 3](<TASK 29 (3).png>)
   - Click **Create Pull Request**.

**Purpose**: Initiate a PR to merge Max’s `story/fox-and-grapes` branch into `master`.

---

## 🔹 Step 6: Assign Tom as Reviewer

1. **Go to the Newly Created PR**:
   - Locate the PR titled `Added fox-and-grapes story` in the Gitea UI.

2. **Add Reviewer**:
   - On the PR page, find the **Reviewers** section on the right.
   - Select **tom** as a reviewer.
   - Save the reviewer assignment.

**Purpose**: Assign Tom to review the PR to ensure proper validation before merging.

---

## 🔹 Step 7: Review and Merge PR as Tom

1. **Log out as Max**:
   - Log out from the Gitea UI.

2. **Log in as Tom**:
   - Username: tom
   - Password: Tom_pass123

3. **Access the PR**:
   - Click the **Notification Bell** in the Gitea UI.
   - Navigate to `sarah/story-blog`.
   - Open the PR titled `Added fox-and-grapes story`.

4. **Review and Merge**:
   - Verify the changes (addition of `fox-and-grapes.txt`).
   - Add a description if needed: `Added fox-and-grapes story`.
   - Click **Merge Pull Request**.
   - Confirm the merge.
  ![screenshot: 4 ](<TASK 29 (4).png>)
  ![screenshot: 5](<TASK 29 (5).png>)
**Purpose**: Review and approve Max’s story, merging it into the `master` branch.

---

## 🔹 Step 8: Verify Merge on Storage Server

```bash
git checkout master
git pull origin master
ls
git log --oneline --all --graph
```

**Purpose**: Confirm the `master` branch now includes `fox-and-grapes.txt` and the merge commit.

**Expected Output**:
```
frogs-and-ox.txt  lion-and-mouse.txt  fox-and-grapes.txt
```

**Git Log Output**:
```
*   <new-hash> (HEAD -> master, origin/master) Merge pull request from story/fox-and-grapes
|\
| * 1ea22a5 (origin/story/fox-and-grapes) Added fox-and-grapes story
* | b6f683e Merge branch 'story/frogs-and-ox'
* | e66aacf Fix typo in story title
* | af2daf1 Completed frogs-and-ox story
|/
* 6c69be1 Initial commit
```

**Success Indicators**:
- `fox-and-grapes.txt` appears in `master`.
- Merge commit reflects the PR integration.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh max@ststor01
cd ~/story-blog/

# Verify repository state
git branch
git log --oneline --all --graph
ls

# Switch to master and verify contents
git checkout master
ls

# Post-merge verification
git pull origin master
ls
git log --oneline --all --graph
```

---

## 💡 What is a Pull Request?

**Pull Request Definition**: A Pull Request (PR) is a method to propose and review changes before merging them into a target branch (e.g., `master`). It facilitates collaboration, code review, and quality control.

### **Key Benefits**:
- **Code Review**: Ensures changes are validated by team members.
- **Controlled Merging**: Prevents direct pushes to critical branches.
- **Collaboration**: Allows discussion and feedback on changes.
- **Traceability**: Maintains a record of changes and approvals.

### **How Pull Requests Work**:
1. **Create PR**: Propose changes from a source branch to a target branch.
2. **Assign Reviewers**: Team members review the proposed changes.
3. **Discuss and Revise**: Address feedback and make necessary updates.
4. **Merge**: Approved changes are merged into the target branch.
5. **Update Remote**: Merged changes are reflected in the remote repository.

### **Common Use Cases**:
- **Feature Integration**: Add new features after review.
- **Bug Fixes**: Validate fixes before merging.
- **Team Collaboration**: Coordinate changes across distributed teams.
- **Quality Assurance**: Ensure code meets standards before merging.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: PR Creation Fails**
**Symptoms**: Unable to create PR due to branch conflicts or permissions.
**Solution**: Ensure branches are up-to-date and user has correct permissions.
```bash
git fetch origin
git checkout story/fox-and-grapes
git pull origin story/fox-and-grapes
```

### **Issue 2: Reviewer Not Notified**
**Symptoms**: Tom does not receive PR review notification.
**Solution**: Verify Tom is correctly assigned in the Gitea UI and reassign if needed.

### **Issue 3: Merge Conflicts**
**Symptoms**: PR cannot be merged due to conflicts.
**Solution**: Resolve conflicts locally, push updates, and retry merge.
```bash
git checkout story/fox-and-grapes
git merge master
# Resolve conflicts in files
git add <resolved-files>
git commit
git push origin story/fox-and-grapes
```

### **Issue 4: Merge Button Disabled**
**Symptoms**: Merge button is grayed out in Gitea UI.
**Solution**: Ensure Tom has reviewed and approved the PR, and all required checks (if any) are complete.

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **ensuring controlled integration of Max’s story** into the `master` branch while adhering to the review process to maintain code quality and prevent direct pushes.

**💡 Solution Approach:**

1. **Repository Verification**: Confirmed the repository contents and commit history to validate Max’s and Sarah’s contributions.
2. **Branch Analysis**: Verified `story/fox-and-grapes` contains Max’s story and `master` does not.
3. **PR Creation**: Used Gitea UI to create a PR from `story/fox-and-grapes` to `master`.
4. **Reviewer Assignment**: Assigned Tom as the reviewer to ensure proper validation.
5. **Review and Merge**: Logged in as Tom to review and merge the PR, ensuring the story is added to `master`.
6. **Post-Merge Verification**: Confirmed the merge by checking the `master` branch contents and history.

**🎯 Key Success Factors:**
- **Controlled Workflow**: Used PR to prevent direct `master` pushes.
- **Accurate Verification**: Validated commits and file contents before and after merging.
- **Reviewer Assignment**: Ensured Tom was correctly assigned for review.
- **UI Navigation**: Correctly used Gitea UI for PR creation and merging.
- **Team Collaboration**: Facilitated review and approval process for quality control.

**⚠️ Critical Operation Details:**
- **Branch Protection**: Direct pushes to `master` prohibited to maintain integrity.
- **Commit Verification**: Ensured Max’s commit (`1ea22a5`) was correctly targeted.
- **Reviewer Role**: Tom’s approval was required before merging.
- **UI-Based Workflow**: PR creation and merging performed via Gitea UI.
- **Post-Merge Sync**: Ensured `master` was updated remotely and locally.

**🔒 Pull Request Benefits:**
- **Quality Control**: Enforced review before merging to `master`.
- **Collaboration**: Enabled team feedback via reviewer assignment.
- **History Preservation**: Maintained clear commit history with merge details.
- **Traceability**: Provided audit trail for changes and approvals.

---

## ⚠️ Important Production Notes

🔧 **Controlled Integration**: PRs ensure only reviewed changes reach `master`.

🔐 **History Management**: Merge commits maintain clear change history.

📊 **Team Collaboration**: Reviewer assignment ensures team validation.

🛡️ **Change Validation**: PR process isolates and verifies changes before merging.