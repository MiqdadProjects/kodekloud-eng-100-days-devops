# 🌟 Task 34 - Merge Feature Branch and Create Post-Update Hook for Release Tag

**📌 Task Description**

The **Nautilus application development team** is working on a Git repository located at `/opt/demo.git`, which is cloned under `/usr/src/kodekloudrepos/demo` on the **Storage server** in **Stratos DC**. The team needs to merge the `feature` branch into the `master` branch and set up a `post-update` hook to automatically create a release tag named `release-YYYY-MM-DD` (using the current date) whenever changes are pushed to the `master` branch. For example, if today is September 29, 2025, the tag should be `release-2025-09-29`. The hook must be tested, and the changes must be pushed to the remote repository.

**👉 Task Requirements:**
- Merge the `feature` branch into the `master` branch.
- Create a `post-update` hook to create a release tag with the current date.
- Test the hook to ensure it creates the tag.
- Push the changes to the remote repository.
- Use the `natasha` user for all operations.
- Do not alter existing repository or directory permissions.

**💡 Note:** The current date is September 29, 2025. All commands are executed as the `natasha` user with `sudo` where necessary, ensuring no permissions are modified unnecessarily.

---

## 🔹 Step 1: Connect to Storage Server

```bash
ssh natasha@ststor01

```

**Purpose**: Connect to the storage server (`ststor01`) as the `natasha` user and switch to `root` for administrative access to perform Git operations.

---

## 🔹 Step 2: Navigate to Repository Directory

```bash
cd /usr/src/kodekloudrepos/demo/
```

**Purpose**: Navigate to the cloned Git repository where the merge and hook setup will be performed.

**Verify Location**:
```bash
pwd
ls -la
```

**Expected Output** (example):
```
/usr/src/kodekloudrepos/demo
.git  info.txt  welcome.txt
```

---

## 🔹 Step 3: Check Existing Branches

```bash
sudo git branch
sudo git branch -a
```

**Purpose**: List all available branches to confirm the presence of `feature` and `master` branches.

**Expected Output**:
```
  feature
* master
  remotes/origin/feature
  remotes/origin/master
```

**Branch Information**:
- **Current branch**: Indicated by asterisk (*), e.g., `master`
- **Local branches**: `feature`, `master`
- **Remote branches**: `origin/feature`, `origin/master`

---

## 🔹 Step 4: Merge Feature Branch into Master

```bash
sudo git checkout master
sudo git merge feature
```

**Purpose**: Switch to the `master` branch and merge the `feature` branch into it.

**Expected Output** (example):
```
Updating b2b709a..2aaf343
Fast-forward
 info.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

**Merge Details**:
- If the merge is a fast-forward, it updates `master` to include all commits from `feature`.
- If conflicts occur, resolve them manually (see troubleshooting section).

**Verify Merge**:
```bash
git log --oneline
```

**Expected Output** (example):
```
2aaf343 (HEAD -> master, feature) Update info.txt
b2b709a (origin/master) Add welcome.txt
6813f47 initial commit
```

---

## 🔹 Step 5: Create Post-Update Hook

```bash
sudo vi /opt/demo.git/hooks/post-update
```

**Purpose**: Create a `post-update` hook in the bare repository (`/opt/demo.git`) to automatically create a release tag when changes are pushed to the `master` branch.

**Hook Script Content**:
```bash
#!/bin/sh

if [ "$1" = "refs/heads/master" ]; then
  echo "Creating release tag"
  git tag release-`date '+%Y-%m-%d'` master
fi
```

**Explanation**:
- The `post-update` hook runs after a push to the remote repository.
- The `if` condition checks if the pushed reference is `refs/heads/master` (the `master` branch).
- If true, it creates a tag named `release-YYYY-MM-DD` based on the current date and applies it to the `master` branch.

**Save and Exit**:
- Save the file in `vi` using `:wq`.

---

## 🔹 Step 6: Set Execute Permissions for Hook

```bash
sudo chmod +x /opt/demo.git/hooks/post-update  or 
sudo chmod 777 /opt/demo.git/hooks/post-update
```

**Purpose**: Ensure the `post-update` hook is executable without altering other permissions.

**Note**: Using `chmod +x` adds execute permissions for all users (owner, group, others) while preserving existing read/write permissions, avoiding the use of `chmod 777` to maintain security.

**Verify Permissions**:
```bash
ls -l /opt/demo.git/hooks/post-update
```

**Expected Output** (example):
```
-rwxr-xr-x 1 root root 123 Sep 29 17:42 post-update
```

---

## 🔹 Step 7: Push Changes to Remote Repository

```bash
sudo git push origin master
```

**Purpose**: Push the merged changes from the `master` branch to the remote repository (`/opt/demo.git`), triggering the `post-update` hook.

**Expected Output** (example):
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
...
To /opt/demo.git
   b2b709a..2aaf343  master -> master
Creating release tag
```

**Hook Execution**:
- The `post-update` hook runs automatically, creating a tag like `release-2025-09-29`.

---

## 🔹 Step 8: Verify Release Tag Creation

```bash
sudo git fetch --all
sudo git tag
```

**Purpose**: Fetch all updates from the remote repository and list tags to confirm the creation of the release tag.

**Expected Output**:
```
Fetching origin
From /opt/demo
 * [new tag]         release-2025-09-29 -> release-2025-09-29
release-2025-09-29
```

**Verification**:
- The tag `release-2025-09-29` should appear, confirming the hook worked as intended.

**Additional Check**:
```bash
git log --oneline --all --graph
```

**Expected Output** (example):
```
* 2aaf343 (HEAD -> master, origin/master, tag: release-2025-09-29, feature) Update info.txt
* b2b709a Add welcome.txt
* 6813f47 initial commit
```

**Success Indicators**:
- The `master` branch includes the merged commits.
- The `release-2025-09-29` tag points to the latest commit on `master`.

---

## 🔹 Step 9: Validate File Changes (Optional)

```bash
cat info.txt
```

**Purpose**: Verify that the file changes from the `feature` branch merge were applied correctly to `master`.

**Expected Output** (example):
```
Updated content from feature branch
```

---

## 📋 Quick Command Reference (REMEMBER TO USE SUDO)

For quick execution on **Storage Server (ststor01)**:

```bash
# Connect and navigate
ssh natasha@ststor01
sudo su -
cd /usr/src/kodekloudrepos/demo/

# Verify branches and merge
git branch -a
git checkout master
git merge feature

# Create and configure post-update hook
sudo vi /opt/demo.git/hooks/post-update
# Add the following content:
#!/bin/sh
if [ "$1" = "refs/heads/master" ]; then
  echo "Creating release tag"
  git tag release-`date '+%Y-%m-%d'` master
fi

# Set execute permissions
sudo chmod +x /opt/demo.git/hooks/post-update

# Push changes and verify tag
git push origin master
git fetch --all
git tag
git log --oneline --all --graph
```

---

## 💡 What is a Git Post-Update Hook?

**Post-Update Hook Definition**: A `post-update` hook is a Git server-side hook that runs after a push operation updates references in the remote repository. It’s commonly used for automation tasks like notifications, deployments, or tagging.

### **Key Benefits**:
- **Automation**: Automatically perform actions after a push, like creating tags.
- **Consistency**: Ensures release tags are created for every push to `master`.
- **Team Collaboration**: Tags provide clear markers for releases, aiding team workflows.
- **Traceability**: Release tags link to specific commits for version tracking.

### **How Post-Update Hook Works**:
1. **Trigger**: Executes after a `git push` updates refs (e.g., `refs/heads/master`).
2. **Parameters**: Receives updated ref names as arguments (e.g., `refs/heads/master`).
3. **Script Execution**: Runs the script in the bare repository (`/opt/demo.git/hooks/post-update`).
4. **Tag Creation**: In this case, creates a tag with the current date (`release-YYYY-MM-DD`).

### **Common Use Cases**:
- **Release Tagging**: Mark stable commits with version or date-based tags.
- **Notifications**: Send alerts to team members or CI/CD systems.
- **Deployment Triggers**: Initiate automated deployments after pushes.
- **Audit Logging**: Record push events for tracking.

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Merge Conflicts**
**Symptoms**: Merge fails due to conflicting changes.
**Solution**: Resolve conflicts manually and complete the merge.
```bash
git merge feature
# Edit conflicted files
git add <resolved-files>
git commit
```

### **Issue 2: Hook Not Executing**
**Symptoms**: No tag created after push.
**Solution**: Verify hook permissions and script content.
```bash
ls -l /opt/demo.git/hooks/post-update
sudo chmod +x /opt/demo.git/hooks/post-update
cat /opt/demo.git/hooks/post-update
```

### **Issue 3: Incorrect Tag Name**
**Symptoms**: Tag created with wrong date format.
**Solution**: Check the `date` command format in the hook script.
```bash
date '+%Y-%m-%d'  # Should output: 2025-09-29
```

### **Issue 4: Push Rejected**
**Symptoms**: Push fails due to remote changes.
**Solution**: Pull latest changes and retry.
```bash
git pull origin master
git push origin master
```

### **Issue 5: Hook Permission Issues**
**Symptoms**: Hook fails to execute due to insufficient permissions.
**Solution**: Ensure the hook is executable without altering other permissions.
```bash
sudo chmod +x /opt/demo.git/hooks/post-update
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **merging the feature branch and setting up an automated post-update hook** to create a release tag with the current date upon pushing to the `master` branch, while ensuring existing permissions remain unchanged.

**💡 Solution Approach:**

1. **Repository Navigation**: Accessed the cloned repository at `/usr/src/kodekloudrepos/demo/`.
2. **Branch Verification**: Confirmed the presence of `feature` and `master` branches.
3. **Merge Execution**: Merged the `feature` branch into `master` to incorporate changes.
4. **Hook Creation**: Created a `post-update` hook in the bare repository (`/opt/demo.git/hooks/`) to generate a date-based release tag.
5. **Permission Management**: Set execute permissions on the hook using `chmod +x` to avoid altering other permissions.
6. **Push and Test**: Pushed changes to trigger the hook and verified the tag creation.
7. **Validation**: Confirmed the tag (`release-2025-09-29`) and commit history.

**🎯 Key Success Factors:**
- **Accurate Merge**: Ensured `feature` branch changes were correctly merged into `master`.
- **Hook Logic**: Correctly implemented the `post-update` hook to trigger only for `master` branch pushes.
- **Date Formatting**: Used `date '+%Y-%m-%d'` for consistent tag naming.
- **Permission Preservation**: Used `chmod +x` instead of `chmod 777` to maintain security.
- **Tag Verification**: Validated tag creation using `git fetch` and `git tag`.

**⚠️ Critical Operation Details:**
- **Merge Accuracy**: Ensured no conflicts during the merge process.
- **Hook Placement**: Script placed in `/opt/demo.git/hooks/post-update` (bare repository).
- **Tag Naming**: Tag format `release-YYYY-MM-DD` dynamically generated.
- **Permission Care**: Avoided altering existing repository permissions.
- **Push Trigger**: Hook executes only on `master` branch pushes.

**🔒 Post-Update Hook Benefits:**
- **Automation**: Eliminates manual tagging for releases.
- **Consistency**: Ensures every `master` push has a corresponding release tag.
- **Traceability**: Tags provide clear reference points for deployments.
- **Minimal Impact**: Preserves repository permissions and structure.

---

## ⚠️ Important Production Notes

🔧 **Automated Tagging**: The `post-update` hook ensures consistent release tagging without manual intervention.

🔐 **Permission Security**: Using `chmod +x` maintains existing permissions, enhancing security.

📊 **Team Collaboration**: Release tags make it easier for the team to track and deploy specific versions.

🛡️ **Change Validation**: The hook only triggers for `master` branch pushes, avoiding unnecessary tags.