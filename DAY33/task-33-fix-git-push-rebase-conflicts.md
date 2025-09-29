# 🌟 Task 33 - Resolve Merge Conflict and Fix Typo in Story Index

## 📌 Task Description

The **Nautilus application development team** is collaborating on the `story-blog` repository located in `/home/max/story-blog` on the **Storage server** in **Stratos DC**. Sarah and Max have been working on writing stories, with Max recently adding changes to the repository. However, Max is encountering issues when trying to push his changes due to a merge conflict in `story-index.txt`. Additionally, there's a typo in the story title "The Lion and the Mooose" that needs to be corrected to "The Lion and the Mouse." The `story-index.txt` file must include titles for all 4 stories. The task requires resolving the merge conflict, fixing the typo, committing the changes, and verifying them in the Gitea UI.

👉 **Your task**: SSH into the storage server as Max, fix the typo in `story-index.txt`, resolve the merge conflict during the push operation, and verify the changes in the Gitea UI.

💡 **Note**: Take screenshots of the Gitea UI verification (e.g., `story-index.txt` content) or record the process using loom.com for review if the task is marked incomplete.

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

## 🔹 Step 2: Navigate to Repository Directory

```bash
cd ~/story-blog/
```

**Purpose**: Navigate to the cloned repository in Max's home directory.

**Verify location and contents**:
```bash
pwd
ls -la
```

**Expected Output**:
```
/home/max/story-blog
fox-and-grapes.txt  frogs-and-ox.txt  lion-and-mouse.txt  story-index.txt
```

---

## 🔹 Step 3: Check Repository Status and History

```bash
git branch
git status
git log --oneline
```

**Purpose**: Confirm the current branch and check for any pending changes.

**Expected Output**:
```
* master
On branch master
Your branch is up to date with 'origin/master'.
nothing to commit, working tree clean
```

**Commit History Example**:
```
f181ac6 (HEAD -> master) Previous commit
...
```

**Current State**: Repository is clean, but remote changes exist that will cause conflicts.

---

## 🔹 Step 4: Edit Story Index File

```bash
vi story-index.txt
```

**Purpose**: Fix the typo in the story title from "The Lion and the Mooose" to "The Lion and the Mouse."

**Before Edit Content**:
```
1. The Lion and the Mooose
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
```

**After Edit Content**:
```
1. The Lion and the Mouse
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
```

**Save the file**: Press `Esc`, then type `:wq` and press `Enter`.

**Verify changes**:
```bash
cat story-index.txt
```

**Expected Output**:
```
1. The Lion and the Mouse
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
```

---

## 🔹 Step 5: Stage and Commit Changes

```bash
git add story-index.txt
git commit -m "Fixed typo error in story index"
```

**Purpose**: Stage the corrected `story-index.txt` and commit the typo fix.

**Expected Output**:
```
[master <commit-hash>] Fixed typo error in story index
 1 file changed, 1 insertion(+), 1 deletion(-)
```

**Commit Effects**:
- Creates a new commit with the typo correction.
- Prepares to push changes to the remote repository.

---

## 🔹 Step 6: Attempt to Push Changes

```bash
git push origin master
```

**Purpose**: Push the committed changes to the remote `master` branch.

**Expected Output** (Error):
```
Username for 'http://git.stratos.xfusioncorp.com': max
Password for 'http://max@git.stratos.xfusioncorp.com': 
To http://git.stratos.xfusioncorp.com/sarah/story-blog.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'http://git.stratos.xfusioncorp.com/sarah/story-blog.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

**Issue**: Remote repository has new commits (e.g., from Sarah) that are not in the local repository.

---

## 🔹 Step 7: Pull Remote Changes with Rebase

```bash
git pull --rebase origin master
```

**Purpose**: Fetch remote changes and rebase local commits on top of them to avoid merge commits.

**Expected Output**:
```
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From http://git.stratos.xfusioncorp.com/sarah/story-blog
   f181ac6..528de86  master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: Fixed typo error in story index
Applying: Added the fox and grapes story
Using index info to reconstruct a base tree...
Falling back to patching base and 3-way merge...
Auto-merging story-index.txt
CONFLICT (add/add): Merge conflict in story-index.txt
error: Failed to merge in the changes.
Patch failed at 0001 Added the fox and grapes story
The copy of the patch that failed is found in: .git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
```

**Rebase Effects**:
- Fetches remote changes (e.g., `528de86` commit).
- Replays local commits on top of remote changes.
- Pauses at a conflict in `story-index.txt`.

---

## 🔹 Step 8: Check Rebase Status

```bash
git status
```

**Purpose**: Verify the rebase state and identify conflicted files.

**Expected Output**:
```
rebase in progress; onto 528de86
You are currently rebasing branch 'master' on '528de86'.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
        new file:   fox-and-grapes.txt

Unmerged paths:
  (use "git reset HEAD <file>..." to unstage)
  (use "git add <file>..." to mark resolution)
        both added:      story-index.txt
```

**Conflict State**: `story-index.txt` has a merge conflict that needs manual resolution.

---

## 🔹 Step 9: Resolve Merge Conflict

```bash
vi story-index.txt
```

**Purpose**: Edit the conflicted `story-index.txt` to resolve the merge conflict.

**Conflicted Content (Before Resolution)**:
```
<<<<<<< 528de86ae17250cd82c9ee9bb7fca502c3f4f67c
1. The Lion and the Mouse
2. The Frogs and the Ox
3. The Fox and the Grapes
=======
1. The Lion and the Mooose
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
>>>>>>> Added the fox and grapes story
```

**Resolved Content (After Edit)**:
```
1. The Lion and the Mouse
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
```

**Resolution Steps**:
- Remove conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
- Choose the correct version: Use "The Lion and the Mouse" (typo fixed).
- Include all 4 story titles.
- Save the file: Press `Esc`, then type `:wq` and press `Enter`.

**Verify Resolution**:
```bash
cat story-index.txt
```

**Expected Output**:
```
1. The Lion and the Mouse
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
```

---

## 🔹 Step 10: Stage Resolved Conflict and Continue Rebase

```bash
git add story-index.txt
git rebase --continue
```

**Purpose**: Stage the resolved file and continue the rebase operation.

**Expected Output**:
```
[detached HEAD <new-hash>] Fixed typo error in story index
```

**Rebase Continuation**:
- Applies remaining commits after conflict resolution.
- Completes the rebase process.
- Returns to the `master` branch.

---

## 🔹 Step 11: Verify Repository Status

```bash
git status
```

**Purpose**: Confirm the rebase is complete and the branch is ready to push.

**Expected Output**:
```
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
nothing to commit, working directory clean
```

**Current State**: Local `master` branch includes resolved changes and is ahead of remote.

---

## 🔹 Step 12: Push Resolved Changes

```bash
git push origin master
```

**Purpose**: Push the rebased and conflict-resolved changes to the remote repository.

**Expected Output**:
```
Username for 'http://git.stratos.xfusioncorp.com': max
Password for 'http://max@git.stratos.xfusioncorp.com': 
Counting objects: 4, done.
Delta compression using up to 16 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 884 bytes | 0 bytes/s, done.
Total 4 (delta 0), reused 0 (delta 0)
remote: . Processing 1 references
remote: Processed 1 references in total
To http://git.stratos.xfusioncorp.com/sarah/story-blog.git
   528de86..fb7fe02  master -> master
```

**Push Effects**:
- Updates the remote `master` branch with the resolved changes.
- Synchronizes local and remote repositories.

---

## 🔹 Step 13: Verify Changes in Gitea UI

1. **Access Gitea UI**:
   - Click the **Gitea UI** button on the top bar.

2. **Log in as Max**:
   - Username: max
   - Password: Max_pass123

3. **Navigate to Repository**:
   - Go to `sarah/story-blog`.

4. **Verify Story Index**:
   - Click on `story-index.txt`.
   - Confirm the content shows the corrected title: "The Lion and the Mouse."
   - Verify all 4 story titles are present.

    ![screenshot: 1](<GIT CONFLICT (1).png>)
    
    ![screenshot: 2](<GIT CONFLICT (2).png>) 

---

## 🔹 Step 14: Final Verification

```bash
git log --oneline --all --graph
cat story-index.txt
```

**Purpose**: Confirm the commit history and file content locally.

**Expected Output (Log Example)**:
```
* fb7fe02 (HEAD -> master, origin/master) Fixed typo error in story index
* 528de86 Added the fox and grapes story
* f181ac6 Previous commits
```

**File Content**:
```
1. The Lion and the Mouse
2. The Frogs and the Ox
3. The Fox and the Grapes
4. The Donkey and the Dog
```

**Success Indicators**:
- Commit history includes the typo fix and story addition.
- `story-index.txt` has the corrected title and all 4 stories.
- No conflicts remain.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh max@ststor01
cd ~/story-blog/

# Fix typo and commit
vi story-index.txt  # Edit: Mooose -> Mouse
git add story-index.txt
git commit -m "Fixed typo error in story index"

# Pull with rebase (triggers conflict)
git pull --rebase origin master

# Resolve conflict
vi story-index.txt  # Remove markers, keep corrected content
git add story-index.txt
git rebase --continue

# Push resolved changes
git push origin master

# Final verification
git status
cat story-index.txt
git log --oneline
```

---

## 💡 What is Git Rebase with Conflict Resolution?

**Git Rebase with Conflicts Definition**: Git rebase replays local commits on top of remote changes, pausing for manual resolution when conflicts occur between versions of the same file.

### **Key Benefits**:
- **Linear History**: Maintains a clean commit history without merge commits.
- **Controlled Integration**: Allows manual review and correction during conflicts.
- **Change Preservation**: Retains all local and remote changes after resolution.
- **Quality Control**: Ensures typos and errors are fixed during integration.

### **How Git Rebase Conflicts Work**:
1. **Fetch Remote**: Pulls latest changes from `origin/master`.
2. **Replay Commits**: Applies local commits on top of remote base.
3. **Detect Conflicts**: Pauses when changes overlap in the same file.
4. **Manual Resolution**: Edit conflicted files to combine changes.
5. **Continue Rebase**: Stage resolved files and complete the operation.
6. **Push Changes**: Upload the integrated history to remote.

### **Common Use Cases**:
- **Team Collaboration**: Integrate parallel changes from multiple developers.
- **Error Correction**: Fix typos while merging new content.
- **History Cleanup**: Maintain linear history during conflict resolution.
- **Quality Assurance**: Review and validate changes before final push.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Authentication Failure**
**Symptoms**: Invalid username/password during push.
**Solution**: Verify credentials (max/Max_pass123) and retry.
```bash
git push origin master
```

### **Issue 2: Persistent Conflicts**
**Symptoms**: Conflicts reappear after resolution.
**Solution**: Abort rebase, reset, and retry with clean pull.
```bash
git rebase --abort
git reset --hard HEAD~1
git pull --rebase origin master
```

### **Issue 3: Push Still Rejected**
**Symptoms**: Push fails even after rebase.
**Solution**: Force push if history is rewritten (use cautiously).
```bash
git push --force origin master
```

### **Issue 4: Gitea UI Not Updating**
**Symptoms**: Changes not visible in Gitea after push.
**Solution**: Refresh the page and verify push success.
```bash
git log --oneline origin/master
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **resolving a merge conflict** in `story-index.txt` during rebase while simultaneously fixing a typo and ensuring all 4 story titles are correctly listed in the index.

**💡 Solution Approach:**

1. **Repository Navigation**: Located `story-blog` in `/home/max/` and verified contents.
2. **Typo Correction**: Edited `story-index.txt` to fix "Mooose" to "Mouse."
3. **Commit Preparation**: Staged and committed the typo fix locally.
4. **Rebase Initiation**: Used `git pull --rebase` to integrate remote changes, triggering the conflict.
5. **Conflict Resolution**: Manually edited `story-index.txt` to remove markers, retain the corrected title, and include all 4 stories.
6. **Rebase Completion**: Staged the resolved file and continued the rebase.
7. **Remote Synchronization**: Pushed the integrated changes to `origin/master`.
8. **UI Verification**: Confirmed the fix in Gitea UI via screenshot.

**🎯 Key Success Factors:**
- **Accurate Conflict Resolution**: Combined remote and local changes correctly.
- **Typo Fix Integration**: Ensured the correction was preserved during rebase.
- **Complete Index**: Verified all 4 story titles are present.
- **Linear History**: Maintained clean commit history without merge commits.
- **UI Validation**: Used Gitea for visual confirmation.

**⚠️ Critical Operation Details:**
- **Conflict Markers**: Removed `<<<<<<<`, `=======`, `>>>>>>>` from `story-index.txt`.
- **Title Correction**: Changed "Mooose" to "Mouse" in the resolved file.
- **All Stories Included**: Ensured 4 titles: Lion/Mouse, Frogs/Ox, Fox/Grapes, Donkey/Dog.
- **Rebase Strategy**: Used `--rebase` to avoid merge commits.
- **Remote Push**: Updated `sarah/story-blog` with resolved changes.

**🔒 Git Conflict Resolution Benefits:**
- **Change Integration**: Merged parallel edits from Sarah and Max.
- **Error Correction**: Fixed typo during conflict resolution.
- **Collaboration**: Enabled seamless team workflow.
- **Quality Control**: Verified changes in both CLI and UI.

---

## ⚠️ Important Production Notes

🔧 **Manual Resolution**: Always review conflicted files carefully to preserve intended changes.

🔐 **Backup Strategy**: Consider branching before rebasing to preserve original state.

📊 **Team Communication**: Coordinate with collaborators (e.g., Sarah) before force-pushing.

🛡️ **UI Verification**: Use Gitea screenshots to document successful integration.