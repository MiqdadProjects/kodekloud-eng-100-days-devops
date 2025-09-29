# 🌟 Task 37 - Copy Encrypted File to Ubuntu Container

**📌 Task Description**

The **Nautilus DevOps team** possesses confidential data on **App Server 1** in the **Stratos Datacenter**. A container named `ubuntu_latest` is running on this server. The task requires copying an encrypted file `/tmp/nautilus.txt.gpg` from the Docker host to the `/opt/` directory inside the `ubuntu_latest` container, ensuring the file is not modified during the operation.

**👉 Task Requirements:**
- Copy the encrypted file `/tmp/nautilus.txt.gpg` from the Docker host (App Server 1) to the `/opt/` directory in the `ubuntu_latest` container.
- Ensure the file is not modified during the copy operation.
- Perform the task on App Server 1 using appropriate user permissions.

**💡 Note:** The task is performed on **App Server 1** (assumed to be `stapp01` based on common Nautilus infrastructure naming). The container `ubuntu_latest` is already running. All commands are executed with the `tony` user, using `sudo` if required. The current date and time is September 29, 2025, 08:52 PM PKT.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
sudo su -
```

**Purpose**: Connect to App Server 1 (`stapp01`) as the `tony` user and switch to `root` for administrative access to perform Docker operations.

**Note**: Root access ensures sufficient permissions for Docker commands and file operations.

---

## 🔹 Step 2: Verify Docker Container

```bash
docker ps
```

**Purpose**: Confirm that the `ubuntu_latest` container is running on App Server 1.

**Expected Output** (example):
```
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
11074b50833e   ubuntu    "/bin/bash"   2 minutes ago   Up 2 minutes             ubuntu_latest
```

**Success Indicators**:
- **Container Name**: `ubuntu_latest` is listed.
- **Status**: `Up` indicates the container is running.
- **Image**: `ubuntu` confirms the base image used.

**Note**: If the container is not running, check stopped containers with `docker ps -a` and start it:
```bash
docker start ubuntu_latest
```

---

## 🔹 Step 3: Verify Source File

```bash
cat /tmp/nautilus.txt.gpg
```

**Purpose**: Inspect the encrypted file `/tmp/nautilus.txt.gpg` on the Docker host to confirm its existence and content (for later comparison).

**Expected Output** (example):
```
?n$mXU8S[T0P 'I1±T,5ǷS\p,#CE.,cy!rJZ5upLx
```

**Note**: The output appears as binary/encrypted data, typical for `.gpg` files. This step helps establish a baseline for verifying the file’s integrity after copying.

---

## 🔹 Step 4: Copy File to Container

```bash
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/opt/
```

**Purpose**: Copy the encrypted file `/tmp/nautilus.txt.gpg` from the Docker host to the `/opt/` directory inside the `ubuntu_latest` container.

**Command Breakdown**:
- `docker cp`: Copies files between the Docker host and a container.
- `/tmp/nautilus.txt.gpg`: Source file path on the host.
- `ubuntu_latest:/opt/`: Destination path inside the container (`/opt/` directory).

**Expected Output**: No output if successful, indicating the file was copied.

---

## 🔹 Step 5: Verify File in Container

```bash
docker exec -it ubuntu_latest /bin/sh
# cd /opt/  chanage directory to /opt/
# ls
# nautilus.txt.gpg  This file must be present inside container 
exit
```

**Purpose**: Access the `ubuntu_latest` container, verify the presence of the copied file, and confirm its contents match the source file to ensure no modification occurred.

**Command Breakdown**:
- `docker exec -it ubuntu_latest /bin/sh`: Opens an interactive shell in the container.
- `cd /opt/`: Navigate to the `/opt/` directory.
- `ls`: List files to confirm `nautilus.txt.gpg` exists.
- `cat nautilus.txt.gpg`: Display the file contents for comparison.
- `exit`: Exit the container shell.

**Expected Output** (example):
```
# cd /opt/
# ls
nautilus.txt.gpg
# cat nautilus.txt.gpg
?n$mXU8S[T0P 'I1±T,5ǷS\p,#CE.,cy!rJZ5upLx
# exit
```

**Success Indicators**:
- **File Presence**: `nautilus.txt.gpg` appears in `/opt/`.
- **File Integrity**: The content matches the source file’s output (e.g., `?n$mXU8S[T0P 'I1±T,5ǷS\p,#CE.,cy!rJZ5upLx`).

**Alternative Verification** (optional):
```bash
docker exec ubuntu_latest ls /opt/
docker exec ubuntu_latest cat /opt/nautilus.txt.gpg
```

**Expected Output** (example):
```
nautilus.txt.gpg
?n$mXU8S[T0P 'I1±T,5ǷS\p,#CE.,cy!rJZ5upLx
```

---

## 📋 Quick Command Reference

For quick execution on **App Server 1 (stapp01)**:

```bash
# Connect to server
ssh tony@stapp01
sudo su -

# Verify container
docker ps

# Verify source file
cat /tmp/nautilus.txt.gpg

# Copy file to container
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/opt/

# Verify file in container
docker exec -it ubuntu_latest /bin/sh
cd /opt/
ls
cat nautilus.txt.gpg
exit
```

---


---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Container Not Running**
**Symptoms**: `docker ps` shows no `ubuntu_latest`, but `docker ps -a` shows it as `Exited`.
**Solution**: Start the container and retry.
```bash
docker start ubuntu_latest
docker ps
```

### **Issue 2: File Not Found on Host**
**Symptoms**: Error like `Error: No such file or directory: /tmp/nautilus.txt.gpg`.
**Solution**: Verify the file exists on the host.
```bash
ls -l /tmp/nautilus.txt.gpg
```

### **Issue 3: Permission Denied**
**Symptoms**: Error like `docker: Got permission denied while trying to connect to the Docker daemon socket`.
**Solution**: Use `sudo` or ensure the user is in the `docker` group.
```bash
sudo docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/opt/
```

### **Issue 4: File Not Copied to Container**
**Symptoms**: `ls /opt/` in the container shows no `nautilus.txt.gpg`.
**Solution**: Verify the container name and destination path, then retry.
```bash
docker ps
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/opt/
docker exec ubuntu_latest ls /opt/
```

### **Issue 5: File Content Modified**
**Symptoms**: `cat /opt/nautilus.txt.gpg` in the container shows different content.
**Solution**: Recopy the file and verify integrity using checksums (if available).
```bash
# On host
md5sum /tmp/nautilus.txt.gpg
# In container
docker exec ubuntu_latest md5sum /opt/nautilus.txt.gpg
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **copying an encrypted file** from the Docker host to the `ubuntu_latest` container’s `/opt/` directory while ensuring the file’s content remains unchanged during the transfer.

**💡 Solution Approach:**

1. **Server Access**: Connected to App Server 1 (`stapp01`) as the `tony` user with `sudo`.
2. **Container Verification**: Confirmed the `ubuntu_latest` container is running using `docker ps`.
3. **Source File Check**: Verified the existence and content of `/tmp/nautilus.txt.gpg` on the host.
4. **File Copy**: Used `docker cp` to transfer the file to `/opt/` in the container.
5. **Integrity Validation**: Accessed the container to confirm the file’s presence and content match the source.

**🎯 Key Success Factors:**
- **Correct Container**: Targeted the `ubuntu_latest` container using its name.
- **Accurate Path**: Copied to `/opt/` as specified.
- **File Integrity**: Verified the copied file’s content matches the source using `cat`.
- **Simple Execution**: Used `docker cp` for a direct, unmodified transfer.
- **Verification**: Confirmed the file’s presence and content in the container.

**⚠️ Critical Operation Details:**
- **File Path**: Source is `/tmp/nautilus.txt.gpg` on the host, destination is `/opt/` in the container.
- **Container Name**: Must be `ubuntu_latest`.
- **Integrity Check**: File content must remain unchanged (e.g., same encrypted output).
- **User Permissions**: Used `tony` with `sudo` for Docker operations.

**🔒 Docker `cp` Benefits:**
- **Reliable Transfer**: Ensures encrypted files are copied without alteration.
- **Ease of Use**: Single command handles the operation.
- **Container Isolation**: Maintains container integrity while adding data.
- **Quick Validation**: Easy to verify file presence and content.

---

## ⚠️ Important Production Notes

🔧 **Secure Data Handling**: The encrypted `.gpg` file ensures confidentiality during transfer.

🔐 **Integrity Focus**: Verifying file content post-copy ensures no corruption occurred.

📊 **Team Collaboration**: The copied file is available in the container for further processing by the DevOps team.

🛡️ **Minimal Impact**: The operation does not modify the container’s running state or other files.