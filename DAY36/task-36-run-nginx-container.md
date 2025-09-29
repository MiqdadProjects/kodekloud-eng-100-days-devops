# 🌟 Task 36 - Deploy Nginx Container on Application Server 1

**📌 Task Description**

The **Nautilus DevOps team** is conducting application deployment tests on selected application servers. They require a Nginx container deployment on **Application Server 1** in **Stratos DC**. The task involves creating a container named `nginx_1` using the `nginx` image with the `alpine` tag, ensuring the container is in a running state.

**👉 Task Requirements:**
- Create a container named `nginx_1` on Application Server 1.
- Use the `nginx:alpine` image.
- Ensure the container is in a running state.

**💡 Note:** The task is performed on **Application Server 1** (assumed to be `stapp01` based on common Nautilus infrastructure naming). All commands are executed with appropriate user permissions (e.g., `tony` with `sudo` if required). The current date is September 29, 2025.

---

## 🔹 Step 1: Connect to Application Server 1

```bash
ssh tony@stapp01
```

**Purpose**: Connect to Application Server 1 (`stapp01`) as the `tony` user to perform the container deployment.

**Note**: If root access is required for Docker operations, use `sudo` or switch to root:
```bash
sudo su -
```

---

## 🔹 Step 2: Verify Docker Installation

```bash
docker --version
```

**Purpose**: Confirm that Docker is installed and accessible on Application Server 1.

**Expected Output** (example):
```
Docker version 20.10.12, build e91ed57
```

**Note**: If Docker is not installed, contact the system administrator or install it (not covered here as it’s assumed to be pre-installed).

---

## 🔹 Step 3: Pull Nginx Alpine Image (Optional)

```bash
docker pull nginx:alpine
```

**Purpose**: Ensure the `nginx:alpine` image is available locally. This step is optional as `docker run` automatically pulls the image if it’s not present.

**Expected Output** (example):
```
alpine: Pulling from library/nginx
Digest: sha256:1e89e8e66d6887d56a5e7b0b1e9b5c1e5e4b8d1f8b4c8d1f8b4c8d1f8b4c8d1
Status: Image is up to date for nginx:alpine
```

---

## 🔹 Step 4: Create and Run Nginx Container

```bash
docker run -d --name nginx_1 nginx:alpine
```

**Purpose**: Create and start a container named `nginx_1` using the `nginx:alpine` image in detached mode (`-d`) to ensure it runs in the background.

**Command Breakdown**:
- `docker run`: Creates and starts a new container.
- `-d`: Runs the container in detached mode (background).
- `--name nginx_1`: Assigns the name `nginx_1` to the container.
- `nginx:alpine`: Specifies the image and tag to use.

**Expected Output** (example):
```
2a4b63771156
```

**Note**: The output is the container ID, indicating successful creation and start.

---

## 🔹 Step 5: Verify Container Status

```bash
docker ps
```

**Purpose**: Confirm that the `nginx_1` container is running.

**Expected Output** (example):
```
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
2a4b63771156   nginx:alpine   "/docker-entrypoint.…"   27 seconds ago   Up 26 seconds   80/tcp    nginx_1
```

**Success Indicators**:
- **Container Name**: `nginx_1` is listed.
- **Image**: `nginx:alpine` is used.
- **Status**: `Up` indicates the container is running.
- **Ports**: `80/tcp` shows Nginx’s default port is available.

**Additional Check** (optional):
```bash
docker ps -a
```

**Purpose**: List all containers (running and stopped) to ensure no naming conflicts or unexpected stopped containers.

**Expected Output** (example):
```
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
2a4b63771156   nginx:alpine   "/docker-entrypoint.…"   27 seconds ago   Up 26 seconds   80/tcp    nginx_1
```

---

## 🔹 Step 6: Inspect Container (Optional)

```bash
docker inspect nginx_1
```

**Purpose**: Retrieve detailed information about the `nginx_1` container to verify its configuration, such as image, name, and running state.

**Expected Output** (partial example):
```json
[
    {
        "Id": "2a4b63771156",
        "Name": "/nginx_1",
        "State": {
            "Status": "running",
            "Running": true
        },
        "Config": {
            "Image": "nginx:alpine"
        }
    }
]
```

**Verification**: Confirm the container is running and using the correct image.

---

## 📋 Quick Command Reference

For quick execution on **Application Server 1 (stapp01)**:

```bash
# Connect to server
ssh tony@stapp01
sudo su -

# Verify Docker
docker --version

# Pull image (optional)
docker pull nginx:alpine

# Run container
docker run -d --name nginx_1 nginx:alpine

# Verify container status
docker ps
```

---




---

## 🔧 Troubleshooting Common Issues

### **Issue 1: Docker Not Installed**
**Symptoms**: `docker: command not found`.
**Solution**: Verify Docker installation or contact the system administrator.
```bash
docker --version
```

### **Issue 2: Container Name Already in Use**
**Symptoms**: Error like `docker: Error response from daemon: Conflict. The container name "/nginx_1" is already in use`.
**Solution**: Remove the existing container and retry.
```bash
docker rm -f nginx_1
docker run -d --name nginx_1 nginx:alpine
```

### **Issue 3: Container Not Running**
**Symptoms**: `docker ps` shows no running container, but `docker ps -a` shows `nginx_1` as `Exited`.
**Solution**: Check container logs for errors and restart.
```bash
docker logs nginx_1
docker start nginx_1
```

### **Issue 4: Image Pull Failure**
**Symptoms**: Error like `docker: Error response from daemon: pull access denied for nginx:alpine`.
**Solution**: Ensure internet connectivity and correct image name.
```bash
docker pull nginx:alpine
```

### **Issue 5: Permission Denied**
**Symptoms**: `docker: Got permission denied while trying to connect to the Docker daemon socket`.
**Solution**: Use `sudo` or ensure the user is in the `docker` group.
```bash
sudo docker run -d --name nginx_1 nginx:alpine
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **deploying a named Nginx container** using the `nginx:alpine` image on Application Server 1 and ensuring it remains in a running state with minimal configuration.

**💡 Solution Approach:**

1. **Server Access**: Connected to Application Server 1 (`stapp01`) as the `tony` user.
2. **Docker Verification**: Confirmed Docker is installed and accessible.
3. **Container Deployment**: Used `docker run -d --name nginx_1 nginx:alpine` to create and start the container.
4. **Status Validation**: Verified the container is running using `docker ps`.
5. **Minimal Configuration**: Ensured the task requirements were met without additional configurations (e.g., port mapping), as not specified.

**🎯 Key Success Factors:**
- **Correct Image**: Used `nginx:alpine` for a lightweight deployment.
- **Named Container**: Assigned the name `nginx_1` as required.
- **Running State**: Ensured the container runs in detached mode (`-d`).
- **Simple Execution**: Avoided unnecessary configurations to meet the task’s scope.
- **Verification**: Confirmed the container’s status with `docker ps`.

**⚠️ Critical Operation Details:**
- **Container Name**: Must be exactly `nginx_1`.
- **Image Tag**: Must use `nginx:alpine` for lightweight deployment.
- **Running State**: Container must be `Up` in `docker ps` output.
- **User Permissions**: Used `tony` with `sudo` if needed, assuming standard Nautilus setup.

**🔒 Nginx Container Benefits:**
- **Lightweight Deployment**: `nginx:alpine` minimizes resource usage.
- **Quick Setup**: Single command deploys a functional web server.
- **Isolation**: Containerized environment prevents conflicts.
- **Reproducibility**: Consistent deployment across servers.

---

## ⚠️ Important Production Notes

🔧 **Minimal Configuration**: The task requires only a running container, making it suitable for quick tests.

🔐 **Security**: Using `nginx:alpine` reduces the attack surface due to its small size.

📊 **Team Collaboration**: The container can be used for further testing or integration by the DevOps team.

🛡️ **Validation**: `docker ps` ensures the container is operational, meeting deployment requirements.